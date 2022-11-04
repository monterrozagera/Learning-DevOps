# Puppet

- Language to describe the desired state of your infrastructure
- A set of tools to enforce that state
- Designed to be simple to learn and easy to understand
- Configuration management tool (like Ansible, Chef, Salt)
- Less about describing tasks or procedures, more about describing the desired state of your nodes

## Moduling approach

Puppet relies heavily on **abstraction** to make systems more manageable.

- Built-in layer of abstraction
- Files and system users abstracted into "resources"
- Resources can be build together into custom resources types and classes, then bundled together into **modules**

That layer of abstraction means that you can provision a web server without having to define all of the necessary resources (files, packages, users, etc) in order to be configured.

## Profiles and Roles

- **Profiles**: Classes that group together subsets of configuration. (web server settings, database settings)
- **Roles**: Each machine will get assigned one role class (bundles a collection of profiles). It lets you know what a machine's business role is.

### Profiles:

Web:

```
class profile::web {
  include nginx
 }
```

Base:

```
class profile::base {
  user {'admin:
    ensure => present,
  }
}
```

Role:

```
class role::app_server {
  include profile::web
  include profile::base
  include profile::app
}
```


## File Resource

```yaml
file { '/root/README':
	ensure => file,
	content => 'hello world',
}
```

```yaml
service { 'crond':
	ensure =>
running,
	enable => true,
}
```

```yaml
node default {
  file {'/root/README':
    ensure  => file,
    content => 'This is a readme',
    owner   => 'root',
  }
}
```

Trying to declare the same resource on the same machine twice will give you an error.

## Classes

```yaml
class dev_environment {
	user { 'grace':
		ensure 		=> present,
		manage_home 	=> true,
		group 		=> ['wheel'],
	}
	package { 'vim':
		ensure => present,
	}
	file { '/home/grace/.vimrc':
		ensure => file,
		source => 'puppet://modules/dev_environment/vimrc',
	}
}
```

Classes can be nested:

```yaml
class foo {
	include dev_environment
	class { 'another_class':
		ensure => present,
	}
	file { '/some/file.txt':
		ensure 	=> file,
		content => 'some content',
	}
}
```

### Parameters (vars instead of hardcoded values)

```
class minecraft {
  $url = 'https://launcher.mojang.com/mc/game/1.12.2/server/886945bfb2b978778c3a0288fd7fab09d315b25f/server.jar'
  $install_dir = '/opt/minecraft'
  file {$install_dir:
    ensure => directory,
  }
  file {"${install_dir}/server.jar":
    ensure => file,
    source      => $url,
    before => Service['minecraft'],
  }
```


## Forge

`forge.puppet.com`

Add to Puppetfile. Example:

```yaml
mod 'puppet/nginx', '1.0.0'
mod 'puppetlabs/stdlib'
mod 'puppetlabs/concat'
mod 'puppetlabs/translate'
```

PS. Add dependencies as well.

## Connect agent nodes to the master

### Using docker:

`docker exec -it example.puppet.com bash`

`puppet agent -t`

### Certificates

Show list of pending certificates: `puppetserver ca list`

Sign specific CA: `puppetserver ca sign --certname example.puppet.example`

**Sign all pending certificates from master**: `puppetserver ca sign --all`

## Orchestration

* MCollective
* Ansible or SSH in a for loop
* Puppet Bolt

## Facter

Used by Puppet to collect facts about nodes, and figure out what needs to be done on them.

`facter [key]`

Use variables in site.pp

`facter fqdn` => master.puppet.vm

```
node 'master.puppet.vm' {
  include role::master_server
  file { '/root/README':
    ensure => file,
    content => "Welcome to ${fqdn}",
 }
}
```

## Modules

A set of directories that follow a certain pattern. Puppet knows where to find files within a module because of that pattern.

### Manifests

* Puppet code for your module
* One class per manifest
* The class named after the module is in init.pp
* eg: the **nginx** class in the **nginx** module is in manifest/init.pp

**/files**: Static files
**/templates**: Dynamic templates
**/lib**: Additional code
**/task**: Extemporary tasks
**metadata.json**: fills in the details of the module

## Templates

Files must be extension `.epp`

EPP tag: `<%= $variable %>`

```
[Unit]                                                        
Description=Minecraft Server                                  
                                                              
Wants=network.target                                          
After=network.target                                          
                                                              
[Service]                                                     
WorkingDirectory=<%= $install_dir %>                               
ExecStart=/usr/bin/java -Xmx512M -Xms32M -jar server.jar nogui
                                                              
[Install]                                                     
WantedBy=multi-user.target
```

# Configuration Management

* Application of programmatic methods that create consistency in the performance, function, design, and operation of compute resources
* Applied over the life cycle of a system
* Verification of performance through testing
* Use of pre-configured code to accomplish common tasks

## Why?

* Cost avoidance
* Prevention of catastrophic events
* Exponential increase of time savings in the form of reusable code


## CM Template

* A template that contains everything needed for:
  * Launching an instance from a public cloud
  * Installing a database on that instance
  * Making that database available for access by web servers
  * Implementing a unique username and password for this database


## Examples

* A script that defines how to install, configure, and start an Apache Web server or Internet Information Services (ISS) Windows server
* A graphical interface that allows for the provisioning, configuration, and termination of virtual or physical resources

```
package "httpd" do
    action :install
end

file "/var/www/html/index.html" do
    content "Hello world
    action :create
end

service "httpd" do
    action :start
end
```


## Configuring Multiple Servers Identically

* Scripts can be rolled out to thousands of servers
* The end result will be that each server is configured identically

## Utilizing Variables Across Servers

* Variables can be used for customization
* Each configuration management platform has the ability to utilize variables
* These variables allow for one script to employ unique values on each server
* The can be set in the script or, more commonly, in other locations, providing global controls and protability
* Avoiding utilizing hard-coded values 

## Example 2: Variables for a Database

``` ruby
# first we set the variable in a config management variable file.

["database"]["user"] = "myDBuser"
["database"]["user"] = "myDBpasswd"
```

``` ruby
# next, we use the variables when configuring the database.

file 'C:\ProgramData\my.ini' do
    content "
        user=#{['database']}['user']
        password=#
    "
    rights :read, 'Everyone'
end
```

## CM Platforms

* Puppet
* Chef
* RightScale
* Ansible
* Salt
* CFEngine
* DIY

## Idempotence

* If an authorized or accidental change is made to a server, idempotence will bring it back into alignment.
* The policy can be applied at regular intervals with no effect, unless a change is needed to bring the server back into alignment.

## CM and the Cloud

* Provides for large-scale automation of compute resources
* Can use the public cloud's API, allowing for additional programmatic and graphical interfaces to be created in the cloud ecosystem
* Abstract cloud complexity with features provided by CM
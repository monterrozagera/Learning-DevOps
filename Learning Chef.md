
Check before: 

[Configuration Management (CM)](Configuration%20Management.html)

# Learning Chef

* Programmatic Configuration Management Platform (CM)
* Made in Ruby
* Node Automation (Servers, VMS, containers)

## Example:

``` ruby
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

## Infrastructure as Code

Chef platform manages any server component:

* System Resources (Packages, files, services, users, groups, scripts, etc)
  * Represented as code components
  * Infrastructure becomes versionable, testable and repeatable.

## Chef Architecture

**Workstation** Cookbooks => **Chef Server** => => => Node (1,2,3)

## ChefDK

* chef-client
* Ruby
* Ruby_gems
* OpenSSl
* key-value stores
* Parsers
* Libraries
* CMD

## chef-client

* Agent that runs locally on every node under management by Chef
* it will perform all of the steps required to bring the node into the expected state

`sudo chef-client [--local-mode or -z] hello.rb`

## Recipes

There just .rb Ruby files
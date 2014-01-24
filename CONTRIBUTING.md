CONTRIBUTING
===========

# General
* The following document will serve as a guide on what and how to contribute to any cookbook within [rackspace-cookbooks](http://github.com/rackspace-cookbooks/).
* The cookbook name, attribute namespace and git repo should all be the same
 * i.e. `rackspace_yum`, `rackspace_apache`
* The following items will not be supported in the rackspace-cookbooks
 * Compiling from Source in recipes
 * Operating systems outside `ubuntu`,`debian`,`rhel`,`centos`
* We will default to Rackspace provided systems
 * i.e. for ntp, we will default to rackspace ntp servers and fall back to community ones  
* Berkshelf should update to point to github locations for any cookbook dependency
 * i.e `cookbook "apt", github: "rackspace-cookbooks/apt"`

#Chef
## Attributes
* All Attribute hashes should be indexed with strings instead of symbols or using 'dot notation'
 * i.e. `node['rackspace_apt']['config']`, not `node[:rackspace_apt][:config]` and not `node.rackspace_apt.config`
* All Attributes namespace should match the cookbook name
 * i.e. `default['rackspace_user']`
* All Attributes that will be written a configuration file must fall under a ['config'] hash
 * i.e. `default['rackspace_apache']['config']`
* Attributes should default to the same ones as installed by the package or sane modifications.
* "data dirs" should be configurable for any service
 * i.e. for mysql `default['rackspace_mysql']['data_dir'] = "/var/lib/mysql"` 

## Recipes
* monitors.rb - Standard rackspace cloud checks for this cookbook. These should not automatically be included by the cookbook and should only be available to be included. 
 * This should populate the config hash `node['cloud_monitoring']['monitors']` 

##Templates
* Template provider calls should always include the `cookbook` attribute as to allow for easily overriding from a wrapper cookbooks. The default should always be the cookbook itself.
 * i.e. in recipe


```ruby   
template "/etc/sudoers" do
  cookbook node['rackspace_sudo']['templates_cookbook']['sudoers']
  source "sudoers.erb"
  mode 0440
  owner "root"
  group "root"
end
```

* i.e. in attributes

```ruby
   default['rackspace_sudo']['templates_cookbook']['sudoers'] = "rackspace_sudo"
```

## metadata.rb
* All dependencies should be listed with the pessimistic operation ("~> ") to the minor version
 * i.e. depends "apt", "~> 1.2" 
 

#Misc
## GIT Tags
* Version increases should have an accompanying git tag. Jenkins will be responsible for ensuring this is complete.

## Licensing
* All Cookbooks must be Apache 2.0 licensed. 
* Include a `LICENSE` file in the top level directory of the cookbook with the Apache 2.0 Official license
* Include a `License and Authors` section of the `README.md`. See example at [rackspace-user](https://github.com/rackspace-cookbooks/rackspace-user)
* If you've forked the cookbook from another repo, please add notes attributing the original work to that repo

## README.md / Documentation
* Please include a README.md file in the cookbook root directory.
* Please include Descriptions, Platform support, notes, notes on recipes, attributes, CONTRIBUTING and testing specifications.
* If the cookbook is a fork, please credit original cookbook authors.

## CHANGELOG.md
* Please include a `CHANGELOG.md` in the cookbook root directory
* Please include brief notes about your changes and your name in each pull request


# Testing specifications
* Added features should include additional tests. Pull requests without tests may be denied.
* Workflow: `Jenkins -> Thor -> Strainer -> knife -> foodcritic -> chefspec -> kitchen -> Thor -> Jenkins`

## CI
* CI will be performed via Jenkins at jenkins.rackops.org on any pull requests.

## test-kitchen support
* test-kitchen 1.x support is required for all cookbooks. Please see [test-kitchen](https://github.com/opscode/test-kitchen) for more details.
* test-kitchen should include platforms section:
 

```ruby
---
driver_plugin: vagrant
driver_config:
  require_chef_omnibus: latest

platforms:
- name: ubuntu-12.04
  driver_config:
    box: opscode-ubuntu-12.04
    box_url: http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_ubuntu-12.04_chef-provisionerless.box
  run_list:
  - recipe[rackspace_apt]
- name: centos-6.4
  driver_config:
    box: opscode-centos-6.4
    box_url: http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_centos-6.4_chef-provisionerless.box
- name: debian-7.2.0
  driver_config:
    box: opscode-debian-7.2.0
    box_url: http://opscode-vm-bento.s3.amazonaws.com/vagrant/virtualbox/opscode_debian-7.2.0_chef-provisionerless.box
  run_list:
  - recipe[rackspace_apt]
```

### test-kitchen structure
* Tests should be called and have any needed attributes set in a .kitchen.yml file with separate suites as appropriate. We will append our own .kitchen.local.yml during the jenkins run.

## Strainer
* standard Gemfile is [here](https://github.com/rackspace-cookbooks/contributing/blob/master/Gemfile)
* standard Strainerfile is [here](https://github.com/rackspace-cookbooks/contributing/blob/master/Strainerfile)

## Thor
* Standard Thorfile is [here](https://github.com/rackspace-cookbooks/contributing/blob/master/Thorfile)

## Rubocop
* standard .rubocop.yml is [here](https://github.com/rackspace-cookbooks/contributing/blob/master/.rubocop.yml)
* Disabling cops in code is allowed where the style recommendation results in code which is more difficult to read, is messier than the original, or is otherwise arguable as an anti-pattern.  Disabled cops must have comments documenting why they are disabled and disable target code blocks, not a whole file.  See [the Rubocop docs](https://github.com/bbatsov/rubocop#disabling-cops-within-source-code) for details on in-code disables.

### Chefspec
* All Chefspec tests should be located in `spec` within the parent cookbook
* All in memory testing. Isolated, independent, atomic.
* LWRPs and libraries need additional unit tests 

### Functional tests (test-kitchen)
* Tests should be handled as a directory under `/test/integration/$suitename`. This is defined [here](http://kitchen.ci/docs/getting-started/writing-test) whereby the $suitename matches the suite defined in .kitchen.yml

## Vagrantfile 
* Not required but nice to have :)

# Code Review
* All code additions and pull requests are subject to the following:
    * Automated CI run must pass. Successes and Failure feedback will be provided in the pull request.
    * An admin must review the code prior to being merged 

# Guidelines
* TODO: add notes regarding guidelines for advanced cookbooks (libraries, LWRPS, etc)


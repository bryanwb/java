Description
===========

Installs a Java. Uses OpenJDK by default but supports installation of Oracle's JDK.

**IMPORTANT NOTE** As of 26 March 2012 you can no longer directly download
the JDK from Oracle's website without using a special cookie. This cookbook uses
that cookie to download the oracle recipe on your behalf, but . . . 

the java::oracle recipe forces you to set either override
the node['java']['oracle']['accept_onerous_download_terms'] to true or set up a
private repository accessible by HTTP. 

Example

### override the `accept_onerous_download_terms`

roles/base.rb

```
default_attributes(
  :java => {
     :oracle => {
       "accept_onerous_download_terms" => true
     }
   }
)
```


### alternately, using a role

roles/base.rb

```
default_attributes(
  :java => {
     :jdk => {
       "6" => {
         :x86_64 => {
           :url => "http://hqlprrepo01.hq.un.fao.org/corporate/jdk-6u30-linux-x64.bin"
         }
       }
     }
   }
)
```

You are most encouraged to voice your complaints to Oracle and/or
switch to OpenJDK.

Requirements
============

Platform
--------

* Debian, Ubuntu
* CentOS, Red Hat, Fedora, Scientific, Amazon
* ArchLinux
* FreeBSD

Attributes
==========

See `attributes/default.rb` for default values.

* `node["java"]["install_flavor"]` - Flavor of JVM you would like installed (`oracle` or
`openjdk`), default `openjdk`.
* `node['java']['java_home']` - Default location of the "`$JAVA_HOME`".
* `node['java']['tarball']` - Name of the tarball to retrieve from your corporate
repository default `jdk1.6.0_29_i386.tar.gz`
* `node['java']['tarball_checksum']` - Checksum for the tarball, if you use a different
tarball, you also need to create a new sha256 checksum
* `node['java']['jdk']` - Version and architecture specific attributes for setting the
URL on Oracle's site for the JDK, and the checksum of the .tar.gz.
* `node['java']['remove_deprecated_packages']` - Removes the now deprecated Ubuntu JDK
packages from the system, default `false`
* `node['java']['oracle']['accept_onerous_download_terms']` - Indicates that you accept 
  oracle's EULA



Recipes
=======

default
-------

Include the default recipe in a run list, to get `java`.  By default
the `openjdk` flavor of Java is installed, but this can be changed by
using the `install_flavor` attribute.

OpenJDK is the default because of licensing changes made upstream by
Oracle. See notes on the `oracle` recipe below.

openjdk
-------

This recipe installs the `openjdk` flavor of Java.

oracle
------

This recipe installs the `oracle` flavor of Java. Since update JDK 1.6u27
you must use a browser cookie in order to accept Oracle's Terms of Service. 

You must specifically set node['java']['oracle']['accept_onerous_download_terms'] to
true using an attribute. It is set to false by default. If you do not accept
Oracle's onerous terms of service, you must override the url and checksum 
attribute with your own.

use distribution packages as Oracle changed the licensing terms with
JDK 1.6u27 and prohibited the practice for both the debian and EL worlds.

For both debian and centos/rhel, this recipe pulls the binary
distribution from the Oracle website, and installs it in the default
JAVA_HOME for each distribution. For debian/ubuntu, this is
/usr/lib/jvm/default-java. For Centos/RHEL, this is /usr/lib/jvm/java

After putting the binaries in place, the oracle recipe updates
/usr/bin/java to point to the installed JDK using the
`update-alternatives` script

oracle_i386
-----------

This recipe installs the 32-bit Java virtual machine without setting
it as the default. This can be useful if you have applications on the
same machine that require different versions of the JVM.


Testing with Vagrant
====================

The cookbook comes with a `Vagrantfile`, allowing you to test-drive the installation and configuration with Vagrant, a tool for building virtualized development infrastructures.

First, make sure you have VirtualBox and Vagrant installed

Next, clone this cookbook

You need to download the required third-party cookbooks (unless you already have them in `~/cookbooks`).

The easiest way is to use the bundled [_Berkshelf_](http://berkshelf.com) support:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~bash
  berks install --shims ./tmp/cookbooks
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The `Vagrantfile` supports two combinations of Linux distribution and JVM so far:

* Ubuntu Lucid 32 bit, oracle 7 jvm
* CentOS 6 32 bit, oracle 7 jvm


Resources/Providers
===================

This cookbook includess the "java_ark" LWRP that has been deprecated in favor of 
the [ark](http://github.com/opscode-cookbooks/ark) cookbook.


License and Author
==================

Author:: Seth Chisamore (<schisamo@opscode.com>)
Author:: Bryan W. Berry (<bryan.berry@gmail.com>)

Copyright:: 2008-2012, Opscode, Inc

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

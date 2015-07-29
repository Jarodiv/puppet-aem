[![Build Status](https://travis-ci.org/bstopp/puppet-aem.svg?branch=master)](https://travis-ci.org/bstopp/puppet-aem)
[![Dependency Status](https://gemnasium.com/bstopp/puppet-aem.svg)](https://gemnasium.com/bstopp/puppet-aem)
[![Code Climate](https://codeclimate.com/github/bstopp/puppet-aem/badges/gpa.svg)](https://codeclimate.com/github/bstopp/puppet-aem)
[![Test Coverage](https://codeclimate.com/github/bstopp/puppet-aem/badges/coverage.svg)](https://codeclimate.com/github/bstopp/puppet-aem/coverage)

# aem - Adobe Experience Manager

#### Table of Contents

1. [Overview - What is the AEM module?](#overview)
2. [Module Description - What does the module do?](#module-description)
3. [Setup - The basics of getting started with AEM module](#setup)
    * [What AEM affects](#what-aem-affects)
    * [Setup requirements](#setup-requirements)
    * [Beginning with AEM](#beginning-with-aem)
4. [Usage - How to use the module](#usage)
5. [Reference - AEM Module type and providers](#reference)
5. [Limitations - OS compatibility, etc.](#limitations)
6. [Development - Contributing to the module](#development)

## Overview

The AEM module installs, configures and manages an AEM instance.

## Module Description

The AEM module introduces the `aem` resource which is used to manage and configure an installation of AEM utilizing the Puppet DSL.

## Setup

### What AEM affects

  * AEM Installations may be modified by using this module. See [warnings](#warnings), for how existing instances are affected by enabling a this module.

### Setup Requirements 

AEM uses Ruby-based providers, so you must enable pluginsync. Java is also required to be installed on the system. Finally, due to the AEM platform being proprietary, this module does not provide installation jar files, it must be provided by the consumer.

### Beginning with AEM

A minimal AEM configuration is specified as:

~~~
aem { 'aem' :
  ensure      => present,
  source      => '/path/to/aem-quickstart.jar',
  home        => '/path/to/home',
}
~~~

See [Useage](#usage) and [Reference](#reference) for options and detailed explanations.

## Usage

### AEM Resource

The `aem` resource definition is used to install and manage an AEM instance. An AEM installation is considered complete when the following steps have occurred:

  * Unpacking the source file (See [documentation](https://docs.adobe.com/docs/en/aem/6-1/deploy/custom-standalone-install.html#Further options available from the Quickstart file).)
  * Configuring the start script (See [documentation](https://docs.adobe.com/docs/en/aem/6-1/deploy/command-line-start-and-stop.html).)
  * Starting & Stopping the server, creating base repository. _This does not create a service._

_Configuring an AEM installation where an unmanaged one exists is undefined._

#### Minimal Definition

This is  the minimal required `aem` resource definition to install AEM. The default property values will be used, and the installation user:group will be `root:root`.

~~~
aem { 'aem' :
  ensure      => present,
  source      => '/path/to/aem-quickstart.jar',
  home        => '/path/to/home',
}
~~~

#### Specific User/Group Example

You can optionally specify either a user and/or group to own the installation. This user and/or group will be used when executing the installation process. (Normal policies apply, see Puppet Provider _execute(*args)_ DSL defintion.)

~~~
aem { 'aem' :
  ensure      => present,
  source      => '/path/to/aem-quickstart.jar',
  home        => '/path/to/home',
  user        => 'aem',
  group       => 'aem',
}
~~~

#### Specify type Example

You can specify the type of AEM installation to create. This is either `author` or `publish`, once specified it cannot be changed. (See [AEM documentation](https://docs.adobe.com/docs/en/aem/6-1/deploy/configuring/configure-runmodes.html#Installation Run Modes))

~~~
aem { 'aem' :
  ensure      => present,
  source      => '/path/to/aem-quickstart.jar',
  home        => '/path/to/home',
  type        => publish,
}
~~~

#### Specify port Example

You can specify the port on which AEM will listen. (See [AEM documentation](https://docs.adobe.com/docs/en/aem/6-1/deploy/custom-standalone-install.html#Changing the Port Number by Renaming the File))

~~~
aem { 'aem' :
  ensure      => present,
  source      => '/path/to/aem-quickstart.jar',
  home        => '/path/to/home',
  port        => 8080,
}
~~~

## Reference

Types:

  * [aem](#type-aem)

###Type: aem

This type enables you to manage AEM instances within Puppet.

####Providers
**Note:** Not all features are available with all providers.

  * `linux`: Linux provider

**Autorequires:**

If Puppet is managing the home directory, user, or group parameters, the aem resource will autorequire those resources.

####Parameters

  * `name`: (namevar) Required. Specifies the name of the AEM resource.

  * `ensure`: Required. Ensures that the resource is present. Valid values are `present`, `absent`.

  * `context_root`: Optional. Specifies the URL context root for the AEM application. [Sling documentation](https://sling.apache.org/documentation/the-sling-engine/the-sling-launchpad.html). Defaults to `/`.

  * `debug_port`: Optional. Specifies the port on which to listen for remote debugging connections. Setting this will add the following JVM options: `-Xdebug -Xnoagent -Djava.compiler=NONE -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=<<port>>` Valid options: any port number. 

  * `group`: Optional. Sets the group for installation. Valid options: any valid group. Default: puppet executor group.

  * `home`: Required. Sets the directory in which AEM will be installed. Valid options: Any absolute system path. Default: `/opt/aem` or `C:/opt/aem`.

  * `jvm_mem_opts`: Optional. Specifies options for the JVM memory. This is separated from the JVM opts to simplify configurations. Valid options: any valid JVM parameter string. Default: `-Xmx1024m -XX:MaxPermSize=256M`.

  * `jvm_opts`: Optional. Specifies options to pass to the JVM. Valid options: any valid JVM parameter string. Default: None, but the following value is always passed, and cannot be overwritten: `-server -Djava.awt.headless=true`

  * `port`: Optional. Specifies the port on which AEM will listen. Valid options: any valid port. Default: 4502. [AEM documentation](https://docs.adobe.com/docs/en/aem/6-1/deploy/custom-standalone-install.html#Changing the Port Number by Renaming the File)

  * `runmodes`: Optional. Sets the array of runmodes to apply to the AEM instance. Do not use this to set options available via `type` configuration, or a `samplecontent` runmode. Valid options: any string array. [AEM documentation](https://docs.adobe.com/docs/en/aem/6-1/deploy/configuring/configure-runmodes.html).

  * `samplecontent`: Optional. Sets whether or not to include the sample content (e.g. Geometrixx). Valid options: `true` or `false`. Default: `true`. [AEM Documentation](https://docs.adobe.com/docs/en/aem/6-1/deploy/configuring/configure-runmodes.html#Using samplecontent and nosamplecontent).

  * `snooze`: Optional. Sets the wait period between checks for installation completion. When monitoring the system for up state, this is the wait period between checks. Value is specified in seconds. Valid options: any number. Default: `10`.

  * `source`: Required. Sets the source jar file to use, provided by user. Valid options: any absolute file.

  * `timeout`: Optional. Sets the timeout allowed for startup monitoring. If the installation doesn't finish by the timeout, an error will be generated. Value is specified in seconds. Valid option: any number. Default: `600`.

  * `type`: Optional. Specifies the AEM installation type. Valid options: `author` or `publish`. Default: `author`. [AEM documentation](https://docs.adobe.com/docs/en/aem/6-1/deploy/configuring/configure-runmodes.html#Installation Run Modes)

  * `user`: Optional. Sets the user for installation. Valid options: any valid group. Defaults: puppet executor user.

  * `version`: Optional. Sets the version of AEM. If not specified, will be found via _quickstart_ jar name. Valid options: any semantic version. 

## Limitations

### OS Compatibility

This module has been tested on: 

  * CentOS 6, 7
  * Ubuntu 12.04, 14.04
  * Debian 6.0, 7.8 

### AEM Compatibility

This module has been tested with the following AEM versions:

  * 6.0
  * 6.1

### Warnings

It is up to the consumer to ensure that the correct version of Java is installed based on the AEM version. See [AEM Documentation](https://docs.adobe.com/docs/en/aem/6-1/deploy/technical-requirements.html) for compatibility.

Defining an AEM resource as absent will remove the instance from the system, regardless of whether or not it was originally managed by puppet.

## Development

This module in its early stages, any updates or feature additions are welcome. 

_Please make sure you do not include any AEM Installer jars in PRs._


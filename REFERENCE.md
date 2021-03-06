# Reference
<!-- DO NOT EDIT: This document was generated by Puppet Strings -->

## Table of Contents

**Classes**

* [`named`](#named): Configures named for execution on a system taking selinux into account.
* [`named::caching`](#namedcaching): Configures a caching nameserver.
* [`named::caching::hints`](#namedcachinghints): Determines what to add to the /var/named/named.ca root hints file.
* [`named::chroot`](#namedchroot): Configures named in a chroot jail for execution on a system.
* [`named::install`](#namedinstall): Installs the appropriate packages for BIND based on the chroot status
* [`named::non_chroot`](#namednon_chroot): Configures named for execution on a system taking selinux into account.
* [`named::service`](#namedservice): A helper class that serves to control the named service and has been

**Defined types**

* [`named::caching::forwarders`](#namedcachingforwarders): Adds forwarders entries to your caching nameserver configuration.

## Classes

### named

It pulls all config files from rsync.

You will need to ensure that rsync is serving out the appropriate space so
that the configuration can be pulled.

The default SIMP configuration will do this for the 'default' space, but
other spaces will need to be added as appropriate.

#### Examples

##### 

```puppet
* Given 'default' configuration that you would like to serve
* Create a chroot pull from that domain on your DNS node
    include 'named'

* Create the associated hieradata
    ---
    named::bind_dns_rsync : 'default'
    rsync::server : 'rsync.foo.bar'

* Ensure that the rsync space is being served out properly from the rsync
  server (probably your puppet master)
    include 'rsync::server'
    # The word 'default' here is the equivalent of the
    # named::bind_dns_rsync variable above.
    rsync::server::section { "bind_dns_default_${environment}":
      auth_users  => ['bind_dns_default_rsync'],
      comment     => 'DNS "default" configuration',
      path        => "${rsync_base}/bind_dns/default",
      hosts_allow => 127.0.0.1 # This is correct if using stunnel
```

#### Parameters

The following parameters are available in the `named` class.

##### `chroot_path`

Data type: `Stdlib::Absolutepath`

If set, enables the chroot jailed version of named.
Simply set to an empty string ("") if you want named outside of a chroot
jail with SELinux disabled.

This is the default if you do not have SELinux enabled.
Chroot jails for named are not compatible with SELinux and will be
disabled if SELinux is enforcing.

* Value in module data

##### `chroot`

Data type: `Boolean`

Toggle the use of chroot and override the autodetected setting to be
compatible with SELinux.

* WARNING: If you have an SELinux enabled system, forcing the chroot may
  cause named to become non-functional.

Default value: !

##### `bind_dns_rsync`

Data type: `String`

The target under "${rsync_base}/bind_dns" from which to fetch all
BIND DNS content.

Default value: 'default'

##### `firewall`

Data type: `Boolean`

Enable SIMP firewall management

Default value: simplib::lookup('simp_options::firewall', { 'default_value' => false })

##### `rsync_server`

Data type: `String`

The rsync server from which to pull the named configuration.

Default value: simplib::lookup('simp_options::rsync::server', { 'default_value' => '127.0.0.1' })

##### `rsync_timeout`

Data type: `Stdlib::Compat::Integer`

The timeout when connecting to the rsync server.

Default value: simplib::lookup('simp_options::rsync::timeout', { 'default_value' => '2' })

##### `sebool_named_write_master_zones`

Data type: `Boolean`

If you need to use dynamic DNS or zone transfers, and are using SELinux,
you will need to set this to ``true``

Default value: `false`

### named::caching

You will need to call named::caching::forwarders to make it useful.

There is also named::caching::root_hints which allows you to set the entire
contents of the 'named.ca' hint file.

If you want something other than the defaults provided here, use the main
named class.

#### Parameters

The following parameters are available in the `named::caching` class.

##### `chroot_path`

Data type: `Stdlib::Absolutepath`

The path to the chroot location.

* Has no effect if SELinux is enforcing.
* Defaults to `named::chroot_path` per module Hiera data.

### named::caching::hints

Determines what to add to the /var/named/named.ca root hints file.

#### Parameters

The following parameters are available in the `named::caching::hints` class.

##### `content`

Data type: `String`

Can be set to arbitrary content of your choosing. This will be included
verbatim in the named.ca file.

Default value: ''

##### `use_defaults`

Data type: `Boolean`

Set to true if you wish to use the default values for the root hints
file. This is recommended if you are not running within an intranet.

Default value: `false`

### named::chroot

It pulls all config files from rsync.

It is meant to be called from named directly.

#### Parameters

The following parameters are available in the `named::chroot` class.

##### `nchroot`

Data type: `Stdlib::Absolutepath`

The Chroot jail for named. This should probably not be changed.

Default value: $::named::chroot_path

##### `bind_dns_rsync`

Data type: `String`

The target under the /var/simp/environments/{environment}/rsync/{os}/{maj_version}/bind_dns from which to fetch all
BIND DNS content.

Default value: $::named::bind_dns_rsync

##### `rsync_source`

Data type: `String`

The source from which the module will pull its files on the rsync server

Default value: "bind_dns_${::named::bind_dns_rsync}_${::environment}_${facts['os']['name']}_${facts['os']['release']['major']}/named"

##### `rsync_server`

Data type: `String`

The rsync server from which to pull the named configuration.

Default value: $::named::rsync_server

##### `rsync_timeout`

Data type: `Stdlib::Compat::Integer`

The timeout when connecting to the rsync server.

Default value: $::named::rsync_timeout

### named::install

Installs the appropriate packages for BIND based on the chroot status

#### Parameters

The following parameters are available in the `named::install` class.

##### `ensure`

Data type: `String`

The `package` ensure setting for installed packages

@see https://docs.puppet.com/puppet/latest/reference/type.html#package-attribute-ensure

Default value: simplib::lookup('simp_options::package_ensure', { 'default_value' => 'installed' })

##### `chroot`

Data type: `Boolean`

Whether or not to use a chroot jail

Default value: `true`

##### `chroot_path`

Data type: `Stdlib::Absolutepath`

The path to the chroot jail

Default value: $::named::chroot_path

### named::non_chroot

It pulls all config files from rsync.

It is meant to be called from named directly.

#### Parameters

The following parameters are available in the `named::non_chroot` class.

##### `bind_dns_rsync`

Data type: `String`

The target under #
/var/simp/environments/{environment}/rsync/{os}/{maj_version}/bind_dns
from which to fetch all BIND DNS content.

Default value: $::named::bind_dns_rsync

##### `rsync_source`

Data type: `String`

The source from which the module will pull its files on the rsync server

Default value: "bind_dns_${::named::bind_dns_rsync}_${::environment}_${facts['os']['name']}_${facts['os']['release']['major']}/named"

##### `rsync_server`

Data type: `String`

The rsync server from which to pull the named configuration.

Default value: $::named::rsync_server

##### `rsync_timeout`

Data type: `Stdlib::Compat::Integer`

The timeout when connecting to the rsync server.

Default value: $::named::rsync_timeout

### named::service

isolated to make the overall logic more understandable.

#### Parameters

The following parameters are available in the `named::service` class.

##### `chroot`

Data type: `Boolean`

Whether or not to run BIND in a chroot jail.

Default value: `true`

##### `chroot_path`

Data type: `Stdlib::Absolutepath`

@see named::chroot_path

Default value: $::named::chroot_path

## Defined types

### named::caching::forwarders

$name can be a whitespace delimited list of values to provide backward
compatibility with the common::resolv format.

#### Examples

##### 

```puppet
named::caching::forwarders { '1.2.3.4': ensure => 'present' }
named::caching::forwarders { '1.2.3.4 5.6.7.8 9.10.11.12':
  ensure => 'present'
}
```


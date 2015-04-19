# Log Courier

[![Build Status](https://img.shields.io/travis/driskell/log-courier/develop.svg)](https://travis-ci.org/driskell/log-courier)
[![Latest Release](https://img.shields.io/github/release/driskell/log-courier.svg)](https://github.com/driskell/log-courier/releases/latest)

Log Courier is a lightweight tool created to ship log files speedily and
securely, with low resource usage, to remote [Logstash](http://logstash.net)
instances. The project is an enhanced fork of
[Logstash Forwarder](https://github.com/elasticsearch/logstash-forwarder) 0.3.1
with many fixes and behavioural improvements.

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Main Features](#main-features)
- [Differences to Logstash Forwarder](#differences-to-logstash-forwarder)
- [Public Repositories](#public-repositories)
  - [RPM](#rpm)
  - [DEB](#deb)
- [Building from Source](#building-from-source)
- [Logstash Integration](#logstash-integration)
- [Generating Certificates](#generating-certificates)
- [Documentation](#documentation)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Main Features

* Read events from a file or over a Unix pipeline
* Follow log file rotations and movements
* Close files after inactivity, reopening if they change
* Add [extra fields](docs/Configuration.md#fields) to events prior to shipping
* [Reload configuration](docs/Configuration.md#reloading) without restarting
* Ship events securely using TLS with server (and optionally client) certificate
verification
* Ship events in plaintext using TCP
* Monitor shipping speed and status with the
[Administration utility](docs/AdministrationUtility.md)
* Pre-process events using codecs (e.g. [Multiline](docs/codecs/Multiline.md),
[Filter](docs/codecs/Filter.md))
* [Logstash Integration](docs/LogstashIntegration.md) with an input and output
plugin
* Very low resource usage

## Differences to Logstash Forwarder

Log Courier is an enhanced fork of
[Logstash Forwarder](https://github.com/elasticsearch/logstash-forwarder) 0.3.1
with many fixes and behavioural improvements. The primary changes are:

* The publisher protocol is rewritten to avoid many causes of "i/o timeout"
which would result in duplicate events sent to Logstash
* The prospector and registrar are heavily revamped to handle log rotations and
movements far more reliably, and to report errors cleanly
* The harvester is improved to retry if an error occurred rather than stop
* The configuration can be reloaded without restarting
* An administration tool is available which can display the shipping speed and
status of all watched log files
* Fields configurations can contain arrays and dictionaries, not just strings
* Codec support is available which allows multiline processing at the sender
side
* A TCP transport is available which removes the requirement for SSL
certificates
* There is support for client SSL certificate verification
* Peer IP address and certificate DN can be added to received events in Logstash
to distinguish events send from different instances
* Windows: Log files are not locked allowing log rotation to occur
* Windows: Log rotation is detected correctly

## Public Repositories

### RPM

*The Log Courier repository depends on the __EPEL__ repository which can be
installed automatically on CentOS distributions by running
`yum install epel-release`. For other distributions, please follow the
installation instructions on the
[EPEL homepage](https://fedoraproject.org/wiki/EPEL).*

To install the Log Courier repository, download the corresponding `.repo`
configuration file below, and place it in `/etc/yum.repos.d`. Log Courier may
then be installed using `yum install log-courier`.

* **CentOS/RedHat 6.x**: [driskell-log-courier-epel-6.repo](https://copr.fedoraproject.org/coprs/driskell/log-courier/repo/epel-6/driskell-log-courier-epel-6.repo)
* **CentOS/RedHat 7.x**:
[driskell-log-courier-epel-7.repo](https://copr.fedoraproject.org/coprs/driskell/log-courier/repo/epel-6/driskell-log-courier-epel-7.repo)

Once installed, modify the configuration file at
`/etc/log-courier/log-courier.conf` to suit your needs, then start the Log
Courier service to begin shipping.

    service log-courier start

### DEB

A Debian/Ubuntu compatible **PPA** repository is under consideration. At the
moment, no such repository exists.

## Building from Source

Requirements:

1. Linux, Unix, OS X or Windows
1. The [golang](http://golang.org/doc/install) compiler tools (1.2-1.4)
1. [git](http://git-scm.com)
1. GNU make

*__Linux/Unix:__ Most requirements can usually be installed by your favourite
package manager.*  
*__OS X:__ Git and GNU make are provided automatically by XCode.*  
*__Windows:__ GNU make for Windows can be found
[here](http://gnuwin32.sourceforge.net/packages/make.htm).*

To build the binaries, simply run `make` as follows.

    git clone https://github.com/driskell/log-courier
    cd log-courier
    make

The log-courier program can then be found in the 'bin' folder. Service scripts
for various platforms can be found in the
[contrib/initscripts](contrib/initscripts) folder, or it can be run on the
command line:

    bin/log-courier -config /path/to/config.conf

*Note: If you receive errors whilst running `make`, try `gmake` instead.*

## Logstash Integration

Log Courier communicates with Logstash via an input plugin called "courier".

You may install the plugin using the Logstash 1.5 Plugin manager. Run the
following as the user Logstash was installed with.

    cd /path/to/logstash
    bin/plugin install logstash-input-log-courier

Detailed instructions, including integration with Logstash 1.4.x, can be found
on the [Logstash Integration](docs/LogstashIntegration.md) page.

*Note: If you receive a Plugin Conflict error, try updating the zeromq output
plugin first using `bin/plugin update logstash-output-zeromq`*

## Generating Certificates

Log Courier provides a commands to help generate SSL certificates: `lc-tlscert`.
This utility is also bundled with the packaged versions of Log Courier, and
should be immediately available at the command-line.

When building from source, running `make selfsigned` will automatically build
and run the `lc-tlscert` utility that can quickly and easily generate a
self-signed certificate, along with the corresponding configuration snippets,
for the 'tls' transport.

## Documentation

* [Administration Utility](docs/AdministrationUtility.md)
* [Command Line Arguments](docs/CommandLineArguments.md)
* [Configuration](docs/Configuration.md)
* [Logstash Integration](docs/LogstashIntegration.md)
* [Change Log](docs/ChangeLog.md)

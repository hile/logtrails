
Log trails message collector and processor
==========================================

This module implement logtrails, a log file processor and pubsub message
queue reader.

Actually the parsing is not limited to log files. Whatever you can parse that generates
events in known format can be parsed.

Configuration
=============

This code does not do anything sensible without configuration files.

Example configuration files are in examples/ directory. Please note you should not just use
the default files as is, these are only examples!

Default configuration file path is ~/.logtrails.cfg unless given as arguments.

Required details in logtrails.cfg:

* Redis queue configuration


* Logfile parser patterns

    Each logfile to parse is configured in 'parsers' section, with the pattern parser
    congurigurations to apploy specified in the arguments.

* Patterns

    Each patterns configuration file labels the generated matches by section and pattern name.

    The pattern configuration files can override default redis pubsub channel 'logtrails' with
    a [settings] section key 'channel'. Obviously you can't label a pattern group 'settings'
    because of this.


Usage
=====

Following examples illustrate usage of the commands with example configuration.

To use example patterns, you need to copy them to some path and configure correct paths
in the file. The following examples run from the source code tree.

Prerequisites
-------------

Since events are created to redis pubsub channels, you obviously need redis running. The
redis host and port are configured in logtrails.cfg, defaulting to localhost.

Redis auth will be added later. This is just PoC after all!

Run watcher daemon
------------------

Following command starts the logtrails watcher in daemon mode, monitoring logfiles
configured in examples/logtrails.cfg with parsers from examples/logtrails/ directory.

    ./bin/logtrails -c examples/logtrails.cfg daemon

The daemon will run on foreground until stopped.

Example systemd service file for the daemon mode is available from examples/logtrails.service.
Obviously you need to configure filenames and paths correctly, and make sure the daemon
user can read the logfiles.

Dump matches from a logfile to redis
------------------------------------

Following commands parse /var/log/messages and dump any matching messages to redis
pubsub queues according to the configuration files in examples/parsers/ directory.

    ./bin/logtrails -c examples/logtrails.cfg dump /var/log/messages
    ./bin/logtrails -c examples/logtrails.cfg dump --no-errors /var/log/messages

Dump generated messages from redis
----------------------------------

Following command connects to localhost:6379 redis and dumps JSON messages logged into
the given channels, ignoring others.

    ./bin/logtrails-queue-dump logtrails-auth logtrails-system

Further ideas && TODO
=====================

* Make logtrails logstash compatible

  It's pretty trivial to change the output of logtrails to generate events for logstash

* Add examples for actual queue processor

  Real beef of this tool is not to dump the data as JSON with the dump tool, but generate
  processed events from known data. This is not yet implemented, but pretty much follows
  the pattern in bin/logtrails-queue-dump script, just doing something sensible with the
  entries instead of dumping them to stdout.

Copyright and license
=====================

Copyright Ilkka Tuohela <hile@iki.fi>, 2016.

This module is licensed with Python Software Foundation License.


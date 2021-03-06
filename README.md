carbon-influxdb is a Graphite Carbon to [InfluxDB](http://influxdb.com) bridge daemon.

## Why do I need this?

Great question, especially as InfluxDB already has a graphite/carbon plugin.  Perhaps...

* You want a easy transition or just want to test out Influx, and don't want to have to change the configuration for all your services, then change it back if Influx doesn't work out.  carbon-influxdb is a drop-in replacement for the carbon-cache daemon (you need an InfluxDB instance running, of course)
    - This allows you to test out Influx at scale!
* You want to write to different InfluxDB databases, perhaps one database per environment.  This is not supported by InfluxDB at the moment. Centralizing multiple Graphite servers to one InfluxDB cluster is a great idea!
* You want a tiered architecture, instead of overwhelming a single Influx cluster with connections from all your services from multiple environments
* You want to record the source hostname so you can filter or group a metric by host
* You want to test out [Hosted Influx](http://customers.influxdb.com) easily and don't want to have to go through Influx support to enable or configure the Carbon bridge.
* You want to tee the existing Carbon stream to your old Graphite server in addition to Influx, just as a backup

I started writing this due to some of the reasons above, but honestly just wanted to play with [Rust](http://rust-lang.org).

## Rust or Scala?

The Scala implementation is complete and production ready.  It includes:
* retries
* basic auth, so your password isn't sent in plaintext

The Rust one was what I started out with, and it technically works, but is missing retries, and settable username/password.

## Deployment

1. cd into scala subdir, then run `sbt assembly`
2. Using `src/main/resources/reference.conf` as a template, create a config file, filling in the details for database, hostname, password, etc.
3. Copy the `target/scala-2.10/carbon-influxdb-*` executable plus the config file to your Graphite box.

At this point you can start carbon-influxdb and replace the graphite carbon-cache daemon.  You can enable `carbon-tee` to tee traffic to both InfluxDB as well as the old carbon-cache daemon/Graphite for comparison purposes.

Note that default configuration already does log file rotation!  You can supply your own logback.xml or change JVM parameters by setting `JVM_OPTS` before calling `carbon-influxdb`.

## Notes on gradual Graphite to InfluxDB transition



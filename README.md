# dropwizard-logback-logstash-encoder

# WARNING
This is a fork of from the [stalar](https://github.com/stalar/dropwizard-logstash-encoder/) fork of the [larrymyers](https://github.com/larrymyers/dropwizard-logstash-encoder) fork, which itself is a fork of the poorly maintained [wikia](https://github.com/Wikia/dropwizard-logstash-encoder) fork. Don't expected this fork to be maintained in time.

[Dropwizard](http://dropwizard.io/) logging addon for sending logs using the [logstash-logback-encoder](https://github.com/logstash/logstash-logback-encoder). 
This is needed because Dropwizard overwrites the default mechanism for loading logback configuration (logback.xml) in favor of its application.yml files.

## Dropwizard Version Compatibility

* dropwizard 0.8.4 => dropwizard-logstash-encoder 1.1.0
* dropwizard 0.9.0 => dropwizard-logstash-encoder 1.2.0
* dropwizard 1.3.14 => dropwizard-logstash-encoder 3.0.0 (Java 11)

## Installation
Must be manually build and added to your repository manager.

Maven:
```xml
<dependency>
  <groupId>de.joblift</groupId>
  <artifactId>dropwizard-logstash-encoder</artifactId>
  <version>3.0.0</version>
</dependency>
```

## Usage
You must configure dropwizard to use these appenders in your application.yml file:
```yml
logging:
  appenders:
    - type: logstash-socket # LogstashSocketAppender, for LogstashTcpSocketAppender use logstash-tcp
      ...
```

Additional configuration keys for the appender, see [logstash-logback-encoder#usage](https://github.com/logstash/logstash-logback-encoder/blob/master/README.md#usage) for info. 
All configs apply to both `logstash-socket` and `logstash-tcp`, unless otherwise noted:

* `host` - string - maps to `syslogHost` when using `logstash-socket`, and `remoteHost` when using `logstash-tcp`
* `port` - int
* `includeCallerInfo` - boolean
* `includeContext` - boolean
* `includeMdc` - boolean
* `customFields` - hashmap - the configuration differs from the original logstash-logback-encoder config in that this is not a raw json string (see example below)
* `fieldNames` - hashmap
* `queueSize` - int - only valid for `logstash-tcp`
* `includeCallerData` - bool - only valid for `logstash-tcp`
* `prefix` - string - pattern written before the JSON object, only valid for `logstash-socket`
* `suffix` - string - pattern written after the JSON object, only valid for `logstash-socket`

For more information about prefixes and suffixes, please refer to [logstash-logback-encoder#prefixsuffix](https://github.com/logstash/logstash-logback-encoder/blob/master/README.md#prefixsuffix).

Example config:
```yaml
logging:
  appenders:
    - type: logstash-socket
      host: 127.0.0.1
      fieldNames:
        message: msg
        timestamp: log_date
      customFields:
        myTestField1: myTestVal
        myTestField2: 2
```

Then, loggers can be used the same way as if they were configured using logback.xml for logstash-logback-encoder, example (using Guava):
```java
LoggerFactory.getLogger("myTestLogger").warn(
	appendEntries(
		new ImmutableMap.Builder<String, Object>()
			.put("some_key", 1)
			.put("some_other_key", "two")
			.build()
	),
	"warning! warning!");
```

Or even more simpler, by using [de.galan:commons](https://github.com/galan/commons) Say class, eg.:
```java
Say.warn("Hello {key}", "World");
```

## License

Distributed under the Eclipse Public License either version 1.0 or (at
your option) any later version.

# log4j-64k-syslogwriter
Writes log4j events to Syslog of size max up to 64K

I wanted to log from Jboss EAP 6.3 to Loggly but was unable to configure
a custom handler that used the EnhancedPatternLayout which Loggly 
requires. So I added a getter and setter to set the format string of an
EnhancedPatternLayout to the current appender. Now we can create a
deployment module in Jboss with the jar from this project:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<module xmlns="urn:jboss:module:1.1" name="com.github.psquickitjayant.log4j">
    <resources>
        <resource-root path="log4jSyslogWriter64k-1.0.0.jar"/>
    </resources>
    <dependencies>
         <!-- Insert any dependencies here like the example below -->
         <module name="org.apache.log4j"/>
    </dependencies>
</module>
```


and add a custom logging handler from that module by entering these 
commands into jboss cli like so:


```
/subsystem=logging/custom-handler=REMOTE-LOG:add(class="com.github.psquickitjayant.log4j.SyslogAppender64k", module="com.github.psquickitjayant.log4j", level=INFO, formatter="not used", properties=[("SyslogHost"=>"localhost"), ("Facility"=>"Local3"), ("Header"=>"true"), ("enhancedPatternLayoutFormatString"=>"java %d{ISO8601}{GMT} %p %t %c{1}.%M - %m %throwable %n")])
/subsystem=logging/root-logger=ROOT:add-handler(name=REMOTE-LOG)
```

I have done that and checked it into Ethersource in modules/.
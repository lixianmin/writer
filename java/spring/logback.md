```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="true" scanPeriod="30 seconds" debug="false">
    <!--定义日志文件的存储地址 勿在 LogBack 的配置中使用相对路径-->
    <property name="LOG_PATH" value="logs"></property>
    <Property name="LOG_PATTERN"
              value="%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level [%file:%line:%method] %msg%n"></Property>

    <!--for remove log at the start of application -->
    <statusListener class="ch.qos.logback.core.status.NopStatusListener"/>

    <contextName>liquid4k</contextName>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <!--<pattern>%d{HH:mm:ss.SSS} [%thread] %-5level - [%file:%line] - %msg%n</pattern>-->
            <pattern>${LOG_PATTERN}</pattern>
            <charset>utf-8</charset>
        </encoder>
    </appender>
    <!--
          说明：
          1、日志级别及文件
              日志记录采用分级记录，级别与日志文件名相对应，不同级别的日志信息记录到不同的日志文件中
              log_info.log 记录info, warn, error级别日志
              log_warn.log 记录warn, error级别日志
              log_error.log 记录error级别日志
          2、文件路径
              若开发在intellij idea中运行项目，则使用项目根目录下的logs文件夹。
              若部署到Tomcat下，则使用Tomcat下的logs文件中

          2020-01-16 logback.xml调整说明：
          1. 日志同时输出到stdout, log_info.log, log_warn.log, log_error.log中
          2. 日志格式按 ${LOG_PATTERN} 输出，调整时间格式，带文件名、行号、方法名，不再带log名，示例：
             2020-01-16 11:43:33 [main] INFO  [MainRollup.kt:26:doRun] log message is here.

          3. 采用ThresholdFilter，过滤日志级别
          4. 存储格式由gbk调整为utf-8
          5. 昨天以前的日志分别归档至info, warn, error目录下
          6. 单个日志文件最大限制为10MB
       -->

    <!-- 日志记录器，日期滚动记录 -->
    <appender name="FILE_INFO" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 日志文件的格式 -->
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <!--<pattern>%d{yy/MM/dd HH:mm:ss.SSS} %-5level - %logger{10} - [%file:%line:%method] - %n%caller{10} - %msg%n</pattern>-->
            <pattern>${LOG_PATTERN}</pattern>
            <charset>utf-8</charset>
        </encoder>
        <!-- 此日志文件记录info以上级别的日志 -->
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>info</level>
        </filter>
        <file>${LOG_PATH}/log_info.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--日志输出位置, 可使用相对或绝对路径 -->
            <fileNamePattern>${LOG_PATH}/info/log_info-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <!-- 可选节点，控制保留的归档文件的最大数量，超出数量就删除旧文件假设设置每个月滚动，且<maxHistory>是6， 则只保存最近6个月的文件，删除之前的旧文件。注意，删除旧文件是，那些为了归档而创建的目录也会被删除 -->
            <TimeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <MaxFileSize>10MB</MaxFileSize>
            </TimeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
    </appender>

    <appender name="FILE_WARN" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${LOG_PATTERN}</pattern>
            <charset>utf-8</charset>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>warn</level>
        </filter>
        <File>${LOG_PATH}/log_warn.log</File>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOG_PATH}/warn/log_warn-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <TimeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <MaxFileSize>10MB</MaxFileSize>
            </TimeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
    </appender>

    <appender name="FILE_ERROR" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>${LOG_PATTERN}</pattern>
            <charset>utf-8</charset>
        </encoder>
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>error</level>
        </filter>
        <File>${LOG_PATH}/log_error.log</File>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOG_PATH}/error/log_error-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <TimeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <MaxFileSize>10MB</MaxFileSize>
            </TimeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
    </appender>

    <logger name="com.hazelcast.internal.diagnostics" level="WARN"/>
    <logger name="com.zaxxer.hikari.pool.HikariPool" level="WARN"/>
    <logger name="com.coinbene.webconsole.app" level="INFO">
        <appender-ref ref="STDOUT"/>
    </logger>

    <root level="INFO">
        <appender-ref ref="STDOUT"/>
        <appender-ref ref="FILE_INFO"/>
        <appender-ref ref="FILE_WARN"/>
        <appender-ref ref="FILE_ERROR"/>
    </root>
</configuration>
```


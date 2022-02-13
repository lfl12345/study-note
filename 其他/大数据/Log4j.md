## 三大组件

* Logger控制日志文件的输出级别和日志是否输入
* Appender指定日志的输出方式（ConsoleAppender控制台输出、FileAppender文件输出、JDBCAppender等）
* Layout控制日志信息的输出格式（simple格式、HTML格式、PatternLayout自定义格式）

## 日志的级别

* Log4j在log4j.Level类中定义了OFF、 FATAL、ERROR、WARN、INFO、DEBUG、TRACE、ALL八种日志级别
* 一般只使用4个级别，优先级从高到低为 ERROR > WARN > INFO > DEBUG。 

| 目录  | 说明                                           |
| ----- | ---------------------------------------------- |
| ERROR | 发生错误事件，但仍不影响系统的继续运行         |
| WARN  | 警告，即潜在的错误情形                         |
| INFO  | 一般在粗粒度级别上，强调应用程序的运行全程     |
| DEBUG | 一般用于细粒度级别上，对调试应用程序非常有帮助 |
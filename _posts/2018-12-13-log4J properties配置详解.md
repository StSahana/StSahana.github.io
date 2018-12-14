# log4J properties配置详解

> log4j 1.2版本在2015年已经停止维护，并已经更新到log4j 2.x,但是目前来看，log4j 1.2版本仍是应用最广泛的版本。随着log4j版本的升级，性能一定会越来越好，或许以后项目会逐渐的使用log4j 2.x。目前网上的很多教程都是针对log4j 1.2版本的，暂且先用着，遇到问题也好百度、google。

> Log4j有三个主要的组件：Loggers(记录器)，Appenders (输出源)和Layouts(布局)。这里可简单理解为日志类别，日志要输出的地方和日志以何种形式输出。综合使用这三个组件可以轻松地记录信息的类型和级别，并可以在运行时控制日志输出的样式和位置。

## 1. 设置log等级和输出

### log4j内置了8个输出等级

> 由高到低排列为：

- OFF(*)

  The OFF has the highest possible rank and is intended to turn off logging.

- FATAL

  The `FATAL` level designates very severe error events that will presumably lead the application to abort.

- ERROR

  The `ERROR` level designates error events that might still allow the application to continue running.

- WARN

  The `WARN` level designates potentially harmful situations.

- INFO

  The `INFO` level designates informational messages that highlight the progress of the application at coarse-grained level.

- DEBUG

  The `DEBUG` Level designates fine-grained informational events that are most useful to debug an application.

- TRACE(*)

  The `TRACE` Level designates finer-grained informational events than the `DEBUG`

- ALL(*)

  The `ALL` has the lowest possible rank and is intended to turn on all logging.

> 1. Log4j建议只使用四个级别，优先级从低到高分别是DEBUG,INFO,WARN,ERROR 。
> 2. log4j只输出级别不低于设定级别的日志信息，例如设置了INFO，则INFO、WARN、ERROR和FATAL级别的日志信息都会输出，而级别比INFO低的DEBUG则不会输出。
> 3. 调试时设置为DEBUG，发布时根据需要设置INFO,WARN,ERROR

### 设置语句

```
log4j.rootLogger = [level],appenderName,appenderName2
/**
*@level 输出等级
*@appenderName 输出目的地址，可以自己设置变量，下面会介绍
*@example log4j.rootLogger=DEBUG,A1，A2 // 输出级别为DEBUG，输出到A1，A2
**/
```

## 2.设置输出目标

### 设置格式

```
log4j.appender.{appenderName} = {className}
/**
*@appederName 个人命名的变量
*@ classname 下面的五个变量之一
*@example log4j.appender.A1=org.apache.log4j.ConsoleAppender //A1为输出到控制台 
**/
```

### appender 格式

```
org.apache.log4j.ConsoleAppender(输出到控制台) 
org.apache.log4j.FileAppender(输出到文件) 
org.apache.log4j.DailyRollingFileAppender(每天产生一个日志文件) 
org.apache.log4j.RollingFileAppender(文件大小到达指定尺寸的时候产生一个新的文件) 
org.apache.log4j.WriterAppender(将日志信息以流格式发送到任意指定的地方) 
```

#### 1.ConsoleAppender属性

- Threshold=WARN：指定日志信息的最低输出级别，默认为DEBUG。
- ImmediateFlush=true：表示所有消息都会被立即输出，设为false则不输出，默认值是true。
- Target=System.err：默认值是System.out。

#### 2.FileAppender属性

- Threshold=WARN：指定日志信息的最低输出级别，默认为DEBUG。
- ImmediateFlush=true：表示所有消息都会被立即输出，设为false则不输出，默认值是true。
- Append=false：true表示消息增加到指定文件中，false则将消息覆盖指定的文件内容，默认值是true。
- File={path}：指定消息输出到{path}文件中。@example File=D:/logging.log4j

#### 3.DailyRollingFileAppender选项：

- Threshold=WARN：指定日志信息的最低输出级别，默认为DEBUG。

- ImmediateFlush=true：表示所有消息都会被立即输出，设为false则不输出，默认值是true。

- Append=false：true表示消息增加到指定文件中，false则将消息覆盖指定的文件内容，默认值是true。

- File={path}：指定消息输出到{path}文件中。@example File=D:/logging.log4j

- DatePattern='.'yyyy-MM：每月滚动一次日志文件，即每月产生一个新的日志文件。当前月的日志文件名为logging.log4j，前一个月的日志文件名为logging.log4j.yyyy-MM。

  另外，也可以指定按周、天、时、分等来滚动日志文件，对应的格式如下：

  1. '.'yyyy-MM：每月
  2. '.'yyyy-ww：每周
  3. '.'yyyy-MM-dd：每天
  4. '.'yyyy-MM-dd-a：每天两次
  5. '.'yyyy-MM-dd-HH：每小时
  6. '.'yyyy-MM-dd-HH-mm：每分钟

#### 4.RollingFileAppender选项：

- Threshold=WARN：指定日志信息的最低输出级别，默认为DEBUG。
- ImmediateFlush=true：表示所有消息都会被立即输出，设为false则不输出，默认值是true。
- Append=false：true表示消息增加到指定文件中，false则将消息覆盖指定的文件内容，默认值是true。
- File=D:/logs/logging.log4j：指定消息输出到logging.log4j文件中。
- MaxFileSize=100KB：后缀可以是KB, MB 或者GB。在日志文件到达该大小时，将会自动滚动，即将原来的内容移到logging.log4j.1文件中。
- MaxBackupIndex=2：指定可以产生的滚动文件的最大数，例如，设为2则可以产生logging.log4j.1，logging.log4j.2两个滚动文件和一个logging.log4j文件。

### 设置输出属性

```java
log4j.appender.{appenderName} = {className}log4j.appender.appenderName.Option1 = value1…log4j.appender.appenderName.OptionN = valueN
  /**    直接拿官网的例子    
  # Set options for appender named "A2"    
  # A2 should be a RollingFileAppender, with maximum file size of 10 MB    
  # using at most one backup file. A2's layout is TTCC, using the    
  # ISO8061 date format with context printing enabled.
  log4j.appender.A2=org.apache.log4j.RollingFileAppender    
  log4j.appender.A2.MaxFileSize=10MB    
  log4j.appender.A2.MaxBackupIndex=1
  log4j.appender.A2.layout=org.apache.log4j.TTCCLayout   
  log4j.appender.A2.layout.ContextPrinting=enabled   
  log4j.appender.A2.layout.DateFormat=ISO8601
  **/
```

## 3.设置输出格式

### 日期格式

```
log4j.appender.appenderName.layout=className

```

### className选项

- org.apache.log4j.HTMLLayout（以HTML表格形式布局）
- org.apache.log4j.PatternLayout（可以灵活地指定布局模式）
- org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串）
- org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等等信息）

#### 各选项的内容如下

- HTMLLayout选项：

  - LocationInfo=true：输出java文件名称和行号，默认值是false。

  - Title=My Logging： 默认值是Log4J Log Messages。

- PatternLayout选项：

  - ConversionPattern=%m%n：设定以怎样的格式显示消息。

    - %p：输出日志信息的优先级，即DEBUG，INFO，WARN，ERROR，FATAL。
    - %d：输出日志时间点的日期或时间，默认格式为ISO8601，也可以在其后指定格式，如：%d{yyyy/MM/dd HH:mm:ss,SSS}。
    - %r：输出自应用程序启动到输出该log信息耗费的毫秒数。
    - %t：输出产生该日志事件的线程名。


    - %l：输出日志事件的发生位置，相当于%c.%M(%F:%L)的组合，包括类全名、方法、文件名以及在代码中的行数。例如：test.TestLog4j.main(TestLog4j.java:10)。

    - %c：输出日志信息所属的类目，通常就是所在类的全名。

    - %M：输出产生日志信息的方法名。

    - %F：输出日志消息产生时所在的文件名称。

    - %L:：输出代码中的行号。

    - %m:：输出代码中指定的具体日志信息。

    - %n：输出一个回车换行符，Windows平台为"\r\n"，Unix平台为"\n"。

    - %x：输出和当前线程相关联的NDC(嵌套诊断环境)，尤其用到像java servlets这样的多客户多线程的应用中。

    - %%：输出一个"%"字符。

      另外，还可以在%与格式字符之间加上修饰符来控制其最小长度、最大长度、和文本的对齐方式。如：

      1. c：指定输出category的名称，最小的长度是20，如果category的名称长度小于20的话，默认的情况下右对齐。
      2. %-20c："-"号表示左对齐。
      3. %.30c：指定输出category的名称，最大的长度是30，如果category的名称长度大于30的话，就会将左边多出的字符截掉，但小于30的话也不会补空格。

- XMLLayout选项属性 

  - LocationInfo = TRUE:默认值false,输出java文件名称和行号

## 4.设置特定包的输出级别

如：log4j.logger.org.springframework=DEBUG 

## 5.实例

### 输出到控制台

```
log4j.appender.systemOut = org.apache.log4j.ConsoleAppender 
log4j.appender.systemOut.layout = org.apache.log4j.PatternLayout 
log4j.appender.systemOut.layout.ConversionPattern = [%-5p][%-22d{yyyy/MM/dd HH:mm:ssS}][%l]%n%m%n 
log4j.appender.systemOut.Threshold = DEBUG 
log4j.appender.systemOut.ImmediateFlush = TRUE 
log4j.appender.systemOut.Target = System.out 
```

### 输出到文件

```
log4j.appender.logFile = org.apache.log4j.FileAppender 
log4j.appender.logFile.layout = org.apache.log4j.PatternLayout 
log4j.appender.logFile.layout.ConversionPattern = [%-5p][%-22d{yyyy/MM/dd HH:mm:ssS}][%l]%n%m%n 
log4j.appender.logFile.Threshold = DEBUG 
log4j.appender.logFile.ImmediateFlush = TRUE 
log4j.appender.logFile.Append = TRUE 
log4j.appender.logFile.File = ../Struts2/WebRoot/log/File/log4j_Struts.log 
log4j.appender.logFile.Encoding = UTF-8 

```

###  按DatePattern输出到文件

```
log4j.appender.logDailyFile = org.apache.log4j.DailyRollingFileAppender 
log4j.appender.logDailyFile.layout = org.apache.log4j.PatternLayout 
log4j.appender.logDailyFile.layout.ConversionPattern = [%-5p][%-22d{yyyy/MM/dd HH:mm:ssS}][%l]%n%m%n 
log4j.appender.logDailyFile.Threshold = DEBUG 
log4j.appender.logDailyFile.ImmediateFlush = TRUE 
log4j.appender.logDailyFile.Append = TRUE 
log4j.appender.logDailyFile.File = ../Struts2/WebRoot/log/DailyFile/log4j_Struts 
log4j.appender.logDailyFile.DatePattern = '.'yyyy-MM-dd-HH-mm'.log' 
log4j.appender.logDailyFile.Encoding = UTF-8 
```

###  设定文件大小输出到文件

```
log4j.appender.logRollingFile = org.apache.log4j.RollingFileAppender 
log4j.appender.logRollingFile.layout = org.apache.log4j.PatternLayout 
log4j.appender.logRollingFile.layout.ConversionPattern = [%-5p][%-22d{yyyy/MM/dd HH:mm:ssS}][%l]%n%m%n 
log4j.appender.logRollingFile.Threshold = DEBUG 
log4j.appender.logRollingFile.ImmediateFlush = TRUE 
log4j.appender.logRollingFile.Append = TRUE 
log4j.appender.logRollingFile.File = ../Struts2/WebRoot/log/RollingFile/log4j_Struts.log 
log4j.appender.logRollingFile.MaxFileSize = 1MB 
log4j.appender.logRollingFile.MaxBackupIndex = 10 
log4j.appender.logRollingFile.Encoding = UTF-8 

```

###  用Email发送日志

```
log4j.appender.logMail = org.apache.log4j.net.SMTPAppender 
log4j.appender.logMail.layout = org.apache.log4j.HTMLLayout 
log4j.appender.logMail.layout.LocationInfo = TRUE 
log4j.appender.logMail.layout.Title = Struts2 Mail LogFile 
log4j.appender.logMail.Threshold = DEBUG 
log4j.appender.logMail.SMTPDebug = FALSE 
log4j.appender.logMail.SMTPHost = SMTP.163.com 
log4j.appender.logMail.From = xly3000@163.com 
log4j.appender.logMail.To = xly3000@gmail.com 
#log4j.appender.logMail.Cc = xly3000@gmail.com 
#log4j.appender.logMail.Bcc = xly3000@gmail.com 
log4j.appender.logMail.SMTPUsername = xly3000 
log4j.appender.logMail.SMTPPassword = 1234567 
log4j.appender.logMail.Subject = Log4j Log Messages 
#log4j.appender.logMail.BufferSize = 1024 
#log4j.appender.logMail.SMTPAuth = TRUE 
```

###  将日志保存到MySQL数据库

```
log4j.appender.logDB = org.apache.log4j.jdbc.JDBCAppender 
log4j.appender.logDB.layout = org.apache.log4j.PatternLayout 
log4j.appender.logDB.Driver = com.mysql.jdbc.Driver 
log4j.appender.logDB.URL = jdbc:mysql://127.0.0.1:3306/xly 
log4j.appender.logDB.User = root 
log4j.appender.logDB.Password = 123456 
log4j.appender.logDB.Sql = INSERT INTOT_log4j(project_name,create_date,level,category,file_name,thread_name,line,all_category,message)values('Struts2','%d{yyyy-MM-ddHH:mm:ss}','%p','%c','%F','%t','%L','%l','%m')
```

[^StSahana]: 写作过程参考了网上的资料，感谢各路大神的分享。
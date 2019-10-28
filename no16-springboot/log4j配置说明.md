# log4j配置说明

### 日志级别
OFF > FATAL > ERROR > WARN > INFO > DEBUG  > ALL 

### 输出日志的位置
````
org.apache.log4j.ConsoleAppender（控制台）    
org.apache.log4j.FileAppender（文件） 
org.apache.log4j.DailyRollingFileAppender（每天产生一个日志文件） 
org.apache.log4j.RollingFileAppender（文件大小到达指定尺寸的时候产生一个新的文件） 
org.apache.log4j.WriterAppender（将日志信息以流格式发送到任意指定的地方）
````

### 日志的输出格式
````
org.apache.log4j.HTMLLayout（以HTML表格形式布局） 
org.apache.log4j.PatternLayout（可以灵活地指定布局模式） 
org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串） 
org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等信息） 
````

### PatternLayout灵活布局的参数说明
````
%p：输出日志信息的优先级，即DEBUG，INFO，WARN，ERROR，FATAL。 
%d：输出日志时间点的日期或时间，默认格式为ISO8601，也可以在其后指定格式，如：%d{yyyy/MM/dd HH:mm:ss,SSS}。 
%r：输出自应用程序启动到输出该log信息耗费的毫秒数。 
%t：输出产生该日志事件的线程名。 
%l：输出日志事件的发生位置，相当于%c.%M(%F:%L)的组合，包括类全名、方法、文件名以及在代码中的行数。例如：test.TestLog4j.main(TestLog4j.java:10)。 
%c：输出日志信息所属的类目，通常就是所在类的全名。 
%M：输出产生日志信息的方法名。 
%F：输出日志消息产生时所在的文件名称。 
%L:：输出代码中的行号。 
%m:：输出代码中指定的具体日志信息。 
%n：输出一个回车换行符，Windows平台为"\r\n"，Unix平台为"\n"。 
%x：输出和当前线程相关联的NDC(嵌套诊断环境)，尤其用到像java servlets这样的多客户多线程的应用中。 
%%：输出一个"%"字符。
````

### 配置方式： 
````
log4j.appender.appenderName = className 
log4j.appender.appenderName.Option1 = value1 
… 
log4j.appender.appenderName.OptionN = valueN 
````

### 配置示例
````
# 控制台(console) 
log4j.appender.console=org.apache.log4j.ConsoleAppender 
log4j.appender.console.Threshold=DEBUG 
log4j.appender.console.ImmediateFlush=true 
log4j.appender.console.Target=System.err 
log4j.appender.console.layout=org.apache.log4j.PatternLayout 
log4j.appender.console.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n 

# 日志文件(logFile) 
log4j.appender.logFile=org.apache.log4j.FileAppender 
log4j.appender.logFile.Threshold=DEBUG 
log4j.appender.logFile.ImmediateFlush=true 
log4j.appender.logFile.Append=true 
log4j.appender.logFile.File=D:/logs/log.log4j 
log4j.appender.logFile.layout=org.apache.log4j.PatternLayout 
log4j.appender.logFile.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n 
# 回滚文件(rollingFile) 
log4j.appender.rollingFile=org.apache.log4j.RollingFileAppender 
log4j.appender.rollingFile.Threshold=DEBUG 
log4j.appender.rollingFile.ImmediateFlush=true 
log4j.appender.rollingFile.Append=true 
log4j.appender.rollingFile.File=D:/logs/log.log4j 
log4j.appender.rollingFile.MaxFileSize=200KB 
log4j.appender.rollingFile.MaxBackupIndex=50 
log4j.appender.rollingFile.layout=org.apache.log4j.PatternLayout 
log4j.appender.rollingFile.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n 
# 定期回滚日志文件(dailyFile) 
log4j.appender.dailyFile=org.apache.log4j.DailyRollingFileAppender 
log4j.appender.dailyFile.Threshold=DEBUG 
log4j.appender.dailyFile.ImmediateFlush=true 
log4j.appender.dailyFile.Append=true 
log4j.appender.dailyFile.File=D:/logs/log.log4j 
log4j.appender.dailyFile.DatePattern='.'yyyy-MM-dd 
log4j.appender.dailyFile.layout=org.apache.log4j.PatternLayout 
log4j.appender.dailyFile.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n 
# 应用于socket 
log4j.appender.socket=org.apache.log4j.RollingFileAppender 
log4j.appender.socket.RemoteHost=localhost 
log4j.appender.socket.Port=5001 
log4j.appender.socket.LocationInfo=true 
# Set up for Log Factor 5 
log4j.appender.socket.layout=org.apache.log4j.PatternLayout 
log4j.appender.socket.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n 
# Log Factor 5 Appender 
log4j.appender.LF5_APPENDER=org.apache.log4j.lf5.LF5Appender 
log4j.appender.LF5_APPENDER.MaxNumberOfRecords=2000 
# 发送日志到指定邮件 
log4j.appender.mail=org.apache.log4j.net.SMTPAppender 
log4j.appender.mail.Threshold=FATAL 
log4j.appender.mail.BufferSize=10 
log4j.appender.mail.From = xxx@mail.com 
log4j.appender.mail.SMTPHost=mail.com 
log4j.appender.mail.Subject=Log4J Message 
log4j.appender.mail.To= xxx@mail.com 
log4j.appender.mail.layout=org.apache.log4j.PatternLayout 
log4j.appender.mail.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n 
# 应用于数据库 
log4j.appender.database=org.apache.log4j.jdbc.JDBCAppender 
log4j.appender.database.URL=jdbc:mysql://localhost:3306/test 
log4j.appender.database.driver=com.mysql.jdbc.Driver 
log4j.appender.database.user=root 
log4j.appender.database.password= 
log4j.appender.database.sql=INSERT INTO LOG4J (Message) VALUES('=[%-5p] %d(%r) --> [%t] %l: %m %x %n') 
log4j.appender.database.layout=org.apache.log4j.PatternLayout 
log4j.appender.database.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n 

# 自定义Appender 
log4j.appender.im = net.cybercorlin.util.logger.appender.IMAppender 
log4j.appender.im.host = mail.cybercorlin.net 
log4j.appender.im.username = username 
log4j.appender.im.password = password 
log4j.appender.im.recipient = corlin@cybercorlin.net 
log4j.appender.im.layout=org.apache.log4j.PatternLayout 
log4j.appender.im.layout.ConversionPattern=[%-5p] %d(%r) --> [%t] %l: %m %x %n
````
### 配置参数说明
````
(1)ConsoleAppender选项： 
Threshold=WARN：指定日志信息的最低输出级别，默认为DEBUG。 
ImmediateFlush=true：表示所有消息都会被立即输出，设为false则不输出，默认值是true。 
Target=System.err：默认值是System.out。 
(2)FileAppender选项： 
Threshold=WARN：指定日志信息的最低输出级别，默认为DEBUG。 
ImmediateFlush=true：表示所有消息都会被立即输出，设为false则不输出，默认值是true。 
Append=false：true表示消息增加到指定文件中，false则将消息覆盖指定的文件内容，默认值是true。 
File=D:/logs/logging.log4j：指定消息输出到logging.log4j文件中。 
(3)DailyRollingFileAppender选项： 
Threshold=WARN：指定日志信息的最低输出级别，默认为DEBUG。 
ImmediateFlush=true：表示所有消息都会被立即输出，设为false则不输出，默认值是true。 
Append=false：true表示消息增加到指定文件中，false则将消息覆盖指定的文件内容，默认值是true。 
File=D:/logs/logging.log4j：指定当前消息输出到logging.log4j文件中。 
DatePattern='.'yyyy-MM：每月滚动一次日志文件，即每月产生一个新的日志文件。当前月的日志文件名为logging.log4j，前一个月的日志文件名为logging.log4j.yyyy-MM。 
另外，也可以指定按周、天、时、分等来滚动日志文件，对应的格式如下： 
1)'.'yyyy-MM：每月 
2)'.'yyyy-ww：每周 
3)'.'yyyy-MM-dd：每天 
4)'.'yyyy-MM-dd-a：每天两次 
5)'.'yyyy-MM-dd-HH：每小时 
6)'.'yyyy-MM-dd-HH-mm：每分钟 
(4)RollingFileAppender选项： 
Threshold=WARN：指定日志信息的最低输出级别，默认为DEBUG。 
ImmediateFlush=true：表示所有消息都会被立即输出，设为false则不输出，默认值是true。 
Append=false：true表示消息增加到指定文件中，false则将消息覆盖指定的文件内容，默认值是true。 
File=D:/logs/logging.log4j：指定消息输出到logging.log4j文件中。 
MaxFileSize=100KB：后缀可以是KB, MB 或者GB。在日志文件到达该大小时，将会自动滚动，即将原来的内容移到logging.log4j.1文件中。 
MaxBackupIndex=2：指定可以产生的滚动文件的最大数，例如，设为2则可以产生logging.log4j.1，logging.log4j.2两个滚动文件和一个logging.log4j文件。 
````
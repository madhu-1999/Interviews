Logback is one of the most widely used logging frameworks in the Java Community. It's a replacement for its predecessor, Log4j. Logback offers a faster implementation, provides more options for configuration, and more flexibility in archiving old log files.

# Logback Architecture
The Logback architecture is comprised of three classes: Logger, Appender, and Layout.
- A Logger is a context for log messages. This is the class that applications interact with to create log messages.
- Appenders place log messages in their final destinations. A Logger can have more than one Appender. Ex: Console, files, smtp etc
- Layout prepares messages for outputting. Logback supports the creation of custom classes for formatting messages, as well as robust configuration options for the existing ones.
# Reasons to prefer logback over log4j 1.x
+ Faster implementation
+ Extensive battery of tests
+ Automatic reloading of config files
+ Graceful recovery from I/O failures
+ Automatic removal of old log archive files
+ Automatic compression of archived log files

# Logging levels
## TRACE
The most fine-grained information only used in rare cases where you need the full visibility of what is happening in your application and inside the third-party libraries that you use
## DEBUG
Less granular than TRACE, used for diagnostic/ troubleshooting purposes
## INFO
Logs when something "happens", like application entered a certain state.
## WARN
Logs when something unexpected happened, but application didn't fail
## ERROR
Logs when something unexpected happened and application failed.

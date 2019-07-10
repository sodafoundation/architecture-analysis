# Log specification
**Authors:** [liuqinguestc](https://github.com/liuqinguestc)

## Summary

OpenSDS log is not uniform and standardized, and the log format is various. We need to develop a unified specification log format to easily locate and solve problems.

## Motivation

Each OpenSDS developer uses a different log format, and the log location is different, causing problems for positioning and troubleshooting. We need to standardize the log format so that developers and testers can quickly locate problems, solve problems, improve development efficiency, and reduce communication costs.

### Goals
* Standardize the log format to show more comprehensive log content, reduce communication costs.
* Specify the log storage location, which is convenient for developers to locate problems and solve problems more effectively.

### Non Goals
* Logs can cover all key issues.

## Design Details
The log format is as follows:
* logContent := fmt.Sprintf("%s %s [pid: %d] %s [%v:%v %v] %v\n", timeNow, LogPrefix, os.Getpid(), logLevel, filename, line, funcname, logMsg)
  * timeNow: Current system time, accurate to microseconds, for example:
      * timeNow := time.Now().Format("2006-01-02 15:04:05.000000")
  * LogPrefix: multi-cloud、hotpot、nbp
  * os.Getpid: current process id
  * logLevel: Log level,such as INFO、WARNING、ERROR、FATAL.
  * filename: go file name.
  * line: Line Number.
  * funcname: function name
  * logMsg: log detail Messages

Log files should be stored in "/var/log/" directory, log file shoud be named "{YYYY-MM-DD}_{ProjectName}.log", for example: "2019-07-02_multi-cloud.log" 
### REST API impact

All the REST API which need logs.

### Data model impact

No

### Security impact

No

### Developer impact

Every developer should print logs according to specifications.




 

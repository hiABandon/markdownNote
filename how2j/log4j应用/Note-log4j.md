log4j
===
入门
---
> log4j写代码调试用的
>
> 使用Log4j进行日志输出，需要导入jar包

### 使用log4j
```java
package log4j;

import org.apache.log4j.BasicConfigurator;
import org.apache.log4j.Level;
import org.apache.log4j.Logger;

public class TestLo4j {
  static Logger logger = Logger.getLogger(TestLo4j.class);
  //基于类的名称获取日志对象

  public static void main(String[] args) {
      BasicConfigurator.configure();
      // 进行默认配置
      logger.setLeve(Level.DEBUG);
      // 设置入职输出级别

      // 进行不同级别的日志输出
      logger.trace("跟踪信息");
      logger.debug("调试信息");
      logger.into("输出信息");
      Thread.sleep(1000);// 为了便于观察前后日志输出的时间差
      logger.warn("警告信息");
      logger.error("错误信息");
      logger.fatal("致命信息");
  }
}
```

输出结果有几个改观：
1. 知道是log4j.TestLog4j这个类里的日志
2. 是在[main]线程里的日志
3. 日志级别可观察，一共有6个级别 TRACE DEBUG INFO WARN ERROR FATAL
4. 日志输出级别范围可控制， 如代码所示，只输出高于DEBUG级别的，那么TRACE级别的日志自动不输出
5. 每句日志消耗的毫秒数(最前面的数字)，可观察，这样就可以进行性能计算


配置讲解
---
> 要使用log4j除了要导入jar包外还要在src目录下添log4j.properties文件

```
log4j.rootLogger=debug, stdout, R

log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout

# Pattern to output the caller's file name and line number.
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] (%F:%L) - %m%n

log4j.appender.R=org.apache.log4j.RollingFileAppender
log4j.appender.R.File=example.log

log4j.appender.R.MaxFileSize=100KB
# Keep one backup file
log4j.appender.R.MaxBackupIndex=5

log4j.appender.R.layout=org.apache.log4j.PatternLayout
log4j.appender.R.layout.ConversionPattern=%p %t %c - %m%n
```

然后修改TestLog4j并运行。由两个效果
1. 会显示哪个类的哪一行输出
2. 会把日志输出到e:\project\log4j\example.log这个位置

```java
package log4j;

import org.apache.log4j.Logger;
import org.apache.log4j.PropertyConfigurator;

public class TestLog4j {
    static Logger logger = Logger.getLogger(TestLog4j.class);
    public static void main(String[] args) throws InterruptedException {
        PropertyConfigurator.configure("e:\\project\\log4j\\src\\log4j.properties");
        // log4j的配置方式按照log4j.properties文件中的设置进行
        for (int i = 0; i < 5000; i++) {
            logger.trace("跟踪信息");
            logger.debug("调试信息");
            logger.info("输出信息");
            logger.warn("警告信息");
            logger.error("错误信息");
            logger.fatal("致命信息");
        }
    }
}
```


log4j
---

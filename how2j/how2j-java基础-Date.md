Java的日期类Date
===
- java.util.Date;

时间原点
---
- **1970年1月1日8点0分0秒**
  - **1970年**：1969年发布了第一个UNIX版本：AT&T
  - **8点**是因为东八区。


创建日期对象
---

```Java
publiv class TestDate {
  Date d1 = new Date();
  syso("当前时间" + d1);

  Date d2 = new Date();
  syso("从1970年1月1日 早上8点0分0秒
   开始，过了5秒钟的时间为：" + d2);
}
```

方法
----

- getTime()
  - 得到一个**long型**整数，
  - 表示距离时间原点所经历的毫秒数

- `System.currentTimeMillis()`
  - 当前日期的毫秒数

日期格式化类
===
simpleDateFormat日期格式化类
---

```Java
public static void main(String[] args) {
  SimpleDateFormat sdf = new SimpleDateFormat("yyy-MM-dd HH:mm:ss SSS")；
  Date d = new Date();
  String str = sdf.format(d);
  syso("当前时间通过yyyy-MM-dd HH:mm:ss SSS 格式化后的输出：" + str);

  SimpleDateFormat sdf1 = new SimpleDateFormat("yyyy-MM-dd");
  Date d1 = new Date();
  String str1 = sdf1.format(d1);
  syso("当前时间通过yyyy-MM-dd 格式化后的输出：" + str1);
}
```
字母 | 代表
--- | ---
y | 年
M | 月
d | 日
H | 24进制的小时
h | 12进制的小时
m | 分钟
s | 秒
S | 毫秒

字符串转日期
---

- 需要注意到，字符串转日期必须满足yyyy/MM/dd HH:mm:ss这种格式，否则会报错。
```Java
public static void main(String[] args) {
  SimpleDateFormat sdf = new SinmleDateFormat("yyyy/MM/dd HH:mm:ss");
  String str = "2016/1/5 12:12:12";

  try {
    Date d = sdf.parse(str);
    Syso("字符串 %s 通过格式 yyy/mm %n 转换为日期对象: %s",str,d.toString());
  } catch (ParseException e) {
    e.printStackTrace();
  }
}
```

Calendar类(日历类)
===
```Java
public static TestDate {
  //使用单例模式获取日历对象
  Calendar c =  Calendar.getInstance();
  // 通过日历对象得到日期对象
  Date d = c.getTime();

  Date d2 = new Date(0);
  c.setTime(d2);
  // 把这个日历调整成时间原点
}
```

翻日历的两个方法（set add）
---

```Java
package date;

import java.text.SimpleDateFormat;
//
import java.util.Calendar;
import java.util.Date;

public class TestDate {

    private static SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

    public static void main(String[] args) {
        Calendar c = Calendar.getInstance();
        Date now = c.getTime();
        // 当前日期
        System.out.println("当前日期：\t" + format(c.getTime()));

        // 下个月的今天
        c.setTime(now);
        c.add(Calendar.MONTH, 1);
        System.out.println("下个月的今天:\t" +format(c.getTime()));

        // 去年的今天
        c.setTime(now);
        c.add(Calendar.YEAR, -1);
        System.out.println("去年的今天:\t" +format(c.getTime()));

        // 上个月的第三天
        c.setTime(now);
        c.add(Calendar.MONTH, -1);
        c.set(Calendar.DATE, 3);
        System.out.println("上个月的第三天:\t" +format(c.getTime()));

    }

    private static String format(Date time) {
        return sdf.format(time);
    }
}
```

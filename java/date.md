# Date, Calendar, DateFormat
## 常识
#### GMT时间
即格林尼治平时（Greenwich Mean Time）。
格林尼治是英国伦敦南郊原皇家格林尼治天文台所在地，地球本初子午线的标界处，世界计算时间和经度的起点。
在格林威治子午线上的平太阳时称为世界时（UTC）， 又叫格林威治平时（GMT）。
我们知道各地都有各地的地方时间。如果对国际上某一重大事情，用地方时间来记录，就会感到复杂不便．而且将来日子一长容易搞错。
因此，天文学家就提出一个大家都能接受且又方便的记录方法，那就是以格林尼治的地方时间为标准，各地的地方平时与世界时之差等于该地的地理经度。

## 时间和时区API
#### java.util.Date
```java
Date date = new Date();
// or
Date date = Calendar.getInstance().getTime();  
```
类Date表示特定的瞬间，精确到毫秒。Date对象本身所存储的毫秒数可以通过date.getTime()方法得到，该函数返回自1970年1月1日 00:00:00 GMT以来此对象表示的毫秒数。
它与时区和地域没有关系(其实可以认为是GMT时间)。

#### java.util.Calendar
Calendar的getInstance()方法：有参数为TimeZone和Locale的重载，可以使用指定时区和语言环境获得一个日历；无参则使用默认时区和语言环境获得日历。

#### TimeZone

* TimeZone对象给我们的是原始的偏移量，也就是与GMT相差的微秒数，即TimeZone表示时区偏移量，本质上以毫秒数保存与GMT的差值。
* 获取TimeZone可以通过时区ID，如"America/New_York"，也可以通过GMT+/-hh:mm来设定。例如北京时间可以表示为GMT+8:00。
* TimeZone.getRawOffset()方法可以用来得到当前时区的标准时间到GMT的偏移量。上段提到的"America/New_York"和"GMT+8:00"两个时区的偏移量分别为-18000000和28800000。
* 时区协调：```TimeZone.setDefault(TimeZone.getTimeZone("Asia/Shanghai")); ``` 或者通过format+parse转换时区

```java
import java.util.Date;  
import java.util.TimeZone;  
public class TimeZoneSwitch {  
      
    public static void main(String[] args) {    
        Date date = new Date(1391174450000L); // 2014-1-31 21:20:50      
        System.out.println(date);    //Fri Jan 31 21:20:50 CST 2014 
        date = changeTimeZone(date, TimeZone.getTimeZone("Asia/Shanghai"), TimeZone.getTimeZone("GMT"));    
        System.out.println(date);    //Fri Jan 31 13:20:50 CST 2014
    }    
        
    /**  
     * 获取更改时区后的日期  
     * @param date 日期  
     * @param oldZone 旧时区对象  
     * @param newZone 新时区对象  
     * @return 日期  
     */    
    public static Date changeTimeZone(Date date, TimeZone oldZone, TimeZone newZone) {    
        Date dateTmp = null;    
        if (date != null) {    
            int timeOffset = oldZone.getRawOffset() - newZone.getRawOffset();    
            dateTmp = new Date(date.getTime() - timeOffset);    
        }    
        return dateTmp;    
    }    
}
```

#### DateFormat
DateFormat是日期/时间格式化子类的抽象类，它以与语言无关的方式格式化并解析日期或时间。
日期/时间格式化子类（如 SimpleDateFormat）允许进行格式化（也就是日期 -> 文本）、解析（文本-> 日期）和标准化，
将日期表示为 Date 对象，或者表示为从 GMT（格林尼治标准时间）1970 年 1 月 1 日 00:00:00 这一刻开始的毫秒数。
SimpleDateFormat则是一个以与语言环境有关的方式来格式化和解析日期的具体类，可以以“日期和时间模式”字符串指定日期和时间格式。

#### 日期模式
|字母|日期或时间元素|表示|示例（具体格式跟字母个数有关）|
|:---|:-------------|:---|:---|
|G|Era|标志符|Text|
|y|年|Year|1996; 96|
|M|年中的月份|Month|July; Jul; 07|
|w|年中的周数|Number|27|
|W|月份中的周数|Number|2|
|D|年中的天数|Number|189|
|d|月份中的天数|Number|10|
|F|月份中的星期|Number|2|
|E|星期中的天数|Text|Tuesday; Tue|
|a|am/pm 标记|Text|PM|
|H|一天中的小时数（0-23）|Number|0|
|k|一天中的小时数（1-24）|Number|24|
|K|am/pm 中的小时数（0-11）|Number|0|
|h|am/pm 中的小时数（1-12）|Number|12|
|m|小时中的分钟数|Number|30|
|s|分钟中的秒数|Number|55|
|S|毫秒数|Number|978|
|z|时区|General time zone|Pacific Standard Time; PST; GMT-08:00|
|Z|时区|RFC 822 time zone|-0800|

## 一些实例
```java
public class DateExample {
    public static void main(String[] args) {
        Date date = new Date();
        DateFormat shortDateFormat = DateFormat.getDateTimeInstance(DateFormat.SHORT,DateFormat.SHORT);
        DateFormat mediumDateFormat = DateFormat.getDateTimeInstance(DateFormat.MEDIUM,DateFormat.MEDIUM);
        DateFormat longDateFormat = DateFormat.getDateTimeInstance(DateFormat.LONG,DateFormat.LONG);
        DateFormat fullDateFormat = DateFormat.getDateTimeInstance(DateFormat.FULL,DateFormat.FULL);

        System.out.println(shortDateFormat.format(date));  //05-8-8 上午9:17 
        System.out.println(mediumDateFormat.format(date)); //2005-8-8 9:17:42
        System.out.println(longDateFormat.format(date));  //2005年8月8日 上午09时17分42秒
        System.out.println(fullDateFormat.format(date));  //2005年8月8日 09时17分42秒 GMT+08:00

        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss"); // 'T'
        System.out.println(sdf.parse("2017-06-01T12:00:00"));
    }
}
```


## 总结

1. 计算机内部记录的时间(Date date = new Date()), 为格林尼治标准时(GMT). 即java.util.Date代表一个时间点，其值为距公元1970年1月1日 00:00:00的毫秒数。所以它可以认为是没有时区和Locale概念的。


2. 日期格式化类DateFormat, 对于不同地区的配置一般有两个点, 一个是Locale , 一个是TimeZone
   * 前者(Locale)使DateFormat按所配置的地区特性来输出文字(例如中国,美国,法国不同地区对日期的表示格式不一样,中国可能是2001年10月5日)
   * 后者(TimeZone)让DateFormat知道怎么去转换,去调整时间偏移度,从而得到符合配置的时区的时间。

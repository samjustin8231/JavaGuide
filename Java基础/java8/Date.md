

Java.time包中的是类是不可变且线程安全的。新的时间及日期API位于java.time中，下面是一些关键类

* Instant——它代表的是时间戳

* LocalDate——不包含具体时间的日期，比如2014-01-14。它可以用来存储生日，周年纪念日，入职日期等。

* LocalTime——它代表的是不含日期的时间

* LocalDateTime——它包含了日期及时间，不过还是没有偏移信息或者说时区。

* ZonedDateTime——这是一个包含时区的完整的日期时间，偏移量是以UTC/格林威治时间为基准的。

# 常用API

```
getYear()    int    获取当前日期的年份
getMonth()    Month    获取当前日期的月份对象
getMonthValue()    int    获取当前日期是第几月
getDayOfWeek()    DayOfWeek    表示该对象表示的日期是星期几
getDayOfMonth()    int    表示该对象表示的日期是这个月第几天
getDayOfYear()    int    表示该对象表示的日期是今年第几天
withYear(int year)    LocalDate    修改当前对象的年份
withMonth(int month)    LocalDate    修改当前对象的月份
withDayOfMonth(intdayOfMonth)    LocalDate   修改当前对象在当月的日期
isLeapYear()    boolean    是否是闰年
lengthOfMonth()    int    这个月有多少天
lengthOfYear()    int    该对象表示的年份有多少天（365或者366）
plusYears(longyearsToAdd)    LocalDate   当前对象增加指定的年份数
plusMonths(longmonthsToAdd)    LocalDate   当前对象增加指定的月份数
plusWeeks(longweeksToAdd)    LocalDate   当前对象增加指定的周数
plusDays(longdaysToAdd)    LocalDate   当前对象增加指定的天数
minusYears(longyearsToSubtract)    LocalDate    当前对象减去指定的年数
minusMonths(longmonthsToSubtract)    LocalDate    当前对象减去注定的月数
minusWeeks(longweeksToSubtract)    LocalDate    当前对象减去指定的周数
minusDays(longdaysToSubtract)    LocalDate    当前对象减去指定的天数
compareTo(ChronoLocalDateother)    int    比较当前对象和other对象在时间上的大小，返回值如果为正，则当前对象时间较晚，
isBefore(ChronoLocalDateother)    boolean   比较当前对象日期是否在other对象日期之前
isAfter(ChronoLocalDateother)    boolean   比较当前对象日期是否在other对象日期之后
isEqual(ChronoLocalDateother)    boolean   比较两个日期对象是否相等
```


# 参考文献
* [Java8 日期、时间操作](https://www.cnblogs.com/ark-blog/p/9694950.html)
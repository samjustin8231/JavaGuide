# java日期时间相关概念


瞬时时间（Instant）,持续时间（duration），日期（date）,时间（time），时区（time-zone）以及时间段（Period）。Java 8仍然延用了ISO的日历体系，并且与它的前辈们不同，java.time包中的类是`不可变且线程安全的`。新的时间及日期API位于`java.time`包中，下面是里面的一些关键的类：

- Instant——它代表的是时间戳（因为它代表了一个确定的时间点，即相对于1970年1月1日的偏移量；但与java.util.Date类不同的是其精确到了纳秒级别。
- Duration：持续时间，时间差
- LocalDate——不包含具体时间的日期，比如2019-01-14。它可以用来存储生日，周年纪念日，入职日期等。
- LocalTime——它代表的是不含日期的时间
- LocalDateTime——它包含了日期及时间，不过还是没有偏移信息或者说时区。
- Period：时间段
- ZoneOffset：时区偏移量，比如：+8:00
- ZonedDateTime——这是一个包含时区的完整的日期时间，偏移量是以UTC/格林威治时间为基准的。
- Clock：时钟，比如获取目前美国纽约的时间

# java8时间API特性
- 不变性：新的日期/时间API中，所有的类都是不可变的，这对多线程很有好处。
- 关注点分离：借鉴了Joda库的一些优点，新的API将人可读的日期时间和机器时间（unix timestamp）明确分离，它为日期（Date）、时间（Time）、日期时间（DateTime）、时间戳（unix timestamp）以及时区定义了不同的类。
- 清晰：在所有的类中，方法都被明确定义用以完成相同的行为。例如要拿到当前实例我们可以使用now()方法，在所有的类中都定义了format()和parse()方法，而不是像以前那样专门有一个独立的类。为了更好的处理问题，所有的类都使用了工厂模式和策略模式，一旦你使用了其中某个类的方法，与其他类协同工作并不困难。
- 实用操作：所有新的日期/时间API类都实现了一系列方法用以完成通用的任务，如：加、减、格式化、解析、从日期/时间中提取单独部分，等等。
- 可扩展性：新的日期/时间API是工作在ISO-8601日历系统上的，但我们也可以将其应用在非IOS的日历上。

# java8日期/时间API包
- java.time包：这是新的Java日期/时间API的基础包，所有的主要基础类都是这个包的一部分，如：LocalDate, LocalTime, LocalDateTime, Instant, Period, Duration等等。所有这些类都是不可变的和线程安全的，在绝大多数情况下，这些类能够有效地处理一些公共的需求。

- java.time.chrono包：这个包为非ISO的日历系统定义了一些泛化的API，我们可以扩展AbstractChronology类来创建自己的日历系统。
java.time.format包：这个包包含能够格式化和解析日期时间对象的类，在绝大多数情况下，我们不应该直接使用它们，因为java.time包中相应的类已经提供了格式化和解析的方法。

- `java.time.temporal`包：temporal(时态)。这个包包含一些时态对象，我们可以用其找出关于日期/时间对象的某个特定日期或时间，比如说，可以找到某月的第一天或最后一天。你可以非常容易地认出这些方法，因为它们都具有“withXXX”的格式。

- java.time.zone包：这个包包含支持不同时区以及相关规则的类。






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

# API介绍


## LocalDate
LocalDate 依然是一个不可变类，它关注时间中年月日部分

```

public static LocalDate now()：截断当前系统时间的年月日信息并初始化一个实例对象
public static LocalDate of(int year, int month, int dayOfMonth)：显式指定年月日信息
public static LocalDate ofYearDay(int year, int dayOfYear)：根据 dayOfYear 可以推出 month 和 dayOfMonth
public static LocalDate ofEpochDay(long epochDay)：相对于格林零时区时间的日偏移量
…………

```

示例：

```
// 取当前日期：
LocalDate today = LocalDate.now(); // -> 2019-01-31
// 根据年月日取日期，12月就是12：
LocalDate crischristmas = LocalDate.of(2018, 12, 25); // -> 2018-12-25
// 根据字符串取：
LocalDate endOfFeb = LocalDate.parse("2018-12-25"); // 严格按照ISO yyyy-MM-dd验证，02写成2都不行，当然也有一个重载方法允许自己定义格式

// 如何获取1周后的日期
LocalDate oneToday = today.plus(1, ChronoUnit.WEEKS); // ->2019-02-07
//一年前的日期
LocalDate previousYear = today.minus(1, ChronoUnit.YEARS);
// 取本月第1天：
LocalDate firstDayOfThisMonth = today.with(TemporalAdjusters.firstDayOfMonth()); // 2019-01-01
// 取本月第2天：
LocalDate secondDayOfThisMonth = today.withDayOfMonth(2); // 2019-01-02
// 取本月最后一天，再也不用计算是28，29，30还是31：
LocalDate lastDayOfThisMonth = today.with(TemporalAdjusters.lastDayOfMonth()); // 2019-01-31
// 取下一天：
LocalDate firstDay = lastDayOfThisMonth.plusDays(1); // 变成了2019-02-01
// 取2019年1月第一个周一
LocalDate firstMonday = LocalDate.parse("2019-01-01").with(TemporalAdjusters.firstInMonth(DayOfWeek.MONDAY)); // 2019-01-07
```

## TemporalAdjuster
但是有些时候我们要面临更复杂的时间操作，比如将时间调到下一个工作日，或者是下个月的最后一天，这时候我们可以使用with()方法的另一个重载方法，它接收一个TemporalAdjuster参数，可以使我们更加灵活的调整日期：

```
LocalDate date7 = date.with(nextOrSame(DayOfWeek.SUNDAY));      // 返回下一个距离当前时间最近的星期日
LocalDate date9 = date.with(lastInMonth(DayOfWeek.SATURDAY));   // 返回本月最后一个星期六
```
如果本身API不满足你的需求，你还可以创建自定义的TemporalAdjuster接口的实现

## LocalTime
类似于 LocalDate，LocalTime 专注于时间的处理，它提供小时，分钟，秒，毫微秒的各种处理

```
初始化LocalTime实例
public static LocalTime now()：根据系统当前时刻获取其中的时间部分内容
public static LocalTime of(int hour, int minute)：显式传入小时和分钟来构建一个实例对象
public static LocalTime of(int hour, int minute, int second)：通过传入时分秒构造实例
public static LocalTime of(int hour, int minute, int second, int nanoOfSecond)：传入时分秒和毫微秒构建一个实例
public static LocalTime ofSecondOfDay(long secondOfDay)：传入一个长整型数值代表当前日已经过去的秒数
public static LocalTime ofNanoOfDay(long nanoOfDay)：传入一个长整型代表当前日已经过去的毫微秒数
```
示例

```
//包含毫秒
LocalTime now = LocalTime.now(); // 11:09:09.240
//不包含毫秒  
LocalTime now = LocalTime.now().withNano(0)); // 11:09:09
//构造时间  
LocalTime zero = LocalTime.of(0, 0, 0); // 00:00:00
LocalTime mid = LocalTime.parse("12:00:00"); // 12:00:00
LocalTime twoHour = now.plusHours(2);
```

## LocalDateTime
LocalDateTime类是LocalDate和LocalTime的结合体，可以通过of()方法直接创建，也可以调用LocalDate的atTime()方法或LocalTime的atDate()方法将LocalDate或LocalTime合并成一个LocalDateTime

```
LocalDateTime ldt1 = LocalDateTime.of(2017, Month.JANUARY, 4, 17, 23, 52);

LocalDate localDate = LocalDate.of(2017, Month.JANUARY, 4);
LocalTime localTime = LocalTime.of(17, 23, 52);
LocalDateTime ldt2 = localDate.atTime(localTime);
LocalDateTime也提供用于向LocalDate和LocalTime的转化：

LocalDate date = ldt1.toLocalDate();
LocalTime time = ldt1.toLocalTime();
```

## Instant
Instant用于表示一个时间戳，它与我们常使用的System.currentTimeMillis()有些类似，不过Instant可以精确到纳秒（Nano-Second），System.currentTimeMillis()方法只精确到毫秒（Milli-Second）。如果查看Instant源码，发现它的内部使用了两个常量，seconds表示从1970-01-01 00:00:00开始到现在的秒数，nanos表示纳秒部分（nanos的值不会超过999,999,999）。Instant除了使用now()方法创建外，还可以通过ofEpochSecond方法创建：

```
Instant instant = Instant.ofEpochSecond(120, 100000);
```

## 时间差
关于时间差的计算，主要涉及到两个类，年月日的日期间差值的计算使用 Period 类足以，而时分秒毫秒的时间的差值计算则需要使用Duration类。

### Duration：处理两个时间之间的差值
Duration的内部实现与Instant类似，也是包含两部分：seconds表示秒，nanos表示纳秒。两者的区别是Instant用于表示一个时间戳（或者说是一个时间点），而Duration表示一个时间段，所以Duration类中不包含now()静态方法。可以通过Duration.between()方法创建

```
LocalDateTime from = LocalDateTime.of(2019, Month.JANUARY, 5, 10, 7, 0);    // 2019-01-05 10:07:00
LocalDateTime to = LocalDateTime.of(2019, Month.FEBRUARY, 5, 10, 7, 0);     // 2019-02-05 10:07:00
Duration duration = Duration.between(from, to);     // 表示从 2019-01-05 10:07:00 到 2019-02-05 10:07:00 这段时间

long days = duration.toDays();              // 这段时间的总天数
long hours = duration.toHours();            // 这段时间的小时数
long minutes = duration.toMinutes();        // 这段时间的分钟数
long seconds = duration.getSeconds();       // 这段时间的秒数
long milliSeconds = duration.toMillis();    // 这段时间的毫秒数
long nanoSeconds = duration.toNanos();      // 这段时间的纳秒数
```

Duration对象还可以通过of()方法创建，该方法接受一个时间段长度，和一个时间单位作为参数：

```
Duration duration1 = Duration.of(5, ChronoUnit.DAYS);       // 5天
Duration duration2 = Duration.of(1000, ChronoUnit.MILLIS);  // 1000毫秒
```

### Period：处理两个日期之间的差值
Period在概念上和Duration类似，区别在于Period是以年月日来衡量一个时间段，比如2年3个月6天

```
Period period = Period.of(2, 3, 6);
```

Period对象也可以通过between()方法创建，值得注意的是，由于Period是以年月日衡量时间段，所以between()方法只能接收LocalDate类型的参数：

```
Period period = Period.between(
            LocalDate.of(2019, 1, 5),
            LocalDate.of(2019, 2, 5));
```
示例


```
LocalDate date = LocalDate.of(2019,01,22);
LocalDate date1 = LocalDate.now();
Period period = Period.between(date,date1);
System.out.println(period.getYears() + "年" +
        period.getMonths() + "月" +
        period.getDays() + "天");

LocalTime time = LocalTime.of(20,30);
LocalTime time1 = LocalTime.of(23,59);
Duration duration = Duration.between(time,time1);
System.out.println(duration.toMinutes() + "分钟");

```

## ZonedDateTime
无论是我们的 LocalDate，或是 LocalTime，甚至是 LocalDateTime，它们基本是时区无关的，内部并没有存储时区属性，而基本用的系统默认时区。往往有些场景之下，缺乏一定的灵活性。

ZonedDateTime 可以被理解为 LocalDateTime 的外层封装，它的内部存储了一个 LocalDateTime 的实例，专门用于普通的日期时间处理。此外，它还定义了 ZoneId 和 ZoneOffset 来描述时区的概念。

ZonedDateTime 和 LocalDateTime 的一个很大的不同点在于，后者内部并没有存储时区，所以对于系统的依赖性很强，往往换一个时区可能就会导致程序中的日期时间不一致。
而后者则可以通过传入时区的名称，使用 ZoneId 进行匹配存储，也可以通过传入与零时区的偏移量，使用 ZoneOffset 存储时区信息。

```
初始化实例
public static ZonedDateTime now()：系统将以默认时区计算并存储日期时间信息
public static ZonedDateTime now(ZoneId zone)：指定时区
public static ZonedDateTime of(LocalDate date, LocalTime time, ZoneId zone)：指定日期时间和时区
public static ZonedDateTime of(LocalDateTime localDateTime, ZoneId zone)
public static ZonedDateTime ofInstant(Instant instant, ZoneId zone)：通过时刻和时区构建实例对象
等等

```

示例

```
ZonedDateTime zonedDateTime = ZonedDateTime.now();
System.out.println(zonedDateTime); 
//->2019-01-31T16:27:23.179+08:00[Asia/Shanghai]

LocalDateTime localDateTime = LocalDateTime.now();
ZoneId zoneId = ZoneId.of("America/Los_Angeles");
ZonedDateTime zonedDateTime1 = ZonedDateTime.of(localDateTime,zoneId);
System.out.println(zonedDateTime1);
// ->2019-01-31T16:27:23.179-08:00[America/Los_Angeles]


Instant instant = Instant.now();
ZoneId zoneId1 = ZoneId.of("GMT");
ZonedDateTime zonedDateTime2 = ZonedDateTime.ofInstant(instant,zoneId1);
System.out.println(zonedDateTime2);
// ->2019-01-31T08:27:23.183Z[GMT]
```

第一个输出应使用了当前系统日期和时间以及默认的时区。
第二个小例子，LocalDateTime 实例保存了时区无关的当前日期时间信息，也就是这里的年月日时分秒，接着构建一个 ZonedDateTime 实例并传入一个美国时区（西七区）。你会发现输出的日期时间为西七区的 16 点 27 分。

像这种关联了时区的日期时间就很能够解决那种，换时区导致程序中时间错乱的问题。因为我关联了时区，无论你程序换到什么地方运行了，日期+时区 本就已经唯一确定了某个时刻，就相当于我在存储某个时刻的时候，说明了这是某某时区的某某时间，即便你换了一个地区，也不至于把这个时间按自己当前的时区进行解析并直接使用。

第三个小例子，构建 ZonedDateTime实例的时候，给定一个时刻和一个时区，而这个时刻值就是相对于给定时区的标准时间所经过的毫秒数。

有关 ZonedDateTime 的其他日期时间的处理方法和 LocalDateTime 是一样的，因为 ZonedDateTime 是直接封装了一个 LocalDateTime 实例对象，所以所有相关日期时间的操作都会间接的调用 LocalDateTime 实例的方法，我们不再赘述。

## 格式化日期时间
Java 8 的新式日期时间 API 中，DateTimeFormatter 作为格式化日期时间的主要类，它与之前的 DateFormat 类最大的不同就在于它是线程安全的,如果需要的话，可以赋值给一个静态变量。

DateTimeFormatter类提供了许多预定义的格式器，你也可以自定义自己想要的格式。当然根据约定，它还有一个parse()方法是用于将字符串转换成日期的，如果转换期间出现任何错误，它会抛出DateTimeParseException异常。类似的，DateFormatter类也有一个用于格式化日期的format()方法，它出错的话则会抛出DateTimeException异常

再说一句，“MMM d yyyy”与“MMm dd yyyy”这两个日期格式也略有不同，前者能识别出"Jan 2 2018"与"Jan 14 2018"这两个串，而后者如果传进来的是"Jan 2 2018"则会报错，因为它期望月份处传进来的是两个字符。为了解决这个问题，在天为个位数的情况下，你得在前面补0，比如"Jan 2 2018"应该改为"Jan 02 2018"。

```
public static void main(String[] a){
    DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy年MM月dd日 HH:mm:ss");
    LocalDateTime localDateTime = LocalDateTime.now();
    System.out.println(formatter.format(localDateTime));

    String str = "2008年08月23日 23:59:59";
    DateTimeFormatter formatter2 = DateTimeFormatter.ofPattern("yyyy年MM月dd日 HH:mm:ss");
    LocalDateTime localDateTime2 = LocalDateTime.parse(str,formatter2);
    System.out.println(localDateTime2);

}
```

## java8 时间与老版本时间转换
因为java8之前Date是包含日期和时间的，而LocalDate只包含日期，LocalTime只包含时间，所以与Date在互转中，势必会丢失日期或者时间，或者会使用起始时间。如果转LocalDateTime，那么就不存在信息误差。

```
//Date与Instant的相互转化
Instant instant  = Instant.now();
Date date = Date.from(instant);
Instant instant2 = date.toInstant();
        
//Date转为LocalDateTime
Date date2 = new Date();
LocalDateTime localDateTime2 = LocalDateTime.ofInstant(date2.toInstant(), ZoneId.systemDefault());
        
//LocalDateTime转Date
LocalDateTime localDateTime3 = LocalDateTime.now();
Instant instant3 = localDateTime3.atZone(ZoneId.systemDefault()).toInstant();
Date date3 = Date.from(instant);

//LocalDate转Date
//因为LocalDate不包含时间，所以转Date时，会默认转为当天的起始时间，00:00:00
LocalDate localDate4 = LocalDate.now();
Instant instant4 = localDate4.atStartOfDay().atZone(ZoneId.systemDefault()).toInstant();
Date date4 = Date.from(instant);


// Calendar to Instant
Instant time = Calendar.getInstance().toInstant();
System.out.println(time);

// TimeZone to ZoneId
ZoneId defaultZone = TimeZone.getDefault().toZoneId();
System.out.println(defaultZone);
 
// ZonedDateTime from specific Calendar
ZonedDateTime gregorianCalendarDateTime = new GregorianCalendar().toZonedDateTime();
System.out.println(gregorianCalendarDateTime);
 
 
GregorianCalendar gc = GregorianCalendar.from(gregorianCalendarDateTime);
System.out.println(gc);
```

# 参考文献
* [Java8 日期、时间操作](https://www.cnblogs.com/ark-blog/p/9694950.html)
* [java8 时间使用](https://www.cnblogs.com/guozp/p/10342775.html)
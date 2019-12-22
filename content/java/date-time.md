---
layout: default
title:  "Date & Time"
---

# Date & Time
{:.no_toc}

* TOC
{:toc}

## Overview
- Found in [java.time](https://docs.oracle.com/javase/8/docs/api/java/time/compact1-package-summary.html)
- See also: [The Java™ Tutorials Overview](https://docs.oracle.com/javase/tutorial/datetime/iso/overview.html)

### Cheat Sheet
Following table taken from [this](https://docs.oracle.com/javase/tutorial/datetime/iso/overview.html) page.
<table style="font-size: 75%">
    <tbody>
        <tr>
            <th>Class or Enum</th>
            <th>Year</th>
            <th>Month</th>
            <th>Day</th>
            <th>Hours</th>
            <th>Minutes</th>
            <th>Seconds*</th>
            <th>Zone Offset</th>
            <th>Zone ID</th>
            <th>toString Output</th>
        </tr>
        <tr>
            <td><tt>Instant</tt></td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>
            <center>✓</center></td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td><tt>2013-08-20T15:16:26.355Z</tt></td>
        </tr>
        <tr>
            <td><tt>LocalDate</tt></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td><tt>2013-08-20</tt></td>
        </tr>
        <tr>
            <td><tt>LocalDateTime</tt></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td><tt>2013-08-20T08:16:26.937</tt></td>
        </tr>
        <tr>
            <td><tt>ZonedDateTime</tt></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td><tt>2013-08-21T00:16:26.941+09:00[Asia/Tokyo]</tt></td>
        </tr>
        <tr>
            <td><tt>LocalTime</tt></td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td><tt>08:16:26.943</tt></td>
        </tr>
        <tr>
            <td><tt>MonthDay</tt></td>
            <td>&nbsp;</td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td><tt>--08-20</tt></td>
        </tr>
        <tr>
            <td><tt>Year</tt></td>
            <td>
            <center>✓</center></td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td><tt>2013</tt></td>
        </tr>
        <tr>
            <td><tt>YearMonth</tt></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td><tt>2013-08</tt></td>
        </tr>
        <tr>
            <td><tt>Month</tt></td>
            <td>&nbsp;</td>
            <td>
            <center>✓</center></td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td><tt>AUGUST</tt></td>
        </tr>
        <tr>
            <td><tt>OffsetDateTime</tt></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>&nbsp;</td>
            <td><tt>2013-08-20T08:16:26.954-07:00</tt></td>
        </tr>
        <tr>
            <td><tt>OffsetTime</tt></td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>&nbsp;</td>
            <td><tt>08:16:26.957-07:00</tt></td>
        </tr>
        <tr>
            <td><tt>Duration</tt></td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td align="center">**</td>
            <td align="center">**</td>
            <td align="center">**</td>
            <td>
            <center>✓</center></td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td><tt>PT20H</tt> (20 hours)</td>
        </tr>
        <tr>
            <td><tt>Period</tt></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>
            <center>✓</center></td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td>&nbsp;</td>
            <td align="center">***</td>
            <td align="center">***</td>
            <td><tt>P10D</tt> (10 days)</td>
        </tr>
    </tbody>
</table>

```text
*   Seconds are captured to nanosecond precision.
**  This class does not store this information, but has methods to provide time in these units.
*** When a Period is added to a ZonedDateTime, daylight saving time or other local time differences are observed.
```

### Date & Time Constants
#### ChronoUnit
```java
enum ChronoUnit implements TemporalUnit {
    NANOS, MICROS, MILLIS, SECONDS, MINUTES, HOURS, HALF_DAYS, DAYS, WEEKS, MONTHS, YEARS, DECADES, CENTURIES, 
    MILLENNIA, ERAS, FOREVER
}
```

#### ChronoField
```java
enum ChronoField implements TemporalField {
    NANO_OF_SECOND, NANO_OF_DAY, MICRO_OF_SECOND, MICRO_OF_DAY, MILLI_OF_SECOND, MILLI_OF_DAY, SECOND_OF_MINUTE, 
    SECOND_OF_DAY, MINUTE_OF_HOUR, MINUTE_OF_DAY, HOUR_OF_AMPM, CLOCK_HOUR_OF_AMPM, HOUR_OF_DAY, 
    CLOCK_HOUR_OF_DAY, AMPM_OF_DAY, DAY_OF_WEEK, ALIGNED_DAY_OF_WEEK_IN_MONTH, ALIGNED_DAY_OF_WEEK_IN_YEAR, 
    DAY_OF_MONTH, DAY_OF_YEAR, EPOCH_DAY, ALIGNED_WEEK_OF_MONTH, ALIGNED_WEEK_OF_YEAR, MONTH_OF_YEAR, 
    PROLEPTIC_MONTH, YEAR_OF_ERA, YEAR, ERA, INSTANT_SECONDS, OFFSET_SECONDS    
}
```
See also: [Difference between ChronoUnit and ChronoField](https://stackoverflow.com/a/41572717/1173112)

## Instant
- Represents a point on the time line
- An origin, called the 'epoch' is arbitrarily set at midnight of January 1, 1970 at the prime meridian that passes through the Greenwich Royal Observatory in London
    - That meridian turns out to be in UTC
- An `Instant` is a moment on the timeline in UTC, a count of nanoseconds since the epoch 
    - Since most of your business logic, data storage, and data exchange should be in UTC, this is a handy class to be used

### Creating an Instant
```java
// Capture the current moment in UTC
Instant.now();              // 2018-11-28T01:51:48.847Z

// Get the epoch
Instant.EPOCH;              // 1970-01-01T00:00:00Z

// Reminder: 1000 milliseconds = 1 second
Instant.ofEpochMilli(1000); // 1970-01-01T00:00:01Z
```
#### Further Reading
- [Java 8 Instant.now() with nanosecond resolution?](https://stackoverflow.com/questions/20689055)

#### Creating an Instant from a Temporal Object
```java
// ZonedDateTime is supported
Instant.from(ZonedDateTime.now()); // 2018-11-29T03:38:33.905Z

// LocalDateTime is not supported
Instant.from(LocalDateTime.now()); // UnsupportedTemporalTypeException: Unsupported field: InstantSeconds
```

### Updating an Instant
__Heads Up!__ `java.time.Instant` is an immutable class an update methods return new instances instead of updating the instance itself.
```java
// I find it interesting that this compiles and runs fine
Instant now = Instant.now();
now = now.plus(1, ChronoUnit.DAYS); // 2019-01-14T16:19:07.939Z
```

## Duration
> A Duration measures an amount of time using time-based values, i.e. hours, minutes, seconds..
>
> [The Java™ Tutorials](https://docs.oracle.com/javase/tutorial/datetime/iso/period.html)

### Creating a Duration
#### Creating a Duration with Constants
```java
// Creating a Duration with ofNanos, ofSeconds, ofMillis, ofMinutes, ofHours, ofDays
Duration.ofNanos(1); // PT0.000000001S
Duration.ofDays(1);  // PT24H
```
#### Creating a Duration from Duration Between Temporals
```java
// Using Instant
Duration.between(Instant.EPOCH, Instant.now()); // PT428714H19M29.54S

// Using LocalTime
LocalTime now = LocalTime.now();
Duration.between(now.minusHours(1).minusMinutes(30).minusSeconds(15), now); // PT1H30M15S

// LocalDate is NOT supported
// Duration.between(LocalDate.now(), LocalDate.now())
// java.time.temporal.UnsupportedTemporalTypeException: Unsupported unit: Seconds
```
### Reading a Duration
```java
Duration d = Duration.ofDays(1).plus(Duration.ofHours(12)).plus(Duration.ofMinutes(30));
d.toDays();  // Returns 1, do not expect 1.5!
d.toHours(); // Returns 36! Not 12! Not 36.5! 
// Also available: toNanos(), toMillis(), getSeconds(), toMinutes()
```
[Why can't I get a duration in minutes or hours in java.time?](https://stackoverflow.com/a/24500045)
> .. that `get(TemporalUnit)` is essentially a framework-level method - it is not designed for day-today use. 
> Unfortunately the method name get does not imply this, resulting in bugs like calling `get(ChronoUnit.MINUTES)` on Duration

### Updating a Duration
```java
// Reminder: a new instance is returned
Duration d = Duration.ofHours(1);
d = d.plusMinutes(30); // PT1H30M
```

### Using Duration as TemporalAmount
Duration extends [TemporalAmount](https://docs.oracle.com/javase/8/docs/api/java/time/temporal/TemporalAmount.html) which is used in various places in the API.
```java
// Example with Instant.plus(TemporalAmount)
Duration dayz = Duration.ofDays(20000); // 20000 days
Instant.EPOCH.plus(dayz);               // 2024-10-04T00:00:00Z

// Example with LocalTime.plus(TemporalAmount)
LocalTime localTime = LocalTime.of(12, 0);
Duration halfAnHour = Duration.ofMinutes(30);
localTime = localTime.plus(halfAnHour)  // 12:30
```

## Period
> A Period measures an amount of time using date-based values, i.e. years, months, days..
>
> [The Java™ Tutorials](https://docs.oracle.com/javase/tutorial/datetime/iso/period.html)

Why can I not get number of calendar days from a `Period`?

> .. it is not possible from a Period to deduce the actual number of calendar days in the period. A Period is not tied to specific dates, once constructed in the way you show, it loses track of the actual calendar dates.
>
> For example your first period represents a period of 1 month and 1 day. But the period does not care which month. It is simply a concept of "a month and a day".
>
> To get number of days between two dates, use `ChronoUnit.DAYS.between(Temporal, Temporal)` ..
>
> [How to calculate the number of days in a period? - StackOverflow](https://stackoverflow.com/a/30833785/1173112)

### Creating a Period
```java
// Period of years, months, days
Period.of(1, 1, 1); // P1Y1M1D
Period.ofDays(45);  // P45D
// also available: ofMonths, ofYears
```

#### Creationg a Period using LocalDates
```java
Period.between(LocalDate.now(), LocalDate.now().plusDays(1)); // P1D
// You can only use LocalDates, not LocalDateTimes, not ZonedDateTimes
```

#### A Small Brain Teaser
Does the following code compile? Run without any exceptions? What is the returned instance? (A period of .. ..?)
```java
Period.ofYears(1).ofMonths(1).ofDays(1);
```

## LocalDate
- Represents a plain date without any time or time zone information
- Birthdays, holidays, schedule times usually best represented as local dates or times

### Creating a LocalDate
```java
LocalDate.now();                // 2018-11-25
LocalDate.of(1984, 9, 3);       // 1984-09-03
LocalDate.parse("1984-09-03");  // 1984-09-03
// There is no direct link between an Instant and a LocalDate
```
### Reading a LocalDate
#### Reading Day Related Information
```java
LocalDate ld = LocalDate.of(1984, 9, 3);
ld.getDayOfWeek();  // DayOfWeek.MONDAY
ld.getDayOfMonth(); // 3
ld.getDayOfYear();  // 247
```
#### Reading Month Related Information
```java
LocalDate ld = LocalDate.of(1984, 9, 3);
ld.getMonth();      // Month.SEPTEMBER
ld.getMonthValue(); // 9
```
#### Reading Various Properties from a LocalDate
```java
LocalDate ld = LocalDate.of(1984, 9, 3);
ld.lengthOfMonth(); // 30
ld.isLeapYear();    // true
ld.lengthOfYear();  // 366 
```

### Finding Duration Between LocalDates using ChronoUnit
```java
ChronoUnit.DAYS.between(LocalDate.now(), LocalDate.now().minusDays(1)); // -1L
```

### Updating a LocalDate
__Heads Up!__ `java.time.LocalDate` is an immutable class an update methods return new instances instead of updating the instance itself.
```java
LocalDate ld = LocalDate.of(1983, 9, 3);
ld.plusYears(1);    // 1984-09-03 ➤ This is most likely what you want
ld.plusDays(365);   // 1984-09-02 ➤ It is not the 3rd of 84 due to leap year

// Using Period
ld.plus(Period.ofYears(1));  // 1984-09-03 ➤ This is most likely what you want
ld.plus(Period.ofDays(365)); // 1984-09-02 ➤ It is not the 3rd of 84 due to leap year

// Using withYear
ld.withYear(1984); // 1984-09-03  ➤ This is most likely what you want

// Using plus with ChronoUnit.YEARS
ld.plus(1, ChronoUnit.YEARS); // 1984-09-03  ➤ This is most likely what you want

// Trying to update a LocalDate with a Duration will throw a Runtime Exception
LocalDate.now().plus(Duration.ofDays(1));
// java.time.temporal.UnsupportedTemporalTypeException: Unsupported unit: Seconds
```

### Comparing LocalDates
```java
LocalDate.now().equals(LocalDate.now());  // true
LocalDate.now().isEqual(LocalDate.now()); // true
```

## LocalTime
- Represents a plain time without any date or time zone information

### Creating a LocalTime
#### Creating a new LocalTime
```java
LocalTime.of(22, 30);     // 22:30
LocalTime.of(22, 30, 45); // 22:30:45
LocalTime.parse("22:30"); // 22:30
LocalTime.now();          // 22:57:36.162
```

## LocalDateTime
### Creating a LocalDateTime
```java
LocalDateTime.now();
LocalDateTime.parse("2019-01-01T00:00:00");
LocalDateTime.parse("2019-01-01 00:00:00", DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"));
```
#### Creating a LocalDateTime using an Instant
- Note how you need to pass a time zone even though a `LocalDateTime` has no time zone information
  - The passed time zone will determine be used for calculating the offset to `ZoneOffset.UTC` 
```java
Instant now = Instant.now();
LocalDateTime.ofInstant(now, ZoneOffset.UTC);               // 2018-12-23T15:22:27.016
LocalDateTime.ofInstant(now, ZoneId.of("America/Toronto")); // 2018-12-23T10:22:27.016
```
### Formatting a LocalDateTime
```java
LocalDateTime l = LocalDateTime.parse("2019-01-01 00:00:00", DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"));
l.format(DateTimeFormatter.ofPattern("yy/MM/dd HH:mm")); // String 19/01/01 00:00
```
### Modifying a LocalDateTime
```java
LocalDateTime ldt = LocalDateTime.of(2018, 1, 1, 12, 0, 0);
ldt = ldt.plusDays(1);  // 2018-01-02T12:00
ldt = ldt.plusHours(1); // 2018-01-02T13:00
```
## ZonedDateTime
- All time zones are based on _Greenwich Mean Time(GMT)_
- _GMT_ is a time zone
- The name of the time standard that uses GMT as the basis for all other time zones is _Coordinated Universal Time(UTC)_

### Creating a ZonedDateTime
```java
ZonedDateTime.now();                            // 2018-11-28T23:30:08.454-05:00[America/Toronto]
ZonedDateTime.now(Clock.systemDefaultZone());   // 2018-11-28T23:30:08.456-05:00[America/Toronto]
ZonedDateTime.now(Clock.systemUTC());           // 2018-11-29T04:30:08.456Z

ZonedDateTime.of(2018, 1, 1, 12, 0, 0, 0, ZoneId.systemDefault()); // 2018-01-01T12:00-05:00[America/Toronto]
// Remember ZonedDateTime.of requires nanoseconds
```
#### Using parse for Creating ZonedDateTimes
```java
ZonedDateTime parse = ZonedDateTime.parse("2018-01-01T08:00:00Z");
parse.getZone(); // Z
// Z stands for Zulu Timezone, which is +0 offset from GMT

ZonedDateTime parse = ZonedDateTime.parse("2018-01-01T08:00:00-05:00");
parse.getZone(); // -05:00
// I guess that is a fixed offset and not a real timezone..

ZonedDateTime parse = ZonedDateTime.parse("2018-01-01T08:00:00-00:00[America/Toronto]");
// 2018-01-01T08:00-05:00[America/Toronto]
parse.getZone();  // America/Toronto
// Note how the offset passed in the String (00:00) is ignored by Java!  
```

#### ZonedDateTime from LocalDate
```java
LocalDate ld = LocalDate.of(1984, 9, 3);
// Basically attaching time zone information to a particular date
ld.atStartOfDay(ZoneId.of("America/Toronto")); // 1984-09-03T00:00-04:00[America/Toronto]
ld.atStartOfDay(ZoneId.of("Europe/Istanbul")); // 1984-09-03T00:00+03:00[Europe/Istanbul]
```

### Finding Time in a Different Zone
#### Using withZoneSameInstant
```java
ZonedDateTime zdt = ZonedDateTime.now();               // 2018-12-23T10:41:45.322-05:00[America/Toronto]
zdt.withZoneSameInstant(ZoneId.of("Europe/Istanbul")); // 2018-12-23T18:41:45.322+03:00[Europe/Istanbul]
```
#### Using an Instant
```java
ZonedDateTime zdt = ZonedDateTime.now();               // 2018-12-23T10:41:45.322-05:00[America/Toronto]
zdt.toInstant().atZone(ZoneId.of("Europe/Istanbul"));  // 2018-12-23T10:41:45.322-05:00[America/Toronto]
```

### Updating a ZonedDateTime
- Adding a `Duration` to `ZonedDateTime` will take any `daylight savings` into account
  - Note how the `ZoneOffset` changes in the following example
```java
// DayLightSavings time for Toronto in 2019: Sunday, March 10, 2:00 am
ZonedDateTime zdt = 
ZonedDateTime.of(
    LocalDate.of(2019, 3, 10),
    LocalTime.of(1, 0),
    ZoneId.of("America/Toronto"));    // 2019-03-10T01:00-05:00[America/Toronto]
zdt = zdt.plus(Duration.ofHours(1));  // 2019-03-10T03:00-04:00[America/Toronto]
```
Another example for observing the above behavior
```java
LocalDateTime ldt = LocalDateTime.of(2019, 3, 10, 0, 0, 0);
ZoneId zoneId = ZoneId.of("America/Toronto");
ZonedDateTime.of(ldt, zoneId); // 2019-03-10T00:00-05:00[America/Toronto]
ldt = ldt.plusDays(1);
ZonedDateTime.of(ldt, zoneId); // 2019-03-11T00:00-04:00[America/Toronto]
```

## TemporalAdjuster
- Example usage: `temporal = temporal.with(temporalAdjuster);`
- Built-in TemporalAdjusters found in: [TemporalAdjusters](https://docs.oracle.com/javase/8/docs/api/java/time/temporal/TemporalAdjusters.html)
```java
class NextWorkingDay implements TemporalAdjuster {
    @Override
    public Temporal adjustInto(Temporal temporal) {
        LocalDate nw = ((LocalDate) temporal).plusDays(1);
        while (nw.getDayOfWeek() == DayOfWeek.SATURDAY || nw.getDayOfWeek() == DayOfWeek.SUNDAY) {
            nw = nw.plusDays(1);
        }
        return nw;
    }
}

class FirstTuesdayOfAMonth implements TemporalAdjuster {
    @Override
    public Temporal adjustInto(Temporal temporal) {
        LocalDate ld = ((LocalDate) temporal).with(TemporalAdjusters.firstDayOfMonth());
        while (ld.getDayOfWeek() != DayOfWeek.TUESDAY) {
            ld = ld.plusDays(1);
        }
        return ld;
    }    
}

LocalDate.now().with(new NextWorkingDay());
LocalDate.now().with(new FirstTuesdayOfAMonth());
```

## DateTimeFormatter
- Found in [java.time.format.DateTimeFormatter](https://docs.oracle.com/javase/8/docs/api/java/time/format/DateTimeFormatter.html)
- Used for parsing _and_ formatting
- Can be combined with `Locale`s
### Formatting with DateTimeFormatter
```java
LocalDateTime ldt = LocalDateTime.now();
ldt.format(DateTimeFormatter.ofPattern("dd/MM/yy")); // 13/02/19
```
#### Formatting with DateTimeFormatter using predefined FormatStyles and custom Locales
```java
// Uses the system default locale with short format style
LocalDateTime ldt = LocalDateTime.now();
ldt.format(DateTimeFormatter.ofLocalizedDateTime(FormatStyle.SHORT));
// 13/02/19 8:13 PM

// Overrides with a specific Locale
LocalDateTime ldt = LocalDateTime.now();
ldt.format(DateTimeFormatter.ofLocalizedDateTime(FormatStyle.SHORT).withLocale(new Locale("tr"))); 
// 13.02.2019 20:00

ZonedDateTime ldt = ZonedDateTime.now().withZoneSameInstant(ZoneId.of("Europe/Istanbul"));
ldt.format(DateTimeFormatter.ofLocalizedDateTime(FormatStyle.LONG).withLocale(new Locale("tr"))) ;
// 14 Şubat 2019 Perşembe 04:04:43 EET

// Heads UP! There are also ofLocalizedDate and ofLocalizedTime methods!
```

## Examples
__Daylight Saving Times__
```java
final Set<ZonedDateTime> dayLightSavingZonedDateTimes = new HashSet<>();

ZoneId.getAvailableZoneIds().forEach(zoneId -> {
    LocalDateTime dateTime = LocalDateTime.of(LocalDate.of(2018, 1, 1), LocalTime.of(0, 0, 0));
    ZonedDateTime now = ZonedDateTime.of(dateTime, ZoneId.of(zoneId));
    while (2018 == now.getYear()) {
        int hour = now.getHour();
        now = now.plusHours(1);
        if (now.getHour() == hour) {
            dayLightSavingZonedDateTimes.add(now);
        }
    }
});

dayLightSavingZonedDateTimes.stream().limit(5).forEach(time -> System.out.println(time));

// 2018-11-04T01:00-05:00[America/Indiana/Petersburg]
// 2018-11-04T01:00-10:00[US/Aleutian]
// 2018-10-28T02:00+01:00[Europe/Brussels]
// 2018-10-28T03:00+02:00[Europe/Sofia]
// 2018-10-28T02:00+01:00[Europe/Vienna]
```
__Day of the Programmer__
```java
// Print the Day of the Programmer (the 256th day of the year) for 2011 and 2012
IntStream.rangeClosed(2011, 2012).forEach(year -> {
    LocalDate programmersDay = LocalDate.of(year, 1, 1).plusDays(255);
    System.out.println(programmersDay);
});

// 2011-09-13
// 2012-09-12
```

__Number of Days until Next Birthday__
```java
LocalDate today = LocalDate.now();
LocalDate nextBirthday = LocalDate.of(1984, 9, 3).withYear(today.getYear());

if (ChronoUnit.DAYS.between(today, nextBirthday) <= 0) // If birthday this year passed already
    nextBirthday = nextBirthday.plusYears(1);

ChronoUnit.DAYS.between(today, nextBirthday); // long
```

__Find the Time Difference Changes Between Toronto and London for 2018__
```java
ZoneId london = ZoneId.of("Europe/London");
ZoneId toronto = ZoneId.of("America/Toronto");

LocalDate ld = LocalDate.of(2018, 1, 1);
LocalTime lt = LocalTime.of(0, 0);   
LocalDateTime ldt = LocalDateTime.of(ld, lt);

ZonedDateTime ldtLondon = ZonedDateTime.of(ldt, london);
ZonedDateTime ldtToronto = ZonedDateTime.of(ldt, toronto);
long hoursBetween = ChronoUnit.HOURS.between(ldtLondon, ldtToronto);

System.out.println("Time difference: " + hoursBetween);
System.out.println(ldtToronto);
System.out.println(ldtLondon);
System.out.println("");

while (2018 == ldt.getYear()) {
    ldt = ldt.plusHours(1);
    ldtLondon = ZonedDateTime.of(ldt, london);
    ldtToronto = ZonedDateTime.of(ldt, toronto);
    long hours = ChronoUnit.HOURS.between(ldtLondon, ldtToronto);
    if (hoursBetween != hours) {
        System.out.println("Time difference changed from " + hoursBetween + " to " + hours);
        System.out.println(ldtToronto);
        System.out.println(ldtLondon);
        System.out.println("");
        hoursBetween = hours;
    }
}
```
Output
```markdown
Time difference: 5
2018-01-01T00:00-05:00[America/Toronto]
2018-01-01T00:00Z[Europe/London]

Time difference changed from 5 to 4
2018-03-11T03:00-04:00[America/Toronto]
2018-03-11T03:00Z[Europe/London]

Time difference changed from 4 to 5
2018-03-25T02:00-04:00[America/Toronto]
2018-03-25T02:00+01:00[Europe/London]

Time difference changed from 5 to 4
2018-10-28T02:00-04:00[America/Toronto]
2018-10-28T02:00Z[Europe/London]

Time difference changed from 4 to 5
2018-11-04T02:00-05:00[America/Toronto]
2018-11-04T02:00Z[Europe/London]
```
- Toronto forwards the clock before London
- Toronto backwards the clock after London
- Toronto stays in daylight saving longer than London
- London is not always in time zone GMT, it uses GMT and GMT+1
- Toronto is either in time zone GMT-5 or GMT-4

__Find Next Particular Weekday__
```java
LocalDate now = LocalDate.now(); // It is Tuesday
LocalDate with = now.with(TemporalAdjusters.next(DayOfWeek.THURSDAY));
System.out.println(ChronoUnit.DAYS.between(now, with)); // 2
```

__Find the Timezone That Stays Longest (or shortest) in DST__
```java
TreeMap<Long,String> periodZoneIdTreeMap = new TreeMap<>(Comparator.reverseOrder());

LocalDateTime janFirst = LocalDateTime.now().with(TemporalAdjusters.firstDayOfYear());
ZoneId.getAvailableZoneIds().forEach(id -> {
    ZonedDateTime zdt = ZonedDateTime.of(janFirst, ZoneId.of(id));
    List<LocalDate> timeChanges = new ArrayList<>();
    while (zdt.getYear() == janFirst.getYear()) {
        if (!zdt.getOffset().equals(zdt.plusHours(1).getOffset())) {
            timeChanges.add(zdt.toLocalDate());
        }
        zdt = zdt.plusHours(1);
        if (timeChanges.size() > 1) {
            long between = ChronoUnit.DAYS.between(timeChanges.get(0), timeChanges.get(1));
            periodZoneIdTreeMap.put(between, id);
            break;
        }
    }
});

periodZoneIdTreeMap.entrySet().iterator().next(); // 294=Pacific/Fiji
// Most Recent DST Transition
// DST ended on Sun 20-Jan-2019 at 02:00:00 A.M. when local clocks were set backward 1 hour
   
// Next Scheduled DST Transition
// DST will begin on Sun 27-Oct-2019 at 02:00:00 A.M. when local clocks are set forward 1 hour
```

## References
- [Oracle Technology Network - Java SE 8 Date and Time](https://www.oracle.com/technetwork/articles/java/jf14-date-time-2125367.html)
- [What's the difference between Instant and LocalDateTime?](https://stackoverflow.com/a/32443004)
---
title: Java开发常用工具类
date: 2018-04-03 21:47:10
tags:
  - Java
categories:
  - 工具
---

包括Apache 的Commons、 Google 的Guava、以及处理时间日期的Joda，以及自己写的一些工具类。

<!--more -->

## <font color = "#159957"> 项目中整理的工具类 </font>

总结最近项目使用的工具类，更多的后续整理到Github上。

```Java
import java.sql.Date;
import java.sql.Timestamp;
import java.util.*;

/** 常用方法
 * Created by Bran on 2018/4/2.
 */
public class Tools {

    /**
     * 返回当前时间戳
     * @return
     */
    public static Timestamp now() {
        Calendar calendar = Calendar.getInstance( );
        return new Timestamp(calendar.getTimeInMillis( ));
    }

    /**
     * 返回当前日期
     * @return
     */
    public static Date currentDate(){
        Calendar calendar = Calendar.getInstance( );
        //设置时分秒
        calendar.set(Calendar.HOUR_OF_DAY, 0);
        calendar.set(Calendar.MINUTE, 0);
        calendar.set(Calendar.SECOND, 0);
        calendar.set(Calendar.MILLISECOND, 0);
        return new Date(calendar.getTimeInMillis( ));
    }

    /**
     * 判断String是否为空
     * @param str
     * @return
     */
    public static boolean isEmpty(String str) {
        //默认为空
        boolean result = true;
        if (str != null && !str.isEmpty( ) && !str.trim( ).equalsIgnoreCase("null")) {
            result = false;
        }
        return result;
    }

    /**
     * 判断List是否为空
     * @param list
     * @return
     */
    public static boolean isEmpty(List list) {
        boolean result = true;
        if (list != null && !list.isEmpty( )) {
            result = false;
        }
        return result;
    }

    /**
     * 判断Map是否为空
     * @param map
     * @return
     */
    public static boolean isEmpty(Map map) {
        boolean result = true;
        if (map != null && !map.isEmpty( )) {
            result = false;
        }
        return result;
    }

    /**
     * 判断String是否在某些String中
     * in("0B","0A","0B","0C")=true
     * @param str
     * @param strings
     * @return
     */
    public static boolean in(String str, String... strings) {
        for (String string : strings) {
            if (string.equals(str)) {
                return true;
            }
        }
        return false;
    }

    public static boolean notIn(String str, String... strings) {
        return !Tools.in(str, strings);
    }

    /**
     * 去重
     * @param list
     * @return
     */
    public static List<String> cleanRepeat(List<String> list) {
        if (Tools.isEmpty(list)) {
            return list;
        }
        Set<String> unionSet = new HashSet<>( );
        for (String str : list) {
            unionSet.add(str);
        }
        List<String> resultList = new ArrayList<>( );
        for (String str : unionSet) {
            resultList.add(str);
        }
        return resultList;
    }

    /**
     * 求交叉
     * @param A
     * @param B
     * @return
     */
    public static List<String> intersection(List<String> A, List<String> B) {
        List<String> resultList = new ArrayList<>( );
        if (Tools.isEmpty(A) || Tools.isEmpty(B)) {
            return resultList;
        }
        for (String str : A) {
            if (B.contains(str)) {
                resultList.add(str);
            }
        }
        resultList = Tools.cleanRepeat(resultList);//去重
        return resultList
    }
}
```

## <font color = "#159957"> commons-lang3 </font>

### <font color = "#159957"> 介绍 </font>

lang3是Apache Commons 团队发布的工具包，要求jdk版本在1.5以上，相对于lang来说完全支持java5的特性。

### <font color = "#159957"> maven依赖 </font>

```Java
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
    <version>3.5</version>
</dependency>
```

### <font color = "#159957"> 常用 </font>

#### <font color = "#159957"> StringUtils </font>

``isBlank(CharSequence cs)`` 检查字符串是否为null、empty或<font color = "#FE4365">空格字符</font>，返回一个boolean

```Java
StringUtils.isBlank(null)      = true
StringUtils.isBlank("")        = true
StringUtils.isBlank(" ")       = true
StringUtils.isBlank("bob")     = false
StringUtils.isBlank("  bob  ") = false
```

``isEmpty(CharSequence cs)`` 检查字符串是否为null、empty，返回一个boolean

```Java
StringUtils.isEmpty(null)      = true
StringUtils.isEmpty("")        = true
StringUtils.isEmpty(" ")       = false
StringUtils.isEmpty("bob")     = false
StringUtils.isEmpty("  bob  ") = false
```

``isNotBlank(CharSequence cs)`` 检查字符串是否不为null、empty或空格字符，返回一个boolean

``isNotEmpty(CharSequence cs)`` 检查字符串是否不为null、empty，返回一个boolean

## <font color = "#159957"> guava </font>

### <font color = "#159957"> 介绍 </font>

Guava工程包含了若干被google的java项目广泛依赖的核心库，例如：集合 [collections] 、缓存 [caching] 、原生类型支持 [primitives support] 、并发库 [concurrency libraries] 、通用注解 [commonannotations] 、字符串处理 [string processing] 、I/O 等等。所有这些工具每天都在被Google的工程师应用在产品服务中。

### <font color = "#159957"> maven依赖 </font>

```Java
<dependency>
    <groupId>com.google.guava</groupId>
    <artifactId>guava</artifactId>
    <version>20.0</version>
</dependency>
```

### <font color = "#159957"> 常用 </font>

[使用 Google Guava 美化你的 Java 代码](https://my.oschina.net/leejun2005/blog/172328)


## <font color = "#159957"> joda-time </font>

### <font color = "#159957"> 介绍 </font>

在Java中处理日期和时间是很常见的需求，基础的工具类就是我们熟悉的Date和Calendar，然而这些工具类的api使用并不是很方便和强大，于是就诞生了Joda-Time这个专门处理日期时间的库。
[Joda-Time 简介](https://www.ibm.com/developerworks/cn/java/j-jodatime.html)

### <font color = "#159957"> maven依赖 </font>

```Java
<dependency>
    <groupId>joda-time</groupId>
    <artifactId>joda-time</artifactId>
    <version>2.3</version>
</dependency>
```

### <font color = "#159957"> 常用 </font>

```Java
import org.apache.commons.lang3.StringUtils;
import org.joda.time.DateTime;
import org.joda.time.format.DateTimeFormat;
import org.joda.time.format.DateTimeFormatter;
import java.text.SimpleDateFormat;
import java.util.Date;

/**
 * Created by Bran
 */
public class DateTimeUtil {

    /**
     * 默认格式
     */
    public static final String STANDARD_FORMAT = "yyyy-MM-dd HH:mm:ss";

    /**
     * 将 string 格式转化为 java.util.Date 格式
     * @param dateTimeStr
     * @param formatStr
     * @return
     */
    public static Date strToDate(String dateTimeStr,String formatStr){
        DateTimeFormatter dateTimeFormatter = DateTimeFormat.forPattern(formatStr);
        DateTime dateTime = dateTimeFormatter.parseDateTime(dateTimeStr);
        return dateTime.toDate();
    }

    /**
     * 将 java.util.Date 格式转化为 string 格式
     * @param date
     * @param formatStr
     * @return
     */
    public static String dateToStr(Date date,String formatStr){
        if(date == null){
            return StringUtils.EMPTY;
        }
        DateTime dateTime = new DateTime(date);
        return dateTime.toString(formatStr);
    }

    public static Date strToDate(String dateTimeStr){
        DateTimeFormatter dateTimeFormatter = DateTimeFormat.forPattern(STANDARD_FORMAT);
        DateTime dateTime = dateTimeFormatter.parseDateTime(dateTimeStr);
        return dateTime.toDate();
    }

    public static String dateToStr(Date date){
        if(date == null){
            return StringUtils.EMPTY;
        }
        DateTime dateTime = new DateTime(date);
        return dateTime.toString(STANDARD_FORMAT);
    }

    public static void main(String[] args) {
        System.out.println(DateTimeUtil.dateToStr(new Date(),"yyyy-MM-dd HH:mm:ss"));//2018-04-02 22:44:16
        System.out.println(DateTimeUtil.strToDate("2010-01-01 11:11:11","yyyy-MM-dd HH:mm:ss"));//Fri Jan 01 11:11:11 CST 2010
        SimpleDateFormat sdf = new SimpleDateFormat(STANDARD_FORMAT);
        Date date= new Date();
        System.out.println(date);//Mon Apr 02 22:44:16 CST 2018
        String str = sdf.format(date);
        System.out.println(str);//2018-04-02 22:44:16
    }

}
```

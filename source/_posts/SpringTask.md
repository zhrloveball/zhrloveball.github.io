---
title: SpringTask 拉取数据遇到的问题
date: 2019-09-21 11:12:21
tags:
    - Spring
---

项目中用定时任务调用另一项目的 REST 接口拉取数据落库，遇到两个问题：如何保证每次拉取的都是增量数据？多个应用同时运行定时任务落库数据重复怎么办(特殊原因多个应用使用的一个数据库)？

<!-- more -->

### <font color = "#159957">如何拉取增量数据</font>

让调用拉取数据的 REST 接口提供 startTime 和 endTime 入参，这样可以查询某时间段的数据。

创建任务表，保存定时任务信息：
```mysql
DROP TABLE IF EXISTS `task`;
CREATE TABLE `task` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(32) NOT NULL,
  `last_execution_time` datetime NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `name` (`name`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
```

每次定时任务拉取数据落库后更新定时任务表的 last_execution_time，下次调用时将 last_execution_time 作为调用 REST 接口的 startTime，这样可以保证每次拉取的都是增量数据。

### <font color = "#159957">通过 INSERT IGNORE 解决重复数据</font>

除了任务表，我们还需要一张表保存拉取的数据:

```mysql
DROP TABLE IF EXISTS `xxx_data`;
CREATE TABLE `xxx_data` (
    `id` bigint(20) NOT NULL AUTO_INCREMENT,
    `uuid` varchar(40) NOT NULL,
    `target_org_id` varchar(40) NOT NULL,
    `target_role_type` tinyint(4) NOT NULL,
    `type` varchar(16) NOT NULL,
    `content` varchar(1024),
    `create_date` datetime NOT NULL,
    `update_date` datetime,
    `data_type` varchar(40) NOT NULL,
    `owner` varchar(40) NOT NULL,
    PRIMARY KEY (`id`),
    UNIQUE KEY `uuid_target_org_id_target_role_type` (`uuid`,`data_type`,`owner`,`target_role_type`,`target_org_id`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4;
```

建表时创建 UNIQUE KEY，落库时使用 ``INSERT IGNORE`` 语句忽略重复数据：

```xml
<insert id="batchInsertIgnore" parameterType="java.util.List">
    INSERT IGNORE INTO
    xxx_data(id, uuid, owner, data_type, target_org_id, target_role_type, type, content, create_date)
    VALUES
    <foreach item="item" index="index" collection="list" separator=",">
        (#{item.id}, #{item.uuid}, #{item.owner}, #{item.dataType}, #{item.targetOrgId}, #{item.targetRoleType}, #{item.type}, #{item.content}, #{item.createDate})
    </foreach>
</insert>
```

### <font color = "#159957">定时任务</font>

开启定时任务

```java
@Configuration
@EnableScheduling
public class SpringTaskConfig {
}
```

```java
@Component
public class SampleTask {

    /**
     * 每隔60s执行一次
     */
    @Scheduled(fixedDelay = 60 * 1000)
    @Transactional(rollbackFor = Exception.class)
    public void task() {

    }
}
```





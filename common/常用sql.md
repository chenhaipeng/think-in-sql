# 做一些常用sql的记录

## 分组取最新
默认情况下，group by 是取表的最早的添加记录（即：如果是主键id自增聚集表，取id小的记录）
```
   create table persondate(
    `id` bigint(20) not null comment '主键' ,
    `name` varchar(32) comment '名字',
    `addDate` datetime not null comment '添加时间',
    primary key(`id`));

   insert into `persondate` (`id`,`name`,`adddate`) values (1,'刘备','2015-10-14 00:00:00');
   insert into `persondate`(`id`,`name`,`adddate`) values (2,'刘备','2015-10-15 00:00:00');
   insert into `persondate`(`id`,`name`,`adddate`) values (3,'张飞','2015-10-16 00:00:00');
   insert into `persondate` (`id`,`name`,`adddate`) values (4,'张飞','2015-10-17 00:00:00');
     
```

### 1.﻿先建立排序子查询，再分组
因些，如果我们希望取之间最新的，只需要一张临时表，表的顺序是按时间降序，那么group by 分组后
取记录的时间,自然就取成我们想要的取新记录
```
    select * from 
     (select * from `persondate` order by adddate desc) t1 
    group by name; 
```
实测不行，因为group by 分组的必须在select 字段
### 2. ﻿关联子查询
﻿关联子查询的逻辑是，对姓名相同的记录，取第一条，当后面还有名称相同的时，判断时间是否最新，取最新的。

```
SELECT * FROM persondate AS T1
WHERE NOT EXISTS (SELECT NULL FROM persondate AS T2 WHERE T1.Name = T2.Name AND T2.AddDate > T1.AddDate)
```

### ﻿衍生表
```

SELECT T1.* FROM persondate AS T1 INNER JOIN
(
    SELECT Name,MAX(addDate) AS MaxDate FROM persondate
    GROUP BY Name
) AS T2 
ON T1.Name = T2.Name AND addDate = MaxDate
```

### ﻿Left Join配合IS NULL 
感觉这种是最优解
```
  SELECT T1.* FROM persondate AS T1
  LEFT JOIN persondate AS T2
  ON T1.Name = T2.Name AND T1.addDate < T2.addDate
  WHERE T2.Id IS NULL  
```

## 统计字符个数

## 备份表数据
## 



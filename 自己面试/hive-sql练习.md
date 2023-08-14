
在线练习sql：尚硅谷
http://forum.atguigu.cn/interview.html

sql  ： http://practice.atguigu.cn/#/question/2/desc?qType=SQL

## 用户连续登录
```sql
userid, login_time
a,2023-07-21
a,2023-07-21
a,2023-07-22
a,2023-07-22
a,2023-07-23
a,2023-07-24
a,2023-07-27
a,2023-07-28
b,2023-07-21
b,2023-07-22

得到结果
userid,login_time, days
a,2023-07-21,4
a,2023-07-27,2
b,2023-07-21,1

最大活跃天数
a,2023-07-21,4
b,2023-07-21,1
```

求解
```
create table tb(
  userid string,
  login_time string
)

insert into table tb values(
a,2023-07-21
a,2023-07-21
a,2023-07-22
a,2023-07-22
a,2023-07-23
a,2023-07-24
a,2023-07-27
a,2023-07-28
b,2023-07-21
b,2023-07-22
)

-- 每个人先按照登录时间去重然后按照用户开窗，登录时间排序打行号，用登录时间-行号得到相减后的时间。
a,2023-07-21,1,  2023-07-20
a,2023-07-22,2,  2023-07-20
a,2023-07-23,3,  2023-07-20
a,2023-07-24,4,  2023-07-20
a,2023-07-27,5,  2023-07-22
a,2023-07-28,6,  2023-07-22
b,2023-07-21,1,  2023-07-20
b,2023-07-22,2,  2023-07-20

-- 统计每个人有多少个第四列就ok
a,2023-07-20,4
b,2023-07-20,1

with temp as(
select
 userid,date_format(login_time,'YYYY-MM-dd') as login_time
from tb
group by userid,login_time
)

select
  userid,after_time,count(after_time)
from
(
  select
  userid,
  login_time,
  date_sub(login_time, (row_number() over(partition by userid order by login_time))) as after_time
from 
  temp
) o
group by userid,after_time




```

### 怎么对某一列去重
hive表有两列数据
```
id, score
1,12
1,23
2,12
3,23
```
按照id去重，同一个id只保留一条数据。注意hive不能update操作

```sql
-- 准备数据
create table test_distinct(
  id bigint,
  score bigint
);

insert into table test_distinct values(1,12);
insert into table test_distinct values(1,23);
insert into table test_distinct values(2,12);
insert into table test_distinct values(3,23);


-- 因为hive不能update，所以只能覆盖

insert overwrite table test_distinct
select
id,score
from
(
select 
  id,
  score,
  row_number() over(partition by id order by score) as rn
from 
test_distinct
) o
where rn=1
```
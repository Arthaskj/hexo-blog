title: SQL查询数据命令
category: 技能
tags: sql
date: 2019-08-14
index_img: 

---

SQL查询数据命令

<!--more-->

<!--
 * @Author: 柯军
 * @Date: 2019-08-14 18:40:49
 * @Description: 
 -->
> 根据字段名找表名、

```
select OBJECT_NAME([object_id]),* from HiMdm_R.sys.columns where name like '%Credit%'
```

---

### 一、SQL查询数据
```
SELECT 'INSERT INTO mdm.md_Index(UniqueKey,UID,LastUpdatedTime,Alias,Period,Currency,FixingType,IsBasic,Mean,BasicIndex,AverageType,Quotes,PublicAgency,BasicName,RateLevel) 
VALUES(' 
+ (CASE WHEN UniqueKey IS NULL THEN 'NULL' ELSE '''' + CAST(UniqueKey AS NVARCHAR(1024)) + '''' END) + ','
+ (CASE WHEN UID IS NULL THEN 'NULL' ELSE '''' + CAST(UID AS NVARCHAR(1024)) + '''' END) + ','
+ (CASE WHEN LastUpdatedTime IS NULL THEN 'NULL' ELSE '''' + Convert(VARCHAR(32), LastUpdatedTime, 20) + '''' END) + ','
+ (CASE WHEN Alias IS NULL THEN 'NULL' ELSE '''' + CAST(Alias AS NVARCHAR(1024)) + '''' END) + ','
+ (CASE WHEN Period IS NULL THEN 'NULL' ELSE '''' + CAST(Period AS NVARCHAR(1024)) + '''' END) + ','
+ (CASE WHEN Currency IS NULL THEN 'NULL' ELSE '''' + CAST(Currency AS NVARCHAR(1024)) + '''' END) + ','
+ (CASE WHEN FixingType IS NULL THEN 'NULL' ELSE '''' + CAST(FixingType AS NVARCHAR(1024)) + '''' END) + ','
+ (CASE WHEN IsBasic IS NULL THEN 'NULL' ELSE '''' + CAST(IsBasic AS NVARCHAR(1024)) + '''' END) + ','
+ (CASE WHEN Mean IS NULL THEN 'NULL' ELSE '''' + CAST(Mean AS NVARCHAR(1024)) + '''' END) + ','
+ (CASE WHEN BasicIndex IS NULL THEN 'NULL' ELSE '''' + CAST(BasicIndex AS NVARCHAR(1024)) + '''' END) + ','
+ (CASE WHEN AverageType IS NULL THEN 'NULL' ELSE '''' + CAST(AverageType AS NVARCHAR(1024)) + '''' END) + ','
+ (CASE WHEN Quotes IS NULL THEN 'NULL' ELSE '''' + CAST(Quotes AS NVARCHAR(1024)) + '''' END) + ','
+ (CASE WHEN PublicAgency IS NULL THEN 'NULL' ELSE '''' + CAST(PublicAgency AS NVARCHAR(1024)) + '''' END) + ','
+ (CASE WHEN BasicName IS NULL THEN 'NULL' ELSE '''' + CAST(BasicName AS NVARCHAR(1024)) + '''' END) + ','
+ (CASE WHEN RateLevel IS NULL THEN 'NULL' ELSE '''' + CAST(RateLevel AS NVARCHAR(1024)) + '''' END) 
 + ')'
FROM mdm.md_Index

```

---

### SQL去重查询
```
    select * from mdm.ts_NewBondPrice  where IID in(select max(IID) from mdm.ts_NewBondPrice group by UniqueKey ) 
```

---

### 新建用户
> 关于mysql8上创建远程连接用户方法
```mysql
mysql8和原来的版本有点不一样，8的安全级别更高，所以在创建远程连接用户的时候，

不能用原来的命令（同时创建用户和赋权）:

mysql>grant all PRIVILEGES on *.* to test@'localhost' identified  by '123456';



必须先创建用户（密码规则：mysql8.0以上密码策略限制必须要大小写加数字特殊符号）：

mysql>create user chenadmin@'%' identified  by 'Chenadmin0.';

再进行赋值：

mysql>grant all privileges on Project.* to chenadmin@'%' with grant option;

最后刷新一下：

mysql>flush privileges;

当你进行远程连接是，会出现这样的错误：

Unable to load authentication plugin 'caching_sha2_password'.

是因为mysql8使用的是caching_sha2_password加密规则，最简单的方法是修改远程连接用户的加密规则：

mysql>ALTER USER 'chenadmin'@'%' IDENTIFIED WITH mysql_native_password BY 'Chenadmin0.';
```

### MySQL备份数据库
```mysql
mysqldump -u arthas -h 128.0.0.0 -p Project > Project.sql

mysqldump -u arthas -h 128.0.0.0 -p Project < Project.sql
```

---

### 修改字段类型
```
  alter table [XPWeb-1].[dbo].[CR_BondRate] alter column RateDate datetime
```

### 数据库两表结合
```
select * from table1 a left join table2 on a.name == b.name
```
### Sql嵌套查询
```
  SELECT * FROM dbo.Req_IndexHeaders
WHERE WFInstanceNo IN (SELECT ProcessInstanceId
  FROM [XPWeb].[dbo].[WF_Trackings]
  
  )order by  RequestDate 
```
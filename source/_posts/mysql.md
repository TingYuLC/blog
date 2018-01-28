---
title: mysql学习笔记
date: 2017-11-20 20:44:12
tags: mysql
---

##### 最近在做前后端分离的项目，第一次做全栈，对数据库的操作也不是很熟悉，了解到mysql开源，适用于小型网站，就开始mysql的学习之路，以下为学习中记录的mysql常用语句。

```
// 登录
mysql -u root -p
password: ******

//创建数据库test
create database if not exists test default charset utf8 collate utf8_general_ci;

//删除数据库test
drop database if exists test;

//赋予用户操作数据库test的权利
grant all privileges on test.* to '用户名'@'%' identified by '用户密码'; 

//选择数据库
use test;

//创建数据表user
create table if not exists user (
id int unsigned not null auto_increment,
name varchar(10) not null,
login_date DATE,
primary key(id)
)engine=innodb default charset utf8;

//向user表插值
insert into user
(name, login_date)
values
('小婊砸', NOW()),
('罗城', NOW()),
('城', NOW()),
('金', NOW());

//删除user表
drop table if exists user;

//修改user表数据
update user set name = 'littleb' where name = '小婊砸';

//查找数据
select * from user where name = '罗城';
select name, login_date as date from user where name like '%城%';

//删除数据
delete from user where name = 'littleb';
```
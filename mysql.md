# DDL

```mysql
#创建数据库
create database if not exists web01;
#使用
use web01 ;
#查看
show create database web01;
```

```mysql
#表字段的增删改查
#创建
create table if not exists urls(
    u_title varchar(20),
    u_url varchar(100),
    u_score int,
    primary key(u_title,u_url) #主键默认为字段添加not null属性, 即使主键约束被删除, not null依旧存在
);
#修改
alter table urls modify u_title varchar(16); #修改字段类型
alter table urls modify u_score int not null; #修改字段属性(default value, not null, default null)
#change 功能和在modify基础上,增加了一个字段重名的功能
alter table $table_name change $old_field_name $new_field_name ... ;

#删除
alter table urls drop primary key;#删除主键约束
alter table urls drop index $constraint_name ;#删除唯一约束, 例如:
# alter table urls drop index u_url;
# constraomt_name可以在show create table {{table_name}}中查看

alter table urls drop u_score; #删除字段
#添加
alter table urls add u_uuid varchar(36); #添加字段
alter table urls add u_date date not null; #添加字段
alter table urls add unique key(u_title,u_url); #添加唯一约束
alter table urls add primary key(u_uuid); #添加主键约束
#查看
show create table urls;
#自增长属性
create table if not exists numbers(
	n_id int auto_increment not null, 
    unique key(n_id)# 字段必须为number类型且有unique约束或者primary约束,才能设置自增长属性
);
```

```mysql
#urls table
create table if not exists urls(
	u_uuid char(36) not null,
    u_title varchar(16) default "UNKNOW",
    u_url varchar(100) not null,
    u_date date not null,
    u_desc varchar(1024),
    primary key(u_uuid),
    unique key(u_url)
);
```

```mysql
#外键约束
#建表时设置外键
create table if not exists nicknames(
	n_id int auto_increment,
   	n_u_uuid char(36) not null,
    n_cotent varchar(16) not null,
    primary key(n_id),
    constraint foreign key(n_u_uuid) references urls(u_uuid)
);
#添加外键
alter table nicknames add constraint foreign key(n_u_uuid) references urls(u_uuid);
#删除外键
alter table nicknames drop foreign key $foregin_key_id; #$foregin_key_id可以通过"show create table nicknames;"查看
```

```mysql
# test01 订单表,订单条目表(订单商品关联表),商品表
create database if not exists test01 ;
use test01 ;
show create database test01 ;
create table if not exists orders(
	o_id int auto_increment,
    o_desc varchar(40) default null,
    o_time timestamp default current_timestamp,#创建时设置默认值为当前时间
    o_update_time timestamp default current_timestamp on update current_timestamp,#每次修改都设置默认值为当前时间
    primary key(o_id)
);

insert into orders() values() ;
update orders set o_desc="ok" where o_id=1 ;

create table if not exists produces(
	p_id int auto_increment,
    p_name varchar(40) default "UNKNOWN",
    p_price float not null,
    p_desc varchar(40) default null,
    p_time timestamp default current_timestamp,#创建时设置默认值为当前时间
    p_update_time timestamp default current_timestamp on update current_timestamp,#每次修改都设置默认值为当前时间
    primary key(p_id)
);
insert into produces(p_price) values(1.0);
update produces set p_name="pen", p_desc="ok" where p_id = 1 ;

create table if not exists order_items(
	oi_id int auto_increment,
    oi_o_id int not null ,
    oi_p_id int not null ,
    oi_count int not null,
    primary key(oi_id),
    constraint foreign key(oi_o_id) references orders(o_id),
    constraint foreign key(oi_p_id) references produces(p_id)
);
insert into order_items(oi_o_id, oi_p_id, oi_count) values(1,1,2) ;
insert into produces(p_name, p_price) values("eraser", 1.0),("pencil",0.5) ;
insert into order_items(oi_o_id, oi_p_id, oi_count) values(1,2,1),(1,3,4) ;
```



# DML

```mysql
#insert 
insert into table_name values(field1_value, field2_value, ...) ; #默认插入为全部字段赋值
insert into table_name(field1, field2, ...) values(field_value, field_value, ...) ;#插入为指定字段赋值
#insert 一次插入多条记录
insert into table_name values(fiel1_value, field2_value,...),(fiel1_value, field2_value,...) ;

#delete
delete from table_name ; #删除表中所有记录
delete from table_name where xxx #删除符合条件的记录

#update
update table_name set field=field_value ; #设置所有记录的field为field_value
update table_name set field=field_value where xxx;#设置符合条件的记录的field为field_value
#update 一次设置多个字段
update table_name set field1=field1_value, field2=field2_value where xxx
```

# DQL

```mysql
#selecet
select * from table_name ; #查询表中所有记录的所有字段
select field1, field2,... from table_name ; #查询表中所有记录的指定字段
select field1, field2,... from table_name where xxx; #查询表中符合条件的记录的指定字段
select * from table_name where field is null ; #查询field为null的字段
select * from table_name where field is not null ; #查询field不为null的字段
#添加常量字段
select field1, field2,...,1.0 from table_name where xxx ;
select field1, field2,...,100 from table_name where xxx ;
select field1, field2,...,"const" from table_name where xxx ;
#别名查询
select field1 as other_name1, field2 as other_name2,... from table_name where xxx ;
#分页查询 limit
select * from table_name limit start,amount;
#排序查询 order by
select * from table_name order by field asc limit 0,2; #升序
select * from table_name order by field desc limit 0,2; #降序
#查询记录去重 distinct
select distinct field1,field2,... from table_name where xxx ; 
```

```mysql
#mysql 模糊查询
# like
#"%"匹配多个字符或数字
select * from table_name where field like "%ok" ;#以ok结尾
select * from table_name where field like "ok%" ;#以ok开头
select * from table_name where field like "%ok%" ;#包含ok
#"_"匹配单个字符或数字
select * from table_name where field like "_ok";
# in & not in
select * from table_name where field in (value1, value2,...);
select * from table_name where field = value1 or field = value2 or... ;

select * from table_name where field not in (value1, value2,...);
select * from table_name where field != value1 and field != value2 and ... ;
```

```mysql
#字符串函数
select p_name, length(p_name) from produces ; # length()字符串字节大小
select p_name, char_length(p_name) from produces ; # char_length()字符串字符个数
select field, mid(field, start, len) from table_name ; #字符串截取
select p_name, mid(p_name, 1, 2) from produces ; #字符串截取
```

```mysql
#数学函数
select field, round(field,number) from table_name ; # round(field, number)四舍五入保留小数点后number位
select field, round(field) from table_name ;# 相当于round(field,0)

```

```mysql
#聚合函数
# count 统计符合条件的所有记录总条数
select count(*) from table_name ;
select count(*) from table_name where xxx ;
# min max 统计符合条件的所有记录中对应字段数值最大或最小的数值
select min(field) from table_name ;
select max(field) from table_name ;
select min(field) from table_name where xxx ;
select min(field) from table_name where xxx ;
# avg 统计符合条件的所有记录对应字段数值的平均值
select avg(field) from table_name ;
select avg(field) from table_name where xxx ;
# avg 统计符合条件的所有记录对应字段数值的总合
select sum(field) from table_name ;
select sum(field) from table_name where xxx ;
```

```mysql
# 分组查询,一般配合聚合函数使用
# 查询所有订单条目中每商品类的总下单数量
select oi_p_id, sum(oi_count) from order_items group by oi_p_id;
# 查询所有订单条目中所属1订单的且总下单数量大于6的商品类 
select oi_p_id, sum(oi_count) from order_items where oi_o_id = 1 group by oi_p_id having sum(oi_count) > 6 order by sum(oi_count) desc;
```

```mysql
# 子查询
# 单列子查询
# 查询order_items中,商品价格大于等于1的item
select * from order_items where oi_p_id in (select p_id from produces where p_price >= 1); 
# 多列子查询, 从派生表中查询
# 查询produces中, 价格和"pen"相同的其它produce
select * from (select p_name, p_price from produces where p_name !="pen") as nopen where p_price = (select p_price from produces where p_name = "pen");
select * from (select p_name, p_price from produces where p_price = (select p_price from produces where p_name = "pen"))as equalpen where p_name != "pen" ;
```

```mysql
# 连接查询
# 内连接查询
# 方式一, 不建议使用
select * from order_items,produces where oi_p_id = p_id;
select order_items.oi_id,order_items.oi_o_id,produces.p_name,produces.p_price from order_items,produces where oi_p_id = p_id;
# 方式二, 建议使用
select * from order_items inner join produces on oi_p_id = p_id ;
select order_items.oi_id,order_items.oi_o_id,produces.p_name,produces.p_price from order_items inner join produces on oi_p_id = p_id ;
select order_items.oi_id,order_items.oi_o_id,produces.p_name,produces.p_price from order_items inner join produces on oi_p_id = p_id where produces.p_price > 0.6;
# 左连接
select * from orders inner join order_items on o_id = oi_o_id ;
select o_id, o_desc, o_update_time, oi_p_id, oi_count from orders left join order_items on o_id = oi_o_id ;
# 右连接
select * from order_items right join produces on oi_p_id = p_id ;
select oi_id, oi_o_id, oi_count, p_name, p_price from order_items right join produces on oi_p_id = p_id ;
# 全连接
select oi_id, oi_o_id, oi_count, p_name, p_price from order_items left join produces on oi_p_id = p_id union select oi_id, oi_o_id, oi_count, p_name, p_price from order_items right join produces on oi_p_id = p_id ;
```


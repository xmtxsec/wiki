# 一、SQL Injection (intro)

## 问题2

查看示例表。尝试检索员工鲍勃·佛朗哥的部门。请注意，您已被授予此分配中的完全管理员权限，无需身份验证即可访问所有数据。



**解答：**

题目的意思就是要我们通过SQL语句来查询出鲍勃·佛朗哥所在的部门，在前面的表信息中我们可以得知”公司在其数据库中保存以下员工信息：唯一的员工编号（ID）、姓氏（last name）、名字（first name）、部门（department）、工资（salary）和交易身份验证编号（auth_tan）。这些信息中的每一条都存储在单独的列中，每行代表公司（employee）的一名员工。“



根据表信息我们可以从公司（employees）表中查询名字（first name）为Bob，姓氏（last name）为Franco的员工所在的部门（department），构造SQL语句如下：

```plain
select department from employees where first_name='Bob' and last_name='Franco'
```

![img](../../_img/0X03%20Injection/1666681088404-cd114b64-79a8-4413-b4cc-fd57e414bf88.png)



## 问题3

尝试将托比·巴内特的部门更改为“销售”。请注意，您已被授予此分配中的完全管理员权限，无需身份验证即可访问所有数据。



**解答:**

这里同前面一题一样只需要构造一条SQL语句即可

```plain
update employees set department='Sales' where first_name='Tobi' and last_name='Barnett'
```

![img](../../_img/0X03%20Injection/1666682850860-e5b527de-a202-4e76-a6bc-51d12152062a.png)



## 问题4

现在尝试通过将列“phone”(varchar(20)) 添加到表“employees”来修改方案。 



**解答：**

题目的意思就是在表employees中新添加一列

```plain
alter table employees add phone varchar(20)
```

![img](../../_img/0X03%20Injection/1666683047549-c9e3ff66-26ac-499a-8730-4f3cfd8feea1.png)



## 问题5

尝试将表的权限授予用户unauthorizeduser



**解答：**

```plain
grant alter table to unauthorized_user
```
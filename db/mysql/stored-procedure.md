



----



| 语句                              | 描述                 |
| --------------------------------- | -------------------- |
| show procedure status;            | 查看所有的procedures |
| show create procedure risk_iskyc; | 查看risk_iskyc       |
|                                   |                      |
|                                   |                      |



```mysql

-- 创建procedure的语法
delimiter $$

drop procedure if exists risk_iskyc$$
create procedure risk_iskyc(in user_id int, in site varchar(16))
DETERMINISTIC
comment '判断是否是kyc用户。当site=“BR”时是巴西kyc，其它的走主站'
begin
	declare state int default 0;
	if site = 'BR' then
	   select if(vip_level >= 1, 1, 0) into state from br_kyc b where b.user_id = user_id;
	else
	   select 1 into state from kyc_list k where k.user_id = user_id and status = 2;
	end if;
	
	select state;
end$$

delimiter ;
```



1. MySQL的存储过程不是原子的，需要自己声明事务；
2. 



----

#### 0x09 References

1. 
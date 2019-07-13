



------



| 语句                             | 描述                |
| -------------------------------- | ------------------- |
| show function status;            | 查看所有的functions |
| show create function risk_iskyc; | 查看risk_iskyc      |
|                                  |                     |
|                                  |                     |



```mysql
delimiter $$

drop function if exists risk_iskyc$$
create function risk_iskyc(user_id int, site varchar(16)) returns int
comment '判断是否是kyc用户: 1-> 是， 0-> 否'
begin
	declare state int default 0;
	-- 当site=“BR”时是巴西kyc，其它的走主站
	if site = 'BR' then
	   select if(vip_level >= 1, 1, 0) into state from br_kyc b where b.user_id = user_id;
	else
	   select 1 into state from kyc_list k where k.user_id = user_id and status = 2;
	end if;
	
	return state;
end$$

delimiter ;
```



1. MySQL的存储过程不是原子的，需要自己声明事务；
2. 



------

#### 0x09 References

1. 
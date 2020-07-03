





| Robot    | Address                                                      |      |
| -------- | ------------------------------------------------------------ | ---- |
| 熊猫警长 | https://oapi.dingtalk.com/robot/send?access_token=a8b641ce0da30699f3a34c033b27f96a53d71d2223681b7f4f591e9ceae8a1c3 |      |
|          |                                                              |      |
|          |                                                              |      |



每个机器人每分钟最多发送20条。消息发送太频繁会严重影响群成员的使用体验



在安全设置中配置两个ip地址为：

> 1.0.0.1/1
>
> 128.0.0.1/1



```shell
curl 'https://oapi.dingtalk.com/robot/send?access_token=099723d58f70400805e693bc289c9b476f83b4b008596b989c49afbd6804dd28' \
-H 'Content-Type: application/json' \
-d '{"msgtype": "text", 
    "text": {
         "content": "钉钉机器人群消息测试"
    }
  }'
```



----

#### 0x09 References

1. [钉钉开发文档](https://ding-doc.dingtalk.com/doc#/serverapi2/qf2nxq)
2. [子网掩码计算](http://tool.chinaz.com/Tools/subnetmask)




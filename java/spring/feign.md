

----

#### 0x01 池化



```yaml
feign:
	hystrix:
		enabled: true
	httpclient:
		enabled: true
		
		# feign的最大连接数
		max-connections: 200
		# 单个路径的最大连接数
		max-connections-per-route: 50
		
	compression:
		request:
			enable: true
			mime-types: text/xml,application/xml,application/json
			# 只有超过2M的请求数据才会进行压缩
			min-request-size: 2048
			
		response:
			enable: true
		
```





----

#### 0x02 重试

高可用需要考虑重试机制，如下配置会在risk-execute-inner这个服务上启用重试：

```yml
# 下面这些不需要
#spring:
#  cloud:
#    loadbalancer:
#      retry:
#        enabled: true

risk-execute-inner:
	ribbon:
		ConnectTimeout: 200
		ReadTimeout: 500
    
    # Max number of retries on the same server (excluding the first try)
    # 0的含义就是本机不重试，直接到下一台机器去重试
		MaxAutoRetries: 0
    
    # Max number of next servers to retry (excluding the first server)
    # nextServer重试一次
		MaxAutoRetriesNextServer: 1
		
		# 通过设置这个参数，如果被调用接口返回HttpStatus=500，则调用方会主动重试
		retryableStatusCodes: 500
    
    # false代表着只有get操作会被retry
    # true 代表着post操作也会被retry
		OkToRetryOnAllOperations: true
		
```



1. 幂等操作(idempotent)：凡重试一定导致同一接口调用多，就需要考虑幂等的问题，要考虑同一接口调用多会不会引发问题
2. 



----

#### 0x09 References

1. [Retrying Failed Requests](https://cloud.spring.io/spring-cloud-netflix/reference/html/#retrying-failed-requests)
2. [Spring Cloud各组件重试总结](https://www.jianshu.com/p/007987c25e13)
3. [ribbon: getting started](https://github.com/Netflix/ribbon/wiki/Getting-Started#the-properties-file-sample-clientproperties)
4. 
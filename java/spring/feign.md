

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
		ConnectTimeout: 500
		ReadTimeout: 1000
    
    # Max number of retries on the same server (excluding the first try)
		MaxAutoRetries: 1
    
    # Max number of next servers to retry (excluding the first server)
		MaxAutoRetriesNextServer: 2
		
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
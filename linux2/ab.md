



------

#### 应用场景

1. 10用户，请求总数为10000

   ```shell
   ab -c 10 -n 10000  "http://172.20.26.52:9336/api/swap/v1/market/orderbook?size=5&symbol=BTCUSDT"
   ```

2. `yum install yum-utils` 安装ab工具





----
#### References

1. [centos7下安装ab测试](https://blog.csdn.net/zeroctu/article/details/53171618)
2. 
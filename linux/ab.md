



------

#### 应用场景

1. 10用户，请求总数为10000

   ```shell
   ab -c 10 -n 10000  "http://172.20.20.152:9336/api/swap/v1/market/orderbook?size=5&symbol=BTCUSDT"
   ```

2. 
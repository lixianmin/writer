



----



```bash

# 清除app目录下的build文件夹
gradle clean

# 检查依赖并编译打包jar包，跳过"测试"
gradle build -x test 

# 打包并发布
./gradlew build publish

# 运行jar包
java -Denv=DEV -jar build/libs/xxx.jar
```



---



1. 刷新时报socks proxy timeout

> 命令行搜索ps aux | grep GradleDaemon，然后将GradleDaemon杀死


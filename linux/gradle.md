



----



```bash

# 清除app目录下的build文件夹
gradle clean

# 检查依赖并编译打包jar包，跳过"测试"
gradle build -x test 

# 安装到本地库中，需要在build.gradle中加上 mavenLocal()
gradle install

# 打包，然后发布
./gradlew build publish

# 流水线
./gradlew clean build install publish

# 运行jar包
java -Denv=DEV -jar build/libs/xxx.jar

# 打印依赖关系
./gradlew dependencyInsight  --dependency jedis
```



---



1. 刷新时报socks proxy timeout

> 命令行搜索ps aux | grep GradleDaemon，然后将GradleDaemon杀死


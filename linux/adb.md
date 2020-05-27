

---
#### 0x01 adb
1. adb install -r foo.apk
2. adb shell 通过usb连接设备
3. adb kill-server
4. adb devices  检查设备是否连接
5. adb logcat | egrep -i ‘unity|debug|crash'   同时过滤unity, debug, crash时的的日志
6. adb pull /storage/…/panda.log    下载日志到当前目录



---
#### 0x02 常用功能

1. 定位游戏目录

   ```shell
    一般在sdcard的根目录中的Android/data/下，先定位sdcard目录（如/storage/sdcard0），另外可以通过ls -R | grep torchlight
   ```
   
2. 查看内存占用

   ```shell
    top -m 1， 或者top | grep torchlight$
   ```
   
3. 按rss排序

   ```shell
    top -m 10 -s rss, 其中rss代表resident set size
   ```
   
4. 查看cpu, mem, gpu信息
        
   ```shell
    cat /proc/cpuinfo
    cat /proc/meminfo
 dumpsys |grep GLES
   ```


5. 取得root权限   
  
	```shell
 su
	```

7. 获取ip

	```shell
    netcfg
	```



---
#### 0x03 testin日志查看

1. 对于崩溃日志，搜索backtrace, stack，sigsegv, libunity.so, 定位崩溃堆栈，pc后面的数值是libunity.so中符号表位置
2. objdump -S libunity.so >> libunity.txt  输出符号表，这个对release版是有用的，对development版本，自带函数名输出到日志中
3. libunity.so          这个是unity的c库文件，并不是UnityEngine.dll，后者都以dll的形式存着呢

1. am_crash             crash
2. am_kill              游戏被杀死
3. am_on_resume_called  切回到游戏

1. SIGSEGV    无效内存引用，或段错误， segmentation violation
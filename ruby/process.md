

---
#### 进程启动

1. 使用%x功能

    ```ruby
    ret= %x| which #{cmd}|
    ```

2. 使用backpack operator

    ```ruby
    puts `ls #{dir}`
    ```

3. 在subshell中执行cmd，返回true/false，表示是否调用成功，会挂起shell；
    ​     
    ```ruby
    # 这个方法的特点在于：它不会将cmd的输出重定向，而且直接打印到shell中
    isOk = system cmd
    ```

4. 调用一个外部命令，替换当前的进程，会挂起shell

    ```ruby
    exec(cmd)
    ```

5. 进程交互

    ```ruby
    require 'open3'
    Open3.popen3(‘ls -l') { \|stdin, stdout, stderr\| ... }
    
    stdin, stdout, stderr = Open3.popen3('dc')
    stdin.puts(‘1’)
    stdin.write(‘hello world')
    stdin.close_write
    stdout.read.split(“\n”).each do |line|
        puts line
    end
    result = stdout.gets
    
    # 某种情况下直接杀死子进程
    Open3.popen3('ls -l') do |i, o, e, w|
        Process.kill('KILL', w.pid)
    end
    ```


----
#### thread特点
1. thread发生异常后会默默地退出，没有任何日志或提示
2. 设置priority之后，只是一个给os的建议，os可以选择忽视
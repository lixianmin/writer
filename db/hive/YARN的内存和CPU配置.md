

对于16G内存、4核CPU的机器，挂载了1个磁盘，则计算方案如下：

```shell
./yarn-utils.py -c 4 -m 16 -d 1 -k False

输出如下：

 Using cores=4 memory=16GB disks=1 hbase=False
 Profile: cores=4 memory=14336MB reserved=2GB usableMem=14GB disks=1
 Num Container=3
 Container Ram=4608MB
 Used Ram=13GB
 Unused Ram=2GB
 
 yarn.scheduler.minimum-allocation-mb=4608
 yarn.scheduler.maximum-allocation-mb=13824
 yarn.nodemanager.resource.memory-mb=13824
 
 mapreduce.map.memory.mb=2304
 mapreduce.map.java.opts=-Xmx1843m
 mapreduce.reduce.memory.mb=4608
 mapreduce.reduce.java.opts=-Xmx3686m
 
 yarn.app.mapreduce.am.resource.mb=2304
 yarn.app.mapreduce.am.command-opts=-Xmx1843m
 mapreduce.task.io.sort.mb=921

```



| 配置文件              | 配置设置    | 默认值    | 计算值      |
| :-------------------- | :----------------------------------- | :-------- | :------------------------------- |
| yarn-site.xml         | yarn.nodemanager.resource.memory-mb  | 8192 MB   | = containers * RAM-per-container |
| yarn-site.xml         | yarn.scheduler.minimum-allocation-mb | 1024MB    | = RAM-per-container              |
| yarn-site.xml         | yarn.scheduler.maximum-allocation-mb | 8192 MB   | = containers * RAM-per-container |
| yarn-site.xml (check) | yarn.app.mapreduce.am.resource.mb    | 1536 MB   | = 2 * RAM-per-container          |
| yarn-site.xml (check) | yarn.app.mapreduce.am.command-opts   | -Xmx1024m | = 0.8 * 2 * RAM-per-container    |
| mapred-site.xml       | mapreduce.map.memory.mb              | 1024 MB   | = RAM-per-container              |
| mapred-site.xml       | mapreduce.reduce.memory.mb           | 1024 MB   | = 2 * RAM-per-container          |
| mapred-site.xml       | mapreduce.map.java.opts              |           | = 0.8 * RAM-per-container        |
| mapred-site.xml       | mapreduce.reduce.java.opts           |           | = 0.8 * 2 * RAM-per-container    |





```python
#!/usr/bin/env python
'''
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at
    http://www.apache.org/licenses/LICENSE-2.0
Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
'''

import optparse
from pprint import pprint
import logging
import sys
import math
import ast

''' Reserved for OS + DN + NM,  Map: Memory => Reservation '''
reservedStack = { 4:1, 8:2, 16:2, 24:4, 48:6, 64:8, 72:8, 96:12, 
                   128:24, 256:32, 512:64}
''' Reserved for HBase. Map: Memory => Reservation '''
  
reservedHBase = {4:1, 8:1, 16:2, 24:4, 48:8, 64:8, 72:8, 96:16, 
                   128:24, 256:32, 512:64}
GB = 1024

def getMinContainerSize(memory):
  if (memory <= 4):
    return 256
  elif (memory <= 8):
    return 512
  elif (memory <= 24):
    return 1024
  else:
    return 2048
  pass

def getReservedStackMemory(memory):
  if (reservedStack.has_key(memory)):
    return reservedStack[memory]
  if (memory <= 4):
    ret = 1
  elif (memory >= 512):
    ret = 64
  else:
    ret = 1
  return ret

def getReservedHBaseMem(memory):
  if (reservedHBase.has_key(memory)):
    return reservedHBase[memory]
  if (memory <= 4):
    ret = 1
  elif (memory >= 512):
    ret = 64
  else:
    ret = 2
  return ret
                    
def main():
  log = logging.getLogger(__name__)
  out_hdlr = logging.StreamHandler(sys.stdout)
  out_hdlr.setFormatter(logging.Formatter(' %(message)s'))
  out_hdlr.setLevel(logging.INFO)
  log.addHandler(out_hdlr)
  log.setLevel(logging.INFO)
  parser = optparse.OptionParser()
  memory = 0
  cores = 0
  disks = 0
  hbaseEnabled = True
  parser.add_option('-c', '--cores', default = 16,
                     help = 'Number of cores on each host')
  parser.add_option('-m', '--memory', default = 64, 
                    help = 'Amount of Memory on each host in GB')
  parser.add_option('-d', '--disks', default = 4, 
                    help = 'Number of disks on each host')
  parser.add_option('-k', '--hbase', default = True,
                    help = 'True if HBase is installed, False is not')
  (options, args) = parser.parse_args()
  
  cores = int (options.cores)
  memory = int (options.memory)
  disks = int (options.disks)
  hbaseEnabled = ast.literal_eval(options.hbase)
  
  log.info("Using cores=" +  str(cores) + " memory=" + str(memory) + "GB" +
            " disks=" + str(disks) + " hbase=" + str(hbaseEnabled))
  minContainerSize = getMinContainerSize(memory)
  reservedStackMemory = getReservedStackMemory(memory)
  reservedHBaseMemory = 0
  if (hbaseEnabled):
    reservedHBaseMemory = getReservedHBaseMem(memory)
  reservedMem = reservedStackMemory + reservedHBaseMemory
  usableMem = memory - reservedMem
  memory -= (reservedMem)
  if (memory < 2):
    memory = 2 
    reservedMem = max(0, memory - reservedMem)
    
  memory *= GB
  
  containers = int (max(3, min(2 * cores,
                         min(math.ceil(1.8 * float(disks)),
                              memory/minContainerSize))))
  log.info("Profile: cores=" + str(cores) + " memory=" + str(memory) + "MB"
           + " reserved=" + str(reservedMem) + "GB" + " usableMem="
           + str(usableMem) + "GB" + " disks=" + str(disks))

  container_ram =  abs(memory/containers)
  if (container_ram > GB):
    container_ram = int(math.floor(container_ram / 512)) * 512
  log.info("Num Container=" + str(containers))
  log.info("Container Ram=" + str(container_ram) + "MB")
  log.info("Used Ram=" + str(int (containers*container_ram/float(GB))) + "GB")
  log.info("Unused Ram=" + str(reservedMem) + "GB")
  log.info("yarn.scheduler.minimum-allocation-mb=" + str(container_ram))
  log.info("yarn.scheduler.maximum-allocation-mb=" + str(containers*container_ram))
  log.info("yarn.nodemanager.resource.memory-mb=" + str(containers*container_ram))
  map_memory = math.floor(container_ram / 2)
  reduce_memory = container_ram
  am_memory = min(map_memory, reduce_memory)
  log.info("mapreduce.map.memory.mb=" + str(int(map_memory)))
  log.info("mapreduce.map.java.opts=-Xmx" + str(int(0.8 * map_memory)) +"m")
  log.info("mapreduce.reduce.memory.mb=" + str(int(reduce_memory)))
  log.info("mapreduce.reduce.java.opts=-Xmx" + str(int(0.8 * reduce_memory)) + "m")
  log.info("yarn.app.mapreduce.am.resource.mb=" + str(int(am_memory)))
  log.info("yarn.app.mapreduce.am.command-opts=-Xmx" + str(int(0.8*am_memory)) + "m")
  log.info("mapreduce.task.io.sort.mb=" + str(int(min(0.4 * map_memory, 1024))))
  pass

if __name__ == '__main__':
  try:
    main()
  except(KeyboardInterrupt, EOFError):
    print("\nAborting ... Keyboard Interrupt.")
sys.exit(1)
```





----

#### 0x09 References

1. [ambari-yarn-utils](https://github.com/mahadevkonar/ambari-yarn-utils)
2. [YARN的内存和CPU配置](https://blog.csdn.net/dxl342/article/details/52840455)
3. [YARN的Memory和CPU调优配置详解](http://blog.itpub.net/30089851/viewspace-2127851/) 














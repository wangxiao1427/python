## Python如何定位内存泄漏代码

#### 内存监控工具 memory_profiler
* 安装pip install memory_profiler
* 在被监控代码位置添加装饰器@profile
```
@profile
def oom():
    a = [0] * (10 ** 6)
    b = [0] * (10 ** 7)
    c = [0] * (10 ** 8)
    del b
    return a

oom()
```
可以通过命令参数执行```python -m memory_profiler test.py```, 结果如下:  
```
Line \#    Mem usage    Increment   Line Contents
================================================
   584   46.168 MiB   46.168 MiB   @profile
   585                             def oom():
   586   53.801 MiB    7.633 MiB       a = [0] * (10 ** 6)
   587  130.098 MiB   76.297 MiB       b = [0] * (10 ** 7)
   588  893.090 MiB  762.992 MiB       c = [0] * (10 ** 8)
   589  130.148 MiB    0.000 MiB       del c
   590  130.148 MiB    0.000 MiB       return a
```
也可以通过依赖包导入执行
```from memory_profiler import profile```

分析memory_filer源码```def profile(func=None, stream=None, precision=1, backend='psutil')```   
stream是输出流对象，默认是控制台，可以传入文件流对象，则会输出结果到文件中  
precision是内存占用大小的精确度  


```
def f(a, n=100):
     import time
     time.sleep(2)
     b = [a] * n
     time.sleep(1)
     return b

from memory_profiler import memory_usage
if __name__ == '__main__':
    print(memory_usage((f, (2,), {'n' : int(1e6)})))
```
结果输出:  
```
[46.11328125, 46.44140625, 46.44921875, 46.44921875, 46.44921875, 46.44921875, 46.44921875, 46.44921875, 46.44921875, 46.44921875, 46.44921875, 46.44921875, 46.44921875, 46.44921875, 46.44921875, 46.44921875, 46.44921875, 46.44921875, 46.44921875, 46.44921875, 46.44921875, 54.08203125, 54.08203125, 54.08203125, 54.08203125, 54.08203125, 54.08203125, 54.08203125, 54.08203125, 54.08203125, 54.08203125, 54.0859375]
```

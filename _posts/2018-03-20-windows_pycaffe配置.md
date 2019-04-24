---
tag: [caffe,python]
---

# 在windows编译caffe接口
安装环境:win10,vs2013,python2.7,cmake3.8.2

<!--more-->

## 从caffe源码编译caffe库和pycaffe
编译流程见上一篇博客,注意修改python相关设置

编译完成后,caffe.sln在caffe/scripts/build下,不在caffe/build下,不明觉厉.

打开sln文件,编译全部项目
## 拷贝库文件到python目录
把caffe/python下的caffe文件夹拷贝到**C:\Python27\Lib\site-packages**(注意修改成你的python目录)

在你的py文件中尝试**import caffe**
## 连环报错
运行python文件,报错:
```python
Traceback (most recent call last):
  File "c:/Users/cat/Desktop/caffe_test.py", line 1, in <module>
    import caffe
  File "C:\Python27\lib\site-packages\caffe\__init__.py", line 1, in <module>
    from .pycaffe import Net, SGDSolver, NesterovSolver, AdaGradSolver, RMSPropSolver, AdaDeltaSolver, AdamSolver, NCCL, Timer  File "C:\Python27\lib\site-packages\caffe\pycaffe.py", line 15, in <module>    import caffe.io
  File "C:\Python27\lib\site-packages\caffe\io.py", line 2, in <module>
    import skimage.io
ImportError: No module named skimage.io
```

使用pip安装skimage.io库
```sh
pip install skimage.io
```
可以修改C:/Users/cat/pip/pip.ini文件,使用豆瓣的pip源(我只找到了豆瓣的国内源)
```sh
[global] 
index-url = https://pypi.douban.com/simple 
[install] 
trusted-host=pypi.doubanio.com 
```
pip提示安装另一个库**scikit-image**
```sh
pip install scikit-image
```
安装报错:Microsoft Visual C++ 9.0 is required Unable to find vcvarsall.bat
解决方案:安装它...[vc for py27](http://www.microsoft.com/en-us/download/details.aspx?id=44266)

继续安装scikit-image报错:
```sh
fatal error C1083: Cannot open include file: 'stdint.h': No such file or directory

fatal error C1083: Cannot open include file: 'intypes.h': No such file or directory
```
解决方案: 找到这两个文件,放到对应目录
[下载链接](https://code.google.com/archive/p/msinttypes/downloads)

目录:C:\Users\cat\AppData\Local\Programs\Common\Microsoft\Visual C++ for Python\9.0\VC\include

最终安装成功了scikit-image

## 完成
测试import caffe没有报错
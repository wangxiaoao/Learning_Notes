# CentOS安装Python3与Python2共存
*from https://www.cnblogs.com/JahanGu/p/7452527.html*

## 1.查看是否已经安装python
```shell
python -V

Python 2.7.5
````

使用`which`查看python命令位置
```shell
which python

/usr/bin/python
```
## 2.安装python相关包
```shell
yum install -y zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make
```

## 3.备份原有python
```shell
cd /usr/bin
mv python python.bak
```

## 4.安装python3
```shell
wget https://www.python.org/ftp/python/3.7.2/Python-3.7.2.tar.xz
```

解压
```shell
tar -xvJf  Python-3.7.2.tar.xz
```

编译安装
```shell
cd Python-3.7.2
./configure prefix=/usr/local/python3
make && make install
```

添加软连接
```shell
ln -s /usr/local/python3/bin/python3 /usr/bin/python
```

确认python版本
```
python -V
Python 3.7.2

python2 -V
Python 2.7.5
```

修改vim和urlgrabber-ext-down的shebang为python2
将#!/usr/bin/python修改为#!/usr/bin/python2
```
vim /usr/bin/yum
vim /usr/libexec/urlgrabber-ext-down
```




# ls mkdir rm cp mv 
`mkdir -p /a/b/c` 创建新目录

# find
```shell
find /etc -name pass* 
/etc/pam.d/password-auth-ac
/etc/pam.d/passwd
(...省略)

find /home -user ansible
/home/ansible
/home/ansible/.cache
(...省略)

```

# inode
*整理自 http://www.ruanyifeng.com/blog/2011/12/inode.html*

Sector：最小存储单元512Byte=0.5KB
Block:最小读写单元，4KB
Inode:文件包含数据和inode

inode 包含
- 文件大小（Bytes)
- user-id
- group-id
- rwx权限
- timestamps，ctime,mtime,atime
- link数量
- block数量

查看文件inode
```shell
stat /etc/passwd

File: ‘/etc/passwd’
  Size: 1555      	Blocks: 8          IO Block: 4096   regular file
Device: fd01h/64769d	Inode: 919500      Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2019-03-26 13:20:01.012512601 +0800
Modify: 2019-03-23 13:14:12.066278761 +0800
Change: 2019-03-23 13:14:12.068278834 +0800
 Birth: -
```

## inode table
查看inode数量和剩余inode数量
```shell
df -i

Filesystem      Inodes IUsed   IFree IUse% Mounted on
/dev/vda1      2621440 69889 2551551    3% /
devtmpfs        124644   322  124322    1% /dev
tmpfs           127020     1  127019    1% /dev/shm
tmpfs           127020   426  126594    1% /run
tmpfs           127020    16  127004    1% /sys/fs/cgroup
tmpfs           127020     1  127019    1% /run/user/0

```

查看inode大小
```shell
dumpe2fs -h /dev/vda1 | grep "Inode size"

dumpe2fs 1.42.9 (28-Dec-2013)
Inode size:	          256
```

## dumpe2fs
`man dumpe2fs`dumpe2fs用来查看superblock和blockgroup
-h 仅查看superblock信息

## superblock & blockgroup
*https://unix.stackexchange.com/questions/4402/what-is-a-superblock-inode-dentry-and-a-file*

**superblock** 文件系统元数据，定义了文件系统的类型、大小、状态等结构。"metadata of metadata"  
**dentry** 连接inode和文件名
**blockgroup** 一组block，用于减少大文件读写时的时间。 
查看blockgroup包含多少block
```shell
dumpe2fs -h /dev/vda1 | grep group
dumpe2fs 1.42.9 (28-Dec-2013)
Blocks per group:         32768
Fragments per group:      32768
Inodes per group:         8192
Inode blocks per group:   512
Flex block group size:    16
Reserved blocks gid:      0 (group root)
```

## inode number
查看inode number
```shell
ls -i /etc/passwd

919500 /etc/passwd
```

# Link
A **hard link** then just creates another file with a link to the same underlying inode.
Hard_link inode相同，创建一个新文件链接到当前inode。
只有所有链接到一个inode的文件都删除了，inode才会被删除或者重写。
symbolic_link链接到文件名。

![RHCSA_Lesson3_Link](https://github.com/wangxiaoao/Learning_Notes/blob/master/Learning%20%20summarize(MarkDown%20Mainly)/Linux%20Notes/RHCSA/picture/RHCSA_Lesson3_Link.png?raw=true)

```shell{.line-numbers}
touch data
#create hardlink
ln data hard_link
#create symbolic link
ln -s data symbolic link
ls -li

398304 -rw-r--r-- 2 root root 0 Mar 26 17:24 data
398304 -rw-r--r-- 2 root root 0 Mar 26 17:24 hard_link
398306 lrwxrwxrwx 1 root root 4 Mar 26 17:25 symbolic_link -> data
```
>注意hard_link inode相同，symbolic_link显示方式不同。





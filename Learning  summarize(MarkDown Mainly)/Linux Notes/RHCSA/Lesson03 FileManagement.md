# ls mkdir rm cp mv 
`mkdir -p /a/b/c` make new directory

# find
```shell
find /etc -name pass* 
/etc/pam.d/password-auth-ac
/etc/pam.d/passwd
(...omitted)

find /home -user ansible
/home/ansible
/home/ansible/.cache
(...ommited)

```

# inode
*sortedby http://www.ruanyifeng.com/blog/2011/12/inode.html*

Sector:minimum storage unit,512Byte=0.5KB
Block:8 Sector,minimum read/write unit
Inode:file=data+inode

inode contains
- size of the file in bytes
- user-id
- group-id
- rwx
- timestamps，ctime,mtime,atime
- link count
- block numbers

show file inodes
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
show inode amount and free inode amount
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

show inode sizes
```shell
dumpe2fs -h /dev/vda1 | grep "Inode size"

dumpe2fs 1.42.9 (28-Dec-2013)
Inode size:	          256
```

## dumpe2fs
`man dumpe2fs`defines *dumpe2fs prints the super  block  and  blocks  group  information  for  thefilesystem present on device.*
-h show superblock only

## superblock & blockgroup
*https://unix.stackexchange.com/questions/4402/what-is-a-superblock-inode-dentry-and-a-file*
The **superblock** is essentially file system metadata and defines the file system type, size, status, and information about other metadata structures (metadata of metadata). 
A **dentry** is the glue that holds inodes and files together by relating inode numbers to file names.
A **blockgroup** that we discussed in the previous section are further grouped together to form block groups for ease of access during read and writes. This is primarily done to reduce the amount of time taken while reading or writing large amounts of data. 

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
show inode number
```shell
ls -i /etc/passwd

919500 /etc/passwd
```

# Link
A **hard link** then just creates another file with a link to the same underlying inode.
When you delete a file it removes one link to the underlying inode. The inode is only deleted (or deletable/over-writable) when all links to the inode have been deleted.
A **symbolic link** is a link to another name in the file system.
![RHCSA_Lesson3_Link](https://github.com/wangxiaoao/Learning_Notes/blob/master/Learning%20%20summarize(MarkDown%20Mainly)/Linux%20Notes/RHCSA%20Course/picture/RHCSA_Lesson3_Link.png?raw=true)

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





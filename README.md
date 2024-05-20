# linux-virtualfs
Create a loop device, build &amp; mount an ext2 filesystem


Authors
--------
Kesi Neblett, Padraic Quinn, and Jonathan Voss 


PART 1
------

Inode: a data structure representing a file object in a file system. It
contains information about a file object such as ownership, permissions,
reference to file content, etc.  
Link: reference to the inode of a file. Every directory has two hard links
including a link to itself and to its parent directory. Every directory also
has a link to its sub directories. 


The Kernel configuration options required for using loop devices...

CONFIG_BLK_DEV_LOOP=m
CONFIG_BLK_DEV_LOOP_MIN_COUNT=8
CONFIG_BLK_DEV_CRYPTOLOOP=m

PART 2
-------

In the inode struct i_uid and i_gid's data type are structs named kuid_t and
kgit_t which are structs. However, before thy were previously defined
without using structs and was able to be cast to an integer type.To build
the module, we had to initialize the members of the structs instead of
setting it equal to zero.

Additionally d_alloc_root(inode * root) no longer defined under this name in
linux/cache.h. The function is now defined as d_make_root(inode * root). We changed the
function name in lwfns.c.


In lwfns.c, mount_dbev() is used to mount our file system on mnt. However,
mount_dbev() is only used to mount a filesytem backed by a block device.
Instead, we used mount_single which mounts a filesystem that shares the
instance between all mount

PART 3
-------

When a directory is made, we increment the count of the new directory and
the count for the parent directory to account for hard links between
directories. To do this, we used inc_nlink() in the function
lfs_create_dir()


PART 4
-------

We added .mkdir to the inode operations struct that points to the mkdir
function specifc to our filesystem. We copied the implementation of
lfs_create_dir but since we were passed the parents inode and the childs
dentry, we only created an inode for thechild dentry and incremented the
hardlink count

PART 5
-------
Works as expected


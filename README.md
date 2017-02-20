# Get some copy infors from different filesystems under linux

# Platform
+ ubuntu 16.04.1 amd64
+ add a new disk (60G)
+ mkfs for ext4 & btrfs & ntfs with this disk
+ mount this disk to `/haha`
+ copy file `test.tar` (23G) into `/haha`

# Test One - get running time
+ run `cp` command line just like below

| filesystem | cp ./test.tar ./test.tar.tar | cp --reflink=always ./test.tar ./test.tar.tar       | cp --reflink=auto ./test.tar ./test.tar.tar |
| ---------- |:----------------------------:|:---------------------------------------------------:| -------------------------------------------:|
| ext4       | long time                    | cp: failed to clone: Inappropriate ioctl for device | long time                                   |
| btrfs      | long time                    | very fast                                           | very fast                                   |
| ntfs       |   |   |   |

# Test Two - get xattr infors
+ `getfattr test.tar` after run `setfattr -n user.foo -v bar test.tar` & `cp` just like below

| filesystem | cp ./test.tar ./test.tar.tar | cp --reflink=always ./test.tar ./test.tar.tar | cp --reflink=auto ./test.tar ./test.tar.tar |
| ---------- |:----------------------------:|:---------------------------------------------:| -------------------------------------------:|
| ext4       |   |   |   |
| btrfs      | none                    | none                                     | none                                   |
| ntfs       |   | are neat      |    $1 |

# Test Three a - modify xattr infors
+ get some infors after run `cp --reflink=always ./test.tar ./test.tar.tar` & `setfattr -n user.foo -v bar test.tar` & `setfattr -n user.foo -v rab test.tar.tar`

| filesystem | getfattr test.tar | getfattr test.tar.tar |
| ---------- |:-----------------:|----------------------:|
| ext4       |   |   |
| btrfs      |   |   | 
| ntfs       |   |   |

# Test Three b - modify xattr infors
+ get some infors after run `cp --reflink=auto ./test.tar ./test.tar.tar` & `setfattr -n user.foo -v bar test.tar` & `setfattr -n user.foo -v rab test.tar.tar`

| filesystem | getfattr test.tar | getfattr test.tar.tar |
| ---------- |:-----------------:|----------------------:|
| ext4       |   |   |
| btrfs      |   |   | 
| ntfs       |   |   |

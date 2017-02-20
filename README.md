# Get some copy infors from different filesystems under linux

# Platform
+ ubuntu 16.04.1 amd64
+ add a new disk (60G)
+ mkfs for ext4 & btrfs & ntfs with this disk
+ mount this disk to `/haha`
+ copy file `test.tar` (23G) into `/haha`

# Test One - get running time
+ run `cp` command line just like below

| filesystem | cp ./test.tar ./test.tar.tar | cp --reflink=always ./test.tar ./test.tar.tar | cp --reflink=auto ./test.tar ./test.tar.tar |
| ---------- |:----------------------------:|:---------------------------------------------:| --------------------------------------------------------:|
| ext4       | long time                    | Inappropriate ioctl for device                | long time                                                |
| btrfs      | long time                    | very fast                                     | very fast                                                |
| ntfs       | long time                    | Bad address                                   | long time                                                |

# Test Two - get xattr infors
+ `getfattr -n user.foo test.tar.tar` after run `setfattr -n user.foo -v bar test.tar` & `cp` just like below

| filesystem | cp ./test.tar ./test.tar.tar | cp --reflink=always ./test.tar ./test.tar.tar | cp --reflink=auto ./test.tar ./test.tar.tar |
| ---------- |:----------------------------:|:---------------------------------------------:| --------------------------------------------------------:|
| ext4       | No such attribute            | null                                          | No such attribute                                        |
| btrfs      | No such attribute            | No such attribute                             | No such attribute                                        |
| ntfs       | No such attribute            | null                                          | No such attribute                                        |

# Test Three - modify xattr infors under btrfs
+ `getfattr -n user.foo test.tar & getfattr -n user.foo test.tar.tar` after run `cp --reflink=always ./test.tar ./test.tar.tar` & `setfattr -n user.foo -v bar test.tar` & `setfattr -n user.foo -v rab test.tar.tar`

| name  | getfattr -n user.foo test.tar | getfattr -n user.foo test.tar.tar |
| ----- |:-----------------------------:|----------------------------------:|
| btrfs | user.foo="bar"                | user.foo="rab"                    | 

# Test Four - modify xattr infors under btrfs
+ `getfattr -n user.foo test.tar & getfattr -n user.foo test.tar.tar` after run `cp --reflink=auto ./test.tar ./test.tar.tar` & `setfattr -n user.foo -v bar test.tar` & `setfattr -n user.foo -v rab test.tar.tar`

| name  | getfattr -n user.foo test.tar | getfattr -n user.foo test.tar.tar |
| ----- |:-----------------------------:|----------------------------------:|
| btrfs | user.foo="bar"                | user.foo="rab"                    | 

# Test Five - modify xattr infors under ext4
+ `getfattr -n user.foo test.tar & getfattr -n user.foo test.tar.tar` after run `cp --reflink=auto ./test.tar ./test.tar.tar` & `setfattr -n user.foo -v bar test.tar` & `setfattr -n user.foo -v rab test.tar.tar`

| name  | getfattr -n user.foo test.tar | getfattr -n user.foo test.tar.tar |
| ----- |:-----------------------------:|----------------------------------:|
| ext4  | user.foo="bar"                | user.foo="rab"                    | 

# Test Six - modify xattr infors under ntfs
+ `getfattr -n user.foo test.tar & getfattr -n user.foo test.tar.tar` after run `cp --reflink=auto ./test.tar ./test.tar.tar` & `setfattr -n user.foo -v bar test.tar` & `setfattr -n user.foo -v rab test.tar.tar`

| name  | getfattr -n user.foo test.tar | getfattr -n user.foo test.tar.tar |
| ----- |:-----------------------------:|----------------------------------:|
| ntfs  | user.foo="bar"                | user.foo="rab"                    | 

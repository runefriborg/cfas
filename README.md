cfas
====

Count files and sizes on linux faster than anyone else. More than 4 times the speed of 'find' and 'du' in most cases. Can count files and sizes for all users in a single pass.

This is created to identify large chunks of data and files hidden in subfolders on
large +10TB storage drives, where a normal 'find' or 'du' either takes forever or
does not identify the wanted hot-spots.

It is written in Python, but uses libc to increase the handling of folders with too many files for other tools.


<h4>Main features</h4>
 - Uses multiple processes to increase throughput when traversing networked filesystems.                             - Supports a progress option, which outputs a status for every N files inspected. Very useful for folders with >100.000 files.
 - Filters output based on a minimum of filecount and/or filesize. Great for identifying hotspots with >100.000 files.
 - Performs user-separated counting in one go, thus reducing the required execution time.
 - Counts hard-linked files once. (when -w 1 is used)
 - Output format is very parser friendly.
 - Can perform counting at any depth level.


<h4>Example Output</h4>
```html
> cfas 
          Files            Size Path
              1              41 /home/runef/BACKUP/github/cfas/.git/refs/heads
              0               0 /home/runef/BACKUP/github/cfas/.git/refs/tags
              2              73 /home/runef/BACKUP/github/cfas/.git/refs/remotes/origin
              3             115 /home/runef/BACKUP/github/cfas/.git/refs/remotes
              7             220 /home/runef/BACKUP/github/cfas/.git/refs
             10           14022 /home/runef/BACKUP/github/cfas/.git/hooks
              1             240 /home/runef/BACKUP/github/cfas/.git/info
              0               0 /home/runef/BACKUP/github/cfas/.git/branches
              2            8644 /home/runef/BACKUP/github/cfas/.git/objects/pack
              0               0 /home/runef/BACKUP/github/cfas/.git/objects/info
              1             781 /home/runef/BACKUP/github/cfas/.git/objects/b0
              1              86 /home/runef/BACKUP/github/cfas/.git/objects/97
              1             166 /home/runef/BACKUP/github/cfas/.git/objects/5d
             10            9995 /home/runef/BACKUP/github/cfas/.git/objects
              1             347 /home/runef/BACKUP/github/cfas/.git/logs/refs/heads
              1             153 /home/runef/BACKUP/github/cfas/.git/logs/refs/remotes/origin
              2             180 /home/runef/BACKUP/github/cfas/.git/logs/refs/remotes
              5             581 /home/runef/BACKUP/github/cfas/.git/logs/refs
              7             972 /home/runef/BACKUP/github/cfas/.git/logs
             47           30402 /home/runef/BACKUP/github/cfas/.git
             51           64625 /home/runef/BACKUP/github/cfas
```

More detailed output

```html
> cfas --max-depth=0 --user --status=1 --human-readable /faststorage/projectdata
# 19970 files/s 19972 1.4TB: /faststorage/projectdata/xx/yy
# 14629 files/s 34603 1.4TB: /faststorage/projectdata/xx/zz
# 11484 files/s 46090 5.5TB: /faststorage/projectdata/cc/sa
           User           Files            Size Path
          user1              20          276.8K /faststorage/projectdata
          user2           49154            4.1T /faststorage/projectdata
          user3             219            1.5T /faststorage/projectdata
  unknown(4432)            2211            1.7T /faststorage/projectdata
```



<h4>Benchmarks</h4>

Traversing a folder on a distributing file system. cfas is 4.4x faster than 'du'. 
```html
###> time cfas -d 0 -h /folder
          Files            Size Path
          49393            5.5T /folder

real	0m3.240s
user	0m4.256s
sys	0m3.073s

###> time du -s -h /folder
5.6T	/folder

real	0m14.265s
user	0m0.166s
sys	0m1.434s

###> time find /folder | wc -l
49394

real	0m6.914s
user	0m0.164s
sys	0m0.785s
```

Traversing a folder with more files on a distributed file system. cfas is 5.4x faster than 'du'. 
```html
###> time cfas -h -d 0 /folder2
          Files            Size Path
         545539           17.1G /folder2

real	0m20.767s
user	0m24.716s
sys	0m34.315s

###> time du -s -h /folder2
18G	/folder2

real	1m51.508s
user	0m0.599s
sys	0m8.078s

###> time find /folder2 | wc -l
545540

real	0m25.094s
user	0m0.387s
sys	0m1.058s
```

Traversing a folder on a networked RAID6 file system (NFSv3). cfas is 4.5x faster than 'du'.

```html
###> time cfas -d 0 -h /folder3
          Files            Size Path
         156286            3.4T /folder3

real	0m4.535s
user	0m5.894s
sys	0m4.148s

###> time du -s -h /folder3
3.5T	/folder3

real	0m20.331s
user	0m0.334s
sys	0m1.717s

###> time find /folder3 | wc -l
156287

real	0m16.494s
user	0m0.200s
sys	0m1.343s
```

<h4>Examples</h4>

Search for folders which contain >50MB files excluding subfolders

```html
> cfas -h -k 50M --exclude-subdirs /usr
          Files            Size Path
              2           94.6M /usr/lib/locale
             51           63.4M /usr/lib/jvm/java-1.6.0-openjdk-1.6.0.0.x86_64/jre/lib
             37          138.3M /usr/lib64/valgrind
           1431          230.9M /usr/lib64
           1559          114.1M /usr/bin
```

Search for collections with more than 10000 files

```html
> cfas -h -n 10000 /usr 
          Files            Size Path
          10511           33.3M /usr/src/kernels/2.6.32-220.17.1.el6.x86_64
          10511           33.3M /usr/src/kernels/2.6.32-220.el6.x86_64
          21024           66.6M /usr/src/kernels
          21026           66.6M /usr/src
          14874           25.8M /usr/share/man
          12327          165.1M /usr/share/doc
          78215          922.1M /usr/share
          12388           79.3M /usr/include
         125707            2.1G /usr
```

Identify the user with >1,000,000 files in a shared folder. Get status output to stderr every 30 seconds.

```html
> cfas -h -n 1000000 -u -s 30 /shared
# 23291 files/s 699396 121.4GB: /shared/...
# 28219 files/s 1546016 157.2GB: /shared/...
# 25849 files/s 2321587 176.2GB: /shared/...
# 27539 files/s 3150395 209.9GB: /shared/...
# 27203 files/s 3966593 212.7GB: /shared/...
# 23770 files/s 4679721 215.1GB: /shared/...
# 22596 files/s 5361898 218.4GB: /shared/...
           User           Files            Size Path
          user1         5479600          218.8G /shared/aa/xx/cc
```


<h4>Help</h4>
```html
> cfas --help
cfas version 0.74 by Rune M. Friborg (updated 2014-10-15)
Usage:
  cfas [--file-limit N] [--size-limit K] [--max-depth D]
       [--exclude-subdirs] [--quiet] [--user] [--human-readable]
       [--status S] [DIRECTORIES TO WALK..]

Optional arguments:
  --help                show this help message and exit
  --max-depth D, -d D   only output results for folder level up to d.
                        -d 0 is similar to 'du -s'

  --file-limit N, -n N  output directory if it contains at least N files
  --size-limit K, -k K  output directory if the total size is at least K bytes.
                          Size suffixes such as P,T,G,M,K may be specified.
  
  --exclude-subdirs     all directories are counted separately

  --quiet, -q           do not output header (usefull for output parsing)
  --user, -u            add user column and split counts between users

  --human-readable, -h  output numbers in human readable format

  --status S, -s S      output status to stderr for every S second

  --workers P, -w P     number of workers to traverse dirs (default: 8)
```




cfas
====

Count files and sizes on linux faster than anyone else. More than 10 times the speed of 'find' and 'du' in many cases. Can count files and sizes for all users in a single pass.

This is created to identify large chunks of data and files hidden in subfolders on
large +10TB storage drives, where a normal 'find' or 'du' either takes forever or
does not identify the wanted hotspots.

It is Python, but uses libc to increase the handling of folders with too many files for other tools.


<h4>Main features</h4>
 - Uses multiple processes to increase throughput when traversing networked filesystems.                                                                                                              - Supports a progress option, which outputs a status for every N files inspected. Very useful for folders with >100.000 files.
 - Filters output based on a minimum of filecount and/or filesize. Great for identifying hotspots with many >100.000 files.
 - Performs user separated counting in one go, thus reducing the required execution time.
 - Counts hard-linked files once.
 - Output format is very parser friendly.
 - Can perform counting at any depth level.


<h4>Example Output</h4>
```html
[runef@fe1 cfas]$ ./cfas 
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


<h4>Benchmarks</h4>

Traversing a distributing file systems with no file cache enabled. cfas is 4.4x faster than 'du'. 
```html
# time ./cfas -d /folder -D 0 -H
          Files            Size Path
          49393            5.5T /folder

real	0m3.240s
user	0m4.256s
sys	0m3.073s

# time du -s -h /folder 
5.6T	/folder

real	0m14.265s
user	0m0.166s
sys	0m1.434s

# time find /folder | wc -l 
49394

real	0m6.914s
user	0m0.164s
sys	0m0.785s
```


<h4>Examples</h4>










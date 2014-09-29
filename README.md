cfas
====

Count files and sizes on linux faster than anyone else.

This is created to identify large chunks of data and files hidden in subfolders on
large +10TB storage drives, where a normal 'find' or 'du' either takes forever or
does not identify the wanted hotspots.

It is Python, but uses libc to increase the handling of folders with too many files for other tools.


Main features:
 - 



Man page:
cfas version 0.03 by Rune M. Friborg and Dan Soendergaard (updated 2014-09-26)
Usage:
  cfas [-h] [--directory DIRECTORY] --file-limit N --size-limit K
            [--recurse LEVELS] [--quiet] [--human-readable] [--progress P]

Optional arguments:
  -h, --help            show this help message and exit
  --directory DIRECTORY, -d DIRECTORY
                        the directory to walk
  --file-limit N, -n N  ouput directory if it contains at least N files
  --size-limit K, -k K  output directory if the total size of files is K
  
  --recurse LEVELS, -r LEVELS
                        the directory depth to summarise for every count.
                        -r 0 will count the files separately for every directory
  --quiet, -q           do not output header (usefull for output parsing)
  --user, -u
                        add user column and split counts between users
  --human-readable, -H
                        output numbers in human readable format
  --progress P, -p P    print progress to stderr for every P file counted


Outputs:





Performance tests:
TODO - test against du and find.










# MAC OSx and iOS internals useful commands

###uname
Get details of architecture and version history of Darwin  
uname -a will give all the info

### Architecture
* Darwin - OS core - kernel and UNIX shell enviornment
* Core frameworks - Graphics and Media layer - core frameworks, Open GL, QuickTime
* Application frameworks - Cocoa, Carbon and Java. iOS only Cocoa Touch
* UX - Aqua, Dashboard, Spotlight and accessibility

### UX
#### qlmanage
Quicklook is a way to quickly preview a file without opening it. The utility used is qlmanage  
To get thumbnail: qlmanage -t <filename>  
To get preview: qlmanage -p <filename> or press space bar in finder with file selected  
To manage quicklook server: qlmanage -m  

#### Spotlight
Quick search technology for OSx and iOS  
Magnifying glass icon in Mac or cmd+space  
Left swipe on home screen in iOS  
To issue search from terminal - mdfind <string>
To manage MetaData data database - mdutil
To diagnose spotlight - MDdiagnose
To list metadata attributes of a file - mdls <filename>  
To prevent indexing of a directory - create an invisible file .metadata_never_index  
this method didn't work, adding .noindex to the folder stopped spotlight search hits  

### Darwin - The UNIX Core

#### Shell
Bunch of choices  
* sh
* bash
* csh
* tcsh
* zsh  

#### File System
##### HFS+
* Case preserving but case insensitive in Mac - will show uppercase lowercase differently but commands will work either case
* Case sensitive in HFSX - Default in iOS  


#### UNIX system directories
* /bin - Unix binaries: Common UNIX commands like ls, rm, df
* /sbin - System binaries: binaries for system admin like file system management, network config, etc.
* /usr - User directory: third party software can install here. Contains bin, sbin, lib. /usr/lib is used for shared objects, also contains include/ subdirectory where standard C headers are kept
* /etc - Et Cetera: contains system config files. In OSx, this is a symbolic link to /private/etc
* /dev - BSD device files: contains hardware device representations
* /tmp - temporary directory: used for temp files, it is world writable. In OSx, symbolic link to /private/tmp
* /var - various: used for log files, mail store, print spool. In OSx, symbolic link to /private/var  
  
UNIX directories are invisible to finder. *chflags* can change an attribute called hidden to make it hidden to the GUI  
ls with -O flag will show the hidden attribute  
ls -Oal will list hidden directories with attribute showing  

#### OSx specific directories
* /Applications - default base for all applications
* /Developer - If xcode is installed, default installation point for all developer tools
* /Library - Data files, help, documentation for system applications
* /Network - Virtual dir for neighbour node discovery and access
* /System - Used for system files, contains a library subdirectory. Contains frameworks, kernel modules, fonts, etc.
* /Users - Home directory for each user
* /Volumes - Mount point for removable media and network file system
* /Cores - Dir for core dumps. Core dumps are created when a process crashes

Dump from my machine
[redacted]-osx:/ wolf43$ ls -Oal
total 51
drwxr-xr-x   34 root  wheel  -                  1224 Jan 28 02:47 .
drwxr-xr-x   34 root  wheel  -                  1224 Jan 28 02:47 ..
-rw-rw-r--    1 root  admin  -                     0 Sep 15  2015 .DS_Store
d--x--x--x    9 root  wheel  -                   306 Apr 28 15:35 .DocumentRevisions-V100
drwxr-xr-x    2 root  admin  -                    68 Jan  8  2015 .[redacted]
drwx------    5 root  wheel  -                   170 Apr 11  2015 .Spotlight-V100
d-wx-wx-wt    2 root  wheel  hidden               68 Jun  6  2015 .Trashes
----------    1 root  admin  -                     0 Sep 15  2015 .file
drwx------  480 root  wheel  -                 16320 May 23 13:12 .fseventsd
drwxr-xr-x@   2 root  wheel  hidden               68 Jan 28 02:32 .vol
drwxrwxr-x+  79 root  admin  sunlnk             2686 May 21 06:06 Applications
drwxrwxr-x    4 root  admin  -                   136 Jan 28 02:47 Incompatible Software
drwxr-xr-x+  71 root  wheel  sunlnk             2414 Mar 25 12:28 Library
drwxr-xr-x@   2 root  wheel  hidden               68 Jan 28 02:32 Network
drwxrwx---    3 root  Virex  -                   102 Nov  9  2015 Quarantine
drwxr-xr-x@   4 root  wheel  restricted          136 Jan 28 02:26 System
drwxr-xr-x    6 root  admin  -                   204 Jan 28 02:32 Users
drwxrwxrwt@   3 root  admin  hidden              102 May 23 09:10 Volumes
drwxr-xr-x@  39 root  wheel  restricted,hidden  1326 Jan 13 18:05 bin
drwxrwxr-t@   2 root  admin  hidden               68 Jan 28 02:32 cores
drwxr-xr-x    3 root  wheel  -                   102 Jan 17 21:55 data
dr-xr-xr-x    3 root  wheel  hidden             7623 Apr 28 15:35 dev
lrwxr-xr-x@   1 root  wheel  restricted,hidden    11 Jan 28 02:30 etc -> private/etc
dr-xr-xr-x    2 root  wheel  hidden                1 Apr 28 15:35 home
-rw-r--r--@   1 root  wheel  hidden              313 Jul 31  2015 installer.failurerequests
dr-xr-xr-x    2 root  wheel  hidden                1 Apr 28 15:35 net
drwxr-xr-x@   6 root  wheel  hidden              204 Feb 21 09:04 opt
drwxr-xr-x@   6 root  wheel  hidden              204 Jan 28 02:32 private
drwxr-xr-x@  59 root  wheel  restricted,hidden  2006 Jan 28 02:31 sbin
lrwxr-xr-x@   1 root  wheel  restricted,hidden    11 Jan 28 02:30 tmp -> private/tmp
drwxr-xr-x@  11 root  wheel  restricted,hidden   374 Jan 28 02:36 usr
lrwxr-xr-x@   1 root  wheel  restricted,hidden    11 Jan 28 02:31 var -> private/var  

#### Bundles
Standardized heirarchical structure that holds executable code and the resources used by that code  
It is used for applications, frameworks, plugins, widgets and even kernel packages  
* Contents/  
  * CodeResources/
  * Info.plist
  * MacOS/
  * PkgInfo
  * Resources/
  * Version.plist
  * _CodeSignature/
    * CodeResources

###### Info.plist
plist has 3 formats
* XML: most common
* Binary: known as bplist
* JSON: not very common  
More info using man plist  
Conversion using plutil  
Info.plist entries  
<https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/AboutInformationPropertyListFiles.html#//apple_ref/doc/uid/TP40009254-SW1>  
PkgInfo = CFBundlePackageType + CFBundleSignature  

 

# MAC OSx and iOS internals useful concepts and commands
## Origin of content
Most of this content is from the awesome textbook <http://www.amazon.com/Mac-OS-iOS-Internals-Apples/dp/1118057651/ref=sr_1_2?ie=UTF8&qid=1464466126&sr=8-2&keywords=mac+os+x+internals>  
If you are serious about learning Mac OS X and iOS internal, get a copy of the book and read it. Think of this document as a quick reminder document of userful concepts and commands
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

To get complete list, run "ls -Oal" from /   

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

##### Resources
This directory contains all the resources the applicaiton needs. This can contain any type of file the application might need  
I'll add a lot more info about the code signing resources in the Mac OS X code signing project  


#### Frameworks
*  Frameworks are libraries that are specific to OS X
*  They are closed source as opposed to Darwin libraries
*  They also have a bundle format which is slightly different from the bundle format of applications discussed above  

##### Location
* /System/Library/Frameworks: Apple supplied frameworks reside here
* /Library/Frameworks: 3rd party frameworks reside here
* ~/Library/Frameworks: User supplied frameworks reside here  

##### Cocoa
This is the framework used to develop applications for OS X <https://en.wikipedia.org/wiki/Cocoa_(API)>  
Apple is trying to get developers to use Swift<https://developer.apple.com/swift/> as the primary programming language but objective C is still very much supported. Cocoa API is also accesible through various other programming languages(wiki page linked above has more info)  
You can have some fun with speech to text framework <https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/SpeechSynthesisProgrammingGuide/SpeechOverview/SpeechOverview.html>  

#### Libraries
* These are the UNIX system libraries that all UNIX folks will be familiar with
* They are stored in /usr/lib directory
* On OS X, they have .dylib extension instead of .so extension
* libc is is libSystem.B.dylib library Apple provides

#### Widgets
* These are basic web pages that can be presented by dashboard
* They are written just like webpages with html, js and css
* They reside as bundles with .wdgt format in /Library/Widgets directory  

#### BSD and Mach API
* It is also possible to work directly with UNIX and Mach API for application development  
* Starting with Leopard(10.5), OS X is a certified UNIX implementation compliant with portable operating system interface(POSIX)
* Mach system calls are also avaiable in the user mode and are negative to keep them distanct from positive POSIX system calls

### XNU components
#### Mach microkernel
* It performs process and thread abstarction
* Manages virtual memory management
* Task scheduling
* Interprocess communication and messaging
#### BSD layer
* It provides UNIX process model
* POSIX threading model
* UNIX users and groups
* Network stack
#### libkern
It is a built in C++ library that allows kernel components to be written in C++(in addition to C and assembly like most kernels are created)
#### I/O kit
It is a I/O device driver framework that runs in libkern(C++) enviornment
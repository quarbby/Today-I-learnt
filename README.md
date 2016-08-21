# Today-I-learnt

A collection of little snippets that we learnt along the way.

### 21/08/16 - Linux Journey and Vim Game
[Learning Linux Journey](https://linuxjourney.com)
[Vim game](http://vim-adventures.com)

### 20/08/16 - USA Olympics Gymnastics Team
1996 was the first time the Women Olympics team won gold. That's 20 years ago. There were 7 members in the team then, called The Magnificent Seven. The Gymnastics team has since reduce the number of members to 6 then 5. Following this Olympics, the number of girls will be reduced to 4. 

### 16/08/16 - Disassemble a C++ code in Visual Studio 2010
Put a break point. Press F5. Upon break, type CTRL-ALT-D.

### 15/08/16 - Running a script in cron on reboot
- Edit cron commands: `crontab -e`
- Add `@reboot /usr/bin/python /home/user/Desktop/test.py` in the file to run
- Reboot runs on startup and reboot

### 15/08/16 - Echo new line and escape characters in batch files 
`echo.` for new line. `^` is escape character, i.e. `^\`

### 12/8/16 - Disable IP Tables in CentOS
```
$sudo chkconfig iptables off
```

### 16/08/16 - In view of Schooling's achievement, TIL
USA has not won any Olympic medals in handball, badminton and table tennis.

### 13/06/16 - Add a local apt-mirror to Ubuntu
Edit `/etc/apt/sources.list` to change the Ubuntu server with your own IP Address server

### 02/07/16 - Mount a Windows Share in Linux
1. Create <FOLDER_NAME> at C:\
2. Right click > Share With... > Everybody. Give Read/Write permissions
3. Create folder in Linux at path, <LINUX_FOLDER>
4. Mount the drive (Note the lack of spacing after the commas)
```
$sudo mount -t cifs //<IP_ADDRESS>/<FOLDER_NAME> <FOLDER_IN_LINUX>
-o username=<WINDOWS_USERNAME>,password=<WINDOWS_PASSWORD>,
uid=<LINUX_USER>,gid=<LINUX_USER>
```
Example:
```
$sudo mount -t cifs //192.168.14.91/kibana /home/super/Desktop/kibana
-o username=lynnette,password=lynnette,
uid=lynnette,gid=lynnette
```

Note: If you don''t set uid, gid, the folder will be mounted as `root`, and you will need `root` privileges to read/ write to it.

### 04/07/16 - Setting up Git Repo on a Server

```
$sudo apt-get install git-core
$mkdir hellogit.git
$cd hellogit.git
$git init --bare
```

### 04/07/16 - Find all files in which contents contains a specific string
```
$grep -rnl <DIRECTORY> -e <STRING_NAME>
$grep -rnl './' -e '192.168'
```

### 04/07/16 - Which version of Linux is running
```
$cat /etc/system-release
OR (For Ubuntu)
$lsb_release -d
```

### 04/07/16 - Disable Cross Origin Policy in Firefox
In the browser bar, type `about:config`.

Type: `security.fileuri.strict_origin_policy` and set to `False`

### 04/07/16 - Yum pointing to local repository mirror
Yum is a software management system for CentOS systems.
It is similar to aptitude for Ubuntu systems.

You can set up a yum local repository (I don''t know how),
but when you do any `yum install`, you can only install from the compatible versions.
For example, CentOS 6 can only install packages from a mirror that contains CentOS 6 packages
and not CentOS 7 packages.

* `$sudo vim /etc/yum.repos.d/CentOS-Base.repo`
* Comment out all `mirrorlist=http://mirrorlist.centos.org`...
and `baseurl=http://mirror.centos.org/`...
* Add instead your own url, i.e. `baseurl=http://192.168.201.203/repos/centos/6/8/`
* Just keep [base], the rest can delete

### 04/07/16 - Samba shared folder in Linux
1. Install samba: `$sudo yum install samba`
2. Set password for samba: `$sudo smbpasswd -a <USER_NAME>`
3. Create a directory to be shared: `mkdir /home/<USER_NAME>/<FOLDER_NAME>`
4. Edit samba config file:

```
$sudo vim /etc/samba/smb.conf

Add to the end of the file:
[<NAME_OF_SHARE>]
path = /home/<USER_NAME>/<FOLDER_NAME>
guest ok = yes
read only = no
```
Note that <NAME_OF_SHARE> should be less than 12 characters

An example would be:

```
[sharedInd]
path = /home/super/Desktop/sharedInd
guest ok = yes
```

5. Restart samba service: `$sudo smbd reload` for CentOS,
`$sudo service smbd restart` for Ubuntu
6. Test whether the `smb.conf` has any syntax errors: `testparm`
7. Opening the Linux samba share on Windows:
`//<IP_ADDRESS>/<FOLDER_NAME>`, i.e. `//192.168.201.108/sharedInd`
Note that the full path is not required; the folder will be automatically detected
8. Mounting the shared folder on another Linux version (02/07/16 - Mount a Windows Share in Linux)

### 04/07/16 - Kibana, Font Awesome, Bootstrap versions
Kibana 3 uses [Font Awesome 3.2.1](fontawesome.io/3.2.1/icons/), Bootstrap v2.2.1

### 07/07/16 - Windows 10 startup program
If you want to put files (i.e. `.bat`) files to run on Windows startup
1. Windows Key + R
2. Type: shell:startup
3. Put your scripts in the startup folder that opens

### 11/07/16 - Home IP Address
On windows, HOME is `127.0.0.1`. On Linux, HOME is `0.0.0.0`

### 11/07/16 - Download a node package to install offline
1. On Internet machine, `npm install --no-bin-link <PACKAGE_NAME>`
2. `npm pack` to get a tarball.
3. On non-internet machine, `npm install <tarball name>`
OR
2. Copy out the `node_modules` with `.zip/ .rar/ .tar`
3. Extract the bundle in offline environment

### 12/07/16 - Easily print Github markdown
https://gitprint.com

### 12/07/16 - Update a file in .jar file
`$zip -u stuff.jar file.txt`

### 12/07/16 - Update a jar file
`zip -u <JAR_FILE_NAME>.jar <FILE_NAME>`

For example:
`zip -u ParseNIndexCopy.jar parser.properties`

### 12/07/16 - Explode and compact a war file
Explode war file: `jar -xvf A.war`
Compact everything in this directory to a war file: `jar -cvf A.war .`

### 12/07/16 - Copy in Linux and force overwrite
`\cp <SOURCE> <DESTINATION>`

### 13/07/16 - A Gulp Application
(Glup.js)[http://gulpjs.com/] helps automate your web workflow to watch your files for changes and build them. More information can be found on the (Github Repo)[https://github.com/gulpjs/gulp/blob/master/docs/getting-started.md]

```
$npm install -g gulp
$gulp start
```

### 13/07/16 - Jade template Engine
(Jade)[http://jade-lang.com/tutorial/] is an interesting HTML template engine. You don't have to add HTML tags but it is white space sensitive.

### 20/7/16 - To Add an entry into /etc/fstab
You need to use the following format:
```
string="echo $SHAREDFOLDER $SHAREDFOLDERLOCALMOUNT cifs username=$WINDOWSSHAREUSER,password=$WINDOWSSHAREPASSWORD,uid=super,gid=super  >> /etc/fstab"

sudo sh -c "$string"
```

Because if you just `sudo echo <STRING> >> /etc/fstab`,
you will get permission denied. This is because sudo elevates
your permission but >> uses reduced permissions again.

### 26/7/16 - UNIX tail -f vs tail -F
From man page 

With --follow (-f), tail defaults to following the file descriptor, which means that even if a tail'ed file is renamed, tail will continue to track its end. This default behavior is not desirable when you really want to track the actual name of the file, not the file descriptor (e.g., log rotation). Use --follow=name in that case. That causes tail to track the named file in a way that accommodates renaming, removal and creation.

### 26/7/16 - Tail -f a log file from a server 
`ssh user@serverIP sudo tail -f <FILE_NAME>`

If ssh failed due to tty allocation: 
`ssh -t user@serverIP sudo tail -f <FILE_NAME>`

### 27/7/16 - Windows computer get Serial Number
Go to command prompt. Type: `wmic bios get serialnumber`

### 2/8/16 - Bash previous command substitution

`^x^y` to replace first instance of `x` with `y` in previous command.
`!!:gs/x/y/` to replace all instances of `x` with `y` in previous command.

```
$ ./a.out < B-small-practice.in > B-small-practice.out
$ !!:gs/small/large/
  ./a.out < B-large-practice.in > B-large-practice.out
```

### 3/8/16 - Git tree in terminal

```
git log --graph --oneline --all
```

### 3/8/16 - Git clone with username and password
```
git clone https://username:password@github.com/username/repository.git
```

### 4/8/16 - Bash loop through all directories
```
for d in */ ; do
    echo $d
done
```

### 8/8/16 - Installing g++, Git, Vim, and Bash on Windows

- Install Git for Windows to get Git Bash (includes Bash, basic Unix tools and vim, reads `.bashrc` and `.vimrc`)
- Install gVim for a better Windows Vim (reads `.vimrc` and `.gvimrc`)
- Install [Nuwen's MinGW Distro (without Git)](https://nuwen.net/mingw.html) for g++
- Add `PATH=$PATH:c/MinGW/bin` to `.bashrc` to use MinGW binaries in Git Bash (default MinGW shell is terrible)

### 8/8/16 - Mount windows share into /etc/fstab
- Windows share at C:\, i.e. <IP_ADDRESS>/<SHARED_FOLDER_NAME>
- Folder created in Linux machine at <LINUX_SHARE_FOLDER_PATH>
```
$sudo vim /etc/fstab

add to end of file

//<IP_ADDRESS>/<SHARED_FOLDER_NAME> <LINUX_SHARE_FOLDER_PATH> cifs username=username,password=password,file_mod=0777,dir_mod=0777
```
- Eg: `//192.168.201.91/neo4j /home/super/Desktop/mountFolder cifs username=lynnette,password=lynnette,file_mod=0777,dir_mod=0777`
- On Ubuntu you can `sudo mount -a` to reboot, CentOS just reboot
- [Ubuntu's Guide to mounting Window's share](https://help.ubuntu.com/community/MountWindowsSharesPermanently)
- [Wiki guide to fstab](https://en.wikipedia.org/wiki/Fstab)



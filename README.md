# Today-I-learnt

### 08/10/16 - Untar and tar a file

Because I keep forgetting

```
$tar -czf archive.tar.gz /path/to/directory
$tar -xzf archive.tar.gz
```

[And a lot more commands with tar](http://www.howtogeek.com/248780/how-to-compress-and-extract-files-using-the-tar-command-on-linux/)

### 03/10/16 - Apple's first iPhones

[The first iPhones were buggy as hell](http://gizmodo.com/the-iphones-first-demo-was-buggy-as-hell-1441324523 )

> Hours of trial and error had helped the iPhone team develop what engineers called “the golden path,” a specific set of tasks, performed in a specific way and order, that made the phone look as if it worked.

### 01/10/16 - Ping Pong

In case you want to pingtest your network and put a date to the output string and then append that string to a text file:
```
ping 8.8.8.8 | while read poing; do echo $(date):$pong"; done >> pingtest.txt
```
Very useful for `tail -F pingtest.txt` later to check network connectivity. 

### 26/09/16 - NatCat UDP

In other words, *talking between two linux VMs*. Because it is a pain to keep walking up and down but there is an elegant solution.

At the sender side: `netcat -u <host> <port>`. So that's `netcat -u 192.168.101.111 9888`. That's opening a UDP connection to the IP address 192.168.101.111 on port 9888.

At the receiver side: `netcat -ul <port>`. For the sender in the previous paragraph, you would use `netcat -ul 9888` to open a UDP listener at port 9888. So you'll be able to communicate easily. 

Of course `netcat` can be used for way more than this. [Like that](https://www.digitalocean.com/community/tutorials/how-to-use-netcat-to-establish-and-test-tcp-and-udp-connections-on-a-vps)

### 23/09/16 - Turning off IP tables in CentOS6

```
$service iptables save
$service iptables stop
$chkconfig iptables off
```

`chkconfig` saves the configuration. sysadmin skills levelling up... slowly but surely

### 22/09/16 - Replacing words in files

To replace all instances of `apple` with `orange` (technically `apply` can be a regular expression) in `file`, run

```
sed -i 's/apple/orange/g' file
```

The `-i` option means *in-place*. This command is especially useful when combined by `ssh` in a for loop (use case: editing `/etc/hosts` of a machines in a cluster!).

To delete lines containing `re` in `file`, run

```
sed -i '/re/d' file
```

### 18/09/16 - Switching working directories with cd

`cd -` switches you to your previous working directory (`pwd`). Doing it again switches you back!

### 18/09/16 - SSH commands

`ssh user@hostname command` does what it says: it logs in as `user` at `hostname`, executes `command`, and leaves.

For the lazy, having a `~/.ssh/config` file is useful:

```
Host cl
 User cydf2
 HostName sandy.cl.cam.ac.uk
 IdentityFile ~/.ssh/id_rsa
 ServerAliveInterval 60
```

I only have to type `ssh cl`.

### 18/09/16 - Inverse grep

`grep -v pattern` returns lines that *do not* match `pattern`. I had to use it once (and piped it to `wc -l`, of course).

### 18/09/16 - Shell history

We all know `history`. `!42` executes the 42nd entry in `history`. `!!` or `!-1` executes the previous command.

`!*` returns the previous command minus the first word (i.e. just arguments), `!^` returns the first argument of the previous command, and `!$` returns the last argument of the previous command.

### 18/09/16 - Subshells

Let's say you're writing a script which involves a `cd` to some other directory followed by some other commands there. Unfortunately, this will not work.

What needs to be done is:

```
(cd somedir && \
command1 && \
command2 && \
command3)
```

The parentheses causes execution of the commands in a subshell.

### 18/09/16 - File

`file filename` tells you the type of file of `filename`.

### 18/09/16 - Shell variables

```
$ variable=value // no whitespace!
$ export variable
$ export variable=value
$ variable=value command // variable set to value for just this command
```

`set` shows all shell variables. `printenv` shows all exported environment variables.

### 18/09/16 - Job control

`command &` runs the command in the background, with `fg` to bring it into foreground (console) and `Ctrl-Z` (suspend)  followed by `bg` to run it in background. No more opening multiple console windows!

`fg`, `bg`, and `kill` accept process ID, `%+jobnumber`, and `%+commandname` as arguments.

`jobs` shows jobs in the current shell.

### 18/09/16 - Quotes

- Single quotes: suppresses all special character meanings
- Double quotes: suppresses all special character meanings, except for `$`, `\`, and `` ` ``.
- Backslash: suppresses all special character meanings for next character (we all know this already, right?)

In more complicated scripts with variables and string outputs, the use of single quotes, double quotes, and backticks (to evalute commands!) is very important.

I think that we should quote *strictly*, i.e. single quotes for all strings unless there is a need to include variables, special characters, or inline evaluation of commands, in which case double quotes are used.

### 18/09/16 - Expansions

```
$ echo {1..10}
1 2 3 4 5 6 7 8 9 10
$ echo document{A,B,C}.pdf
documentA.pdf documentB.pdf documentC.pdf
$ echo ${HOME}
/home/darren
$ echo $HOME // alternative to ${variablename}
/home/darren
$ echo $(whoami)
darren
$ echo `whoami` // alternative to $(command)
darren
```

### 18/09/16 - Double hyphens

Let's say, for some reason, you have a file that starts with a hyphen, and you want to remove it with `rm`. Unfortunately running `rm` the usual way will probably throw an error because your file name will be parsed as options, not an argument.

`--` signals (in many tools) that subsequent words are arguments, not options, e.g. `rm -- -i` removes the file called `-i`.

### 18/09/16 - File redirection

Most people probably know `>` (send output to file) and `>>` (append output to file). However it must be noted that it is stdout that is sent, not stderr, although both are usually sent to the console.

To send *both* stdout and stderr to the same file, it suffices to add `2>&1` after the first redirection:

```
command > filename 2>&1
```

What's going on? stderr (with file descriptor 2) is sent to (think pointer) stdout (file descriptor 1). File descriptor 0 is stdin.

What if stderr is flooding your console? Use `2>/dev/null`.

### 18/09/16 - Bash sequential commands

A semi-colon executes commands in sequence.

```
command1 ; command2 ; command3
```

A double ampersand (`AND`) or double pipe (`OR`) ([short-circuit evaluation] (https://en.wikipedia.org/wiki/Short-circuit_evaluation)) executes *conditionally* commands in sequence.

```
command1 && command2 // command2 will execute if command1 succeeds
command1 || command2 // command2 will execute if command1 fails
```

### 18/09/16 - Output files in directory in Windows

An equivalent of `ls` in Unix. Shift + Right click on the directory you want and Open Command Window. Then, `dir /b > dir.txt`. dir.txt comes out a list of files in the folder. This is windows. In Linux its just `ls > dir.txt`. Sweet and Simple.

### 15/09/16 - :p in bash

:p is not an emoticon. Type `!sa:p` and you'll see what the previous command you executed that starts with sa is. `:p`, that's colon with a lower case p, would show you the command then you can press the enter key if that's what you want to execute.

Also, [Advancing in the Bash Shell](http://samrowe.com/wordpress/advancing-in-the-bash-shell/)

### 15/09/16 - su hyphen

The difference between `su user` and `su - user` ([ArchWiki](
https://wiki.archlinux.org/index.php/Su)):

> The hyphen has two effects:
>
> 1) switches from the current directory to the home directory of the new user (e.g., to /root in the case of the root user) by logging in as that user
>
> 2) changes the environmental variables to those of the new user as dictated by their ~/.bashrc. That is, if the first argument to su is a hyphen, the current directory and environment will be changed to what would be expected if the new user had actually logged on to a new session (rather than just taking over an existing session).

In fact, avoid `su` if you can `sudo`.

### 14/09/16 - Local NTP server

If you need to keep the clocks of your network computers synchronised, you need to designate one computer as the reference (NTP server); the others are NTP clients. I'm assuming this network is not connected to the Internet, or to put it simply, is not connected to any authoritative low-stratum NTP server like `time.nist.gov`.

#### NTP server

Add the following in `/etc/ntp.conf`:
```
restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap nopeer
...
server 127.127.1.0
fudge 127.127.1.0 stratum 10
```

modifying `192.168.1.0` and `255.255.255.0` to suit your network. The magic IP address is `127.127.1.0`; leave that alone.

Without the last two lines, any connecting clients will see that your server is stratum 16, i.e. lousy.

#### NTP client

Add the following in `/etc/ntp.conf`:
```
server XXX.XXX.XXX.XXX
```

where `XXX.XXX.XXX.XXX` is the IP address (or hostname) of your NTP server.

On the server and client, run `ntpq -p`, `ntpstat`, and `ntpdate -q XXX.XXX.XXX.XXX` for juicy NTP statistics.

### 10/9/16 Watson Developer Cloud and Emotion Analysis

TIL that [Watson Developer Cloud Tone Analyzer](http://www.ibm.com/watson/developercloud/tone-analyzer/api/v3/?python#post-tone) only classifies the tones `Anger, Disgust, Fear, Joy, Sadness`. As compared to the six [Universally Recognised Face Expressions of  Emotions](https://www.kairos.com/blog/the-universally-recognized-facial-expressions-of-emotion) -- Yes I know that Face Emotions and Tone Emotions are slightly different, but for the purposes of this argument, let's leave it first -- Watson does not have surprise. This is because *surprise* is an emotion that is usually very quick, then follower by *fear* or *joy*. 

In summary, TIL that after less than a second of shock, I will be feeling in awe (*joy*) or scared (*fear*). 

###  09/09/16 Writing multiple lines to a file in bash

Due to the need for creation of multiple scripts from one script in bash

```
cat >/home/ubuntu/Desktop/script.sh <<EOL
line 1, ${var1}
line 2, ${var2}

EOL
```

If you don't want the variables to be replaced, surround EOL with single quotes 
```
cat >/home/ubuntu/Desktop/script.sh <<'EOL'
line 1, ${var1}
line 2, ${var2}

EOL
```

### 30/08/16 Verifying RPM packages

Don't have the checksums for a `.rpm` file? Run `rpm -Kv X.rpm` on your favourite RPM-based Linux distro.

### 30/08/16 Transferring files without `scp`

Or you *do* have `scp`, but you're just really lazy to run `ssh-keygen` and stuff.

Go to the folder containing the file you want to share and run `python -m SimpleHTTPServer`. A HTTP server on port 8000 will be created.

Assuming the receiving machine is on the same network, `curl -O 192.168.X.Y:8000/file.extension` will do the job. Yes, you don't have `wget` too.

### 29/08/16 Computing checksums in PowerShell

`Get-FileHash [file] -Algorithm MD5` or `SHA256`

Extra tip: you can open `powershell` in `cmd`. But `bash` is still king!

### 28/08/16 Latency versus Bandwidth

Or "*I Regret Not Paying Attention in My Computer Networks Course*": [It's the Latency, Stupid](https://web.archive.org/web/20141204094820/http://www.stuartcheshire.org/rants/Latency.html)

> Years ago David Cheriton at Stanford taught me something that seemed very obvious at the time -- that if you have a network link with low bandwidth then it's an easy matter of putting several in parallel to make a combined link with higher bandwidth, but if you have a network link with bad latency then no amount of money can turn any number of them into a link with good latency.

### 27/08/16 - You can regex replace an emoji
In Chrome console
```
> '👪'.replace(/👩/g, '👱')
<- "👪"
```

### 26/08/16 - PanChromatic Images 
[Gis answer](http://gis.stackexchange.com/questions/113896/panchromatic-image-association-with-high-spatial-resolution)

> [remote sensing] A single band image generally displayed as shades of gray.
>
> Panchromatic images are created when the imaging sensor is sensitive to a wide range of wavelengths of light, typically spanning a large part of the visible part of the spectrum.

### 25/08/16 - Patching Apollo 14

Or "*What I Don't Want to Ever Have to Do in My Life*".

#### [NASA](https://www.hq.nasa.gov/alsj/a14/a14AbortDiscrete.html)

> The intermittent short circuit of the ABORT button represented the most dramatic moment for the MIT computer folks in the entire Apollo program. They had between three and four hours to find a way for the computer to ignore it, test the fix at Grumman, then test it in Houston before sending it to the crew in time for PDI .

#### [Wikipedia](https://en.wikipedia.org/wiki/Apollo_14#Launch_and_flight_to_lunar_orbit)

> If the problem recurred after the descent engine fired, the computer would think the signal was real and would initiate an auto-abort, causing the ascent stage to separate from the descent stage and climb back into orbit.
>
> NASA and the software teams at the Massachusetts Institute of Technology scrambled to find a solution, and determined the fix would involve reprogramming the flight software to ignore the false signal. The software modifications were transmitted to the crew via voice communication, and Mitchell manually entered the changes (amounting to over 80 keystrokes on the LM computer pad) just in time.

### 25/08/16 - Building resilient systems

#### [Netflix Chaos Monkey](http://techblog.netflix.com/2015/09/chaos-engineering-upgraded.html)

> Several years ago we introduced a tool called Chaos Monkey. This service pseudo-randomly plucks a server from our production deployment on AWS and kills it. At the time we were met with incredulity and skepticism. Are we crazy? In production?!?
>
> ...
>
>  Building on the success of Chaos Monkey, we looked at an extreme case of infrastructure failure. We built Chaos Kong, which doesn’t just kill a server. It kills an entire AWS Region.

#### [Space software](https://www.fastcompany.com/28121/they-write-right-stuff)

> Four identical machines, running identical software, pull information from thousands of sensors, make hundreds of milli-second decisions, vote on every decision, check with each other 250 times a second. A fifth computer, with different software, stands by to take control should the other four malfunction.
>
> ...
>
> This software never crashes. It never needs to be re-booted. This software is bug-free. It is perfect, as perfect as human beings have achieved. Consider these stats : the last three versions of the program — each 420,000 lines long-had just one error each. The last 11 versions of this software had a total of 17 errors.
>
> ...
>
> The group's most important creation is not the perfect software they write — it's the process they invented that writes the perfect software.
>
> ...
>
> The central group breaks down into two key teams: the coders - the people who sit and write code — and the verifiers — the people who try to find flaws in the code.

### 24/08/16 - Visual Studio & Eclipse shortcuts
| Function        | Visual Studio           | Eclipse  |
| ------------- |:-------------:| -----:|
| Go to method | F12 | F3 |
| Run     | F5 | Ctrl + F11 |
| Run with Debugging     | F5     |   F11 |
| Stop  | Shift + F5 | Stop button |
| Continue | F5      |    F8 |
| Step Into | F11 | F5 |
| Step Over | F10 | F6 |
| Step Out  | Shift + F11 | F7 |

### 23/08/16 - Bash keyboard shortcuts

These are very useful when typing long commands.

`Ctrl-C` - cancels current input, and yes it also kills the current process

`Ctrl-L` - clears screen

`Ctrl-U` - deletes everything from cursor to start of line

`Ctrl-K` - deletes everything from cursor to end of line

`Ctrl-A` - moves cursor to start of line

`Ctrl-E` - moves cursor to end of line

`Ctrl-W` - deletes previous word

#### "But I will never use this!"

Arrow keys are for the weak, but I'm still weak for now because I don't use Vi mode in Bash. But I digress.

`Ctrl-C` is not a great command because it creates a new prompt (hence ugly). I use `Ctrl-E` (move cursor to end of line) and `Ctrl-U` to delete the current input. If I'm typing at the end of the line and I want to clear the input, I just use `Ctrl-U`.

`Ctrl-W` is useful if you mistyped an argument. I rarely use `Ctrl-K`, because such situations don't occur often.

`Ctrl-A` is useful if you want to append something to the front of some previous command, say.

"But I can use `!!`" I hear someone scream. Yes, `sudo !!` is very useful. However today I found myself stuck in a SQL shell with no support for `!!`. The stupid way to append `EXPLAIN` to a previous SQL query is to use the left arrow key and traverse back to the very beginning. A smarter way is to use `Ctrl-A` to jump there. The smartest way is still unknown, suspected to be in the mind of some wizened SQL guru in his sunlight-deprived room.

### 21/08/16 - Linux Journey and Vim Game
[Learning Linux Journey](https://linuxjourney.com)
[Vim game](http://vim-adventures.com)

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



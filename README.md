# Today I Learnt 2018

### 31/01/18 - tqdm. Easy to implement progress bar on the Python console
There are times when you are just looping around doing something and wondering when it will finish. Printing dots and astericks with the ```print('*',end='')``` aint't really that cool either.
tqdm gives the progress bar, progress count, time taken and time to finish by approximation. Kinda cool when i was processing videos of frames.
```
pbar = tqdm(total=100)
for i in range(100):
    pbar.update(1)
pbar.close()
```

### 29/01/18 - $PS1 generator

You can generate your own `$PS1` (aka custom bash prompt)
[here](http://bashrcgenerator.com/).

For reference, here's mine:

```
PS1="\[$(tput bold)\]\[\033[38;5;10m\]\u@\h\[$(tput sgr0)\]\[$(tput sgr0)\]\[\033[38;5;15m\] [\$?] \[$(tput sg    r0)\]\[\033[38;5;11m\]\w\[$(tput sgr0)\]\[\033[38;5;15m\]\n\\$ \[$(tput sgr0)\]"˧
```

Who knew a two-line `$PS1` would be so useful? It also contains the
exit status of the previous command.

### 29/01/18 - Dotfiles

Way overdue update as I was learning this while setting up my new
ThinkPad. The venerable ArchWiki has a handy [table](https://wiki.archlinux.org/index.php/Bash#Configuration_files), but I'll make it simpler (for limited use cases only). Here's how I
configure my ThinkPad.

- `~/.profile`: exports environment variables.
- `~/.bashrc`: sets color prompts, `$PS1`, exports aliases and functions
- `~/.xsessionrc`: loads `.profile` and sets X session stuff (DPI, terminal colors)
- `/etc/rc.local`: writes values to `/sys/devices/...` to set mouse pointer stuff

Cases:
- login shells: loads `~/.profile` and `~/.bashrc`
- X display manager: loads `~/.xsessionrc`, which in turn loads `~/.profile`
- non-login shells: loads `~/.bashrc` (`~/.profile` would have already been loaded in X)

This is really the simplest configuration I could come up with without
having too many dotfiles while also covering all use cases
(X, SSH, TTY).

### 29/01/18 - Ethernet cables

- Cat 5: 100 Mbps (100 metres)
- Cat 5e: 1 Gbps (100 metres)
- Cat 6: 1 Gbps (100 metres), 10 Gbps (55 metres)
- Cat 6a: 10 Gbps (100 metres)

### 29/01/18 - Gateways on private networks

If you're setting up a private network (e.g. `192.168.0.0/16`), you
don't need a gateway *unless* you need to access a network on another
subnet. This means that you don't need to set a gateway in your network
settings.

### 29/01/18 - Reverting a revert in GitLab

Situation: you've accidentally merged a merge request in GitLab. Then
you click `Revert`. However, when you try to create a new merge request
for the branch to be merged (call it `feature`), you find that there are
no changes between `feature` and the target branch (call it `dev`).

What happened: when you clicked `Revert`, GitLab *added* extra commits
on top of `dev`. These commits are *undo* commits, i.e. they undo the
changes of `feature`. This means that the `feature` tree is still a
*sub-tree* of `dev`, so it's already *merged*.

How to fix it, fast: you can run this:

```
git checkout <commit> -- <files>
```

where `<commit>` the latest commit of `dev` before the merge request
was merged. This stages the changes to *undo the undo*; commit the
changes and this will make `feature` a super-tree of `dev`.

### 29/01/18 - Reading files in Java 8

Enough with `BufferedReader`s and `FileInputStream`s; behold Streams in
Java 8:

```
Stream<String> stream = Files.lines(Paths.get(filePath));
```

### 23/01/18 - Checksums in Windows 

To compute the checksum in windows command line without downloading extra files, use `CertUtil -hashfile C:\myFile.txt MD5`, where `myFile.txt` is the file you want to compute an MD5 sum for. 

More information [here](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc732443(v=ws.11)#BKMK_menu)

### 15/01/18 - Logstash ingestions 

Logstash has been very useful in ingesting data into Elastic Search, after which you can use Kibana to view and visualise the data. Really simple and cool. But here's a few stuff... 

Logstash is meant to ingest logs. So make sure your data file is formatted one entry per line. Which means there should be a new line between each entry. Which means, if you want to do that for a series of entries like json data, do the following: 
```
{.....entry 1.....}
{.....entry 2.....}
````

Shouldn't be too hard to write a few bash/python lines to format your data. Then turn on Elastic Search and Kibana (optional). Do make sure the ELK stack is in the same versioning because it's not really backward compatible :( 

Now you need to write a config file for ingesting the data. And our `conf/json_to_es.conf` goes like this: 
```
input {
	stdin {
	}
}

filter {
	json {
		source => "message"
		remove_field => [ "message", "host" ]
	}
}

output {
    elasticsearch {
		index => "twitter"
    }
}
```

Running the ingestion: `logstash -f ../conf/json_to_es.conf < data_file.json`. Oh do make sure you're in the logstash bin directory if you're using this command directly, otherwise do the necessary path changes. We're feeding the input from `stdin`, which we can read from the config file and the `<` operator. After the json is read, we do some filtering, basically putting the data into a source field called `message`, so that we don't mix up the data with the other meta-data. Then we remove some fields like original `message` field which is just a string of the json object that we fed in without formatting, and `host`, which describes the computer you ran the indexing on. Then we output the result to an index called `twitter`, which will autogenerate and create the fields that are the keys in the json object; saves us time from writing parsers. 

TADA

### 08/01/18 - Bash Double Quotes

Quotations in bash can get tricky. Double quotes dereference variables, while single quotes go literal. 

```
A='123'  
echo "$A"   # Outputs 123
echo '$A'   # Outputs A
```

Also, I came across [this link](https://zwischenzugs.com/2018/01/06/ten-things-i-wish-id-known-about-bash/) while searching for that 

### 08/01/18 - Shell Startup 

I think [this picture](https://zwischenzugs.files.wordpress.com/2018/01/shell-startup-actual.png) is very informative to tell you where a shell goes when it starts up. I.e. which profile directories are entered. 

### 01/01/18 - Today I Learnt
Heading to the future!

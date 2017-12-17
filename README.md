# Today-I-learnt

### 17/12/17 - Removing all files in .gitignore

Say you commit some code, and then wrote a gitignore file. That means some files in the previous commit should be removed from the git indexed and ignore. Just run this command and you'll be fine
`git rm -r --cached . `

### 17/12/17 - Assigning emojis as variables in Swift

I learnt that you can assign emojis to variables in Swift. Check out the below code block! However, not all emojis work as Swift identifiers. [Here's a list of what works](https://stackoverflow.com/questions/39188733/which-emoji-characters-work-as-swift-identifiers)

```
let 👩 = "me"
let 😗 = "kiss"
print(😗 + " " + 👩)		// Prints "kiss me"

let 😮 = "oh"
let 💩 = "shit"
print(😮 + " " + 💩)		// Prints "oh shit"

let 🐶🐮 = "dogcow"
let 🐶 = 3
let 🐮 = 🐶 + 2
print(🐮)			// Prints 5
```

### 12/12/17 - Sorting a Hashmap in Java

I never actually learnt how to sort a hashmap in Java.

```
Map<String, Integer> unsortedMap = new HashMap<String, Integer>();

// Add items to unsortedMap here

// Convert unsorted map to a linked list 
List<Map.Entry<String, Integer>> unsortedList = new LinkedList<Map.Entry<String, Integer>>(unsortedMap.entrySet());

// Sort the linked list in reverse order of values
Collections.sort(unsortedList, new Comparator<Map.Entry<String, Integer>>(){

	@Override
	public int compare(Entry<String, Integer>o1, Entry<String, Integer> o2){
		return (o1.getValue()).compareTo(o2.getValue());
	}
}.reversed());

// Put the sorted list back into a linked hashmap
Map<String, Integer> sortedMap = new LinkedHashMap<String, Integer>();
for (Map.Entry<String, Integer> entry: unsortedList) {
	sortedMap.put(entry.getKey(), entry.getValue());
}
```

### 09/12/17 - Regex

I've never actually had a good time with Regex.

\w matches alphanumeric characters, which means a-z, A-Z, and 0-9. It also matches the underscore, _, and the dash, -.

\d matches digits, which means 0-9.

\s matches whitespace characters, which include the tab, new line, carriage return, and space characters.

\S matches non-whitespace characters.

. matches any character except the new line character \n.

+ matches more than 1 character

[Here's More](https://www.dataquest.io/blog/regular-expressions-data-scientists/?imm_mid=0f9342&cmp=em-data-na-na-newsltr_20171206)

### 08/12/17 - Cleaning up Git branches

Recently I've had success in using feature branches for development, but `git checkout [tab autocomplete]` shows a lot of old branches that have already been deleted.

If they've been cleaned up on `remote` and local, you can remove them with `git remote prune origin`.

### 07/12/17 - Python string matching with list 

This uses python. Say you have a list of strings `a = ['hello', 'world']` and a string `str = 'hello world'`. The problem is you want to find whether any strings in `a` appear in `str`. 

If you just want a `True` or `False` boolean, do: `if any(x in str for x in a):`

If you want the first match (with False as a default), do: `match = next((x for x in a if x in str), False)`

If you want to get all matches (including duplicates), do: `matches = [x for x in a if x in str]`

If you want to get all non-duplicate matches (disregarding order), do: `matches = {x for x in a if x in str}`

If you want to get all non-duplicate matches in the right order:
```
matches = []
for x in a:
    if x in str and x not in matches:
        matches.append(x)
```

### 06/12/17 - Calling subdirectories as modules in python

Say you have a file `mainfile.py` and a subdirectory with a python module `subdir/subfile.py`. Add an `__init__.py` in the subdirectory to call functions in `subfile.py`. The directory structure looks as such: 
-mainfile.py
|_ subdir
  - subfile.py
  - __init__.py
  
To call a function in `subfile.py`, do: 
```
In mainfile.py

from subdir import subfile

subfile.function()
```

### 04/12/17 - Memory in Linux

[Help! Linux ate my RAM!](https://www.linuxatemyram.com/)

### 02/12/17 - Remove ip addresses tagged to an ethernet interface 

Remove all ip addresses tagged to `eth0`: `ip addr flush dev eth0`

### 11/11/17 - Python Graph Gallery

[Python Graph Gallery](https://python-graph-gallery.com/?imm_mid=0f7469&cmp=em-data-na-na-newsltr_20171018)

Brilliant collection of graphs that you can make with Seaborn, useful for front end visualisations. Gives you the input and output formats too. 

### 04/11/17 - Debugging the front end web 

Useful debugging tools: 
- `console.log`: Everyone knows this 
- `console.assert`: Yknow javascripts and asserts woohoo
- `console.count(label)`: Counts the number of times you print a particular label, which can also be a count variable i. Haha.
- `console.table()`: Useful in putting a json object in a table form for debug
- `console.group`: Useful in grouping elements 
- `$()`: Document selectors just like in jQuery

[Full article Here](https://blog.pragmatists.com/how-to-debug-front-end-console-3456e4ee5504)

### 01/11/17 - Unicode and friends

Problem: encoding and decoding produces different results on different machines (with different OSes). Analysis of the binary representations of certain fields show that there was a "pointer" error, i.e. the start index (for say, four bytes for an int) was incorrect.

Initial suspicions: must be a decoding problem, because encodings are variable-width, i.e. sometimes more than one byte. As I thought about it I became increasingly convinced, because at some point the program expects a binary input that is converted into characters. What could go wrong?

Let's say the binary input is all ASCII-compliant.

```
0XXXXXX 0XXXXXXX 0XXXXXXX
```

Then the converted characters will be nice, universally recognisable ASCII characters that can be decoded back into binary by my program.

However, it's ridiculous to expect the binary input to be ASCII-compliant. Who knows what the format is like? What if the input is:

```
1110XXXX 10XXXXXX 10XXXXXX
```

Ouch. That's actually **one** character in UTF-8, and three characters in ISO-8859-1 or other funky encodings.

So the sender must encode the binary input into a string using Encoding 1, let the communications transmit the string, and the receiver must decode the encoded string using the same Encoding 1. What could go wrong? *Everything*.

As long as the data is still encoded in Encoding 1, everything that touches the data needs use Encoding 1 for operations. That's hard to guarantee.

To make matters worse, there are binary sequences which are **invalid** sequences in UTF-8 and other encodings. How they are handled depends on the implementation of whatever's handling it...

Solution: I still haven't found the bug, I've only guessed. But it would be interesting to find out where exactly the encoding/decoding screwed up, and this may also explain why "the messages received in the current system have problems": maybe the decoding has been wrong all this while!

If you're lucky enough to be in this situation, please read [this](https://en.wikipedia.org/wiki/Binary-to-text_encoding) and just use Base-64.

Outcome: Will try soon.

Note to self: if the decoding is wrong, the corrupted input may still be recoverable by guessing the encoding. However this is assuming that Implementations X and Y didn't drop invalid code sequences along the way! #undefinedbehaviour

### 01/11/17 - `table-layout`

Problem: I have a (Bootstrap) table and the column widths change depend on the screen size. I want to lock the widths of certain columns.

Solution: [`table-layout: fixed`](https://css-tricks.com/fixing-tables-long-strings/)

Also: `white-space: nowrap`? `text-overflow: ellipsis`? Holy cow I may actually like being a frontend guy now.

Outcome: Will try soon.

Note to self: add other notes on modern CSS goodies (flexbox and grid layout, and to think the latter was only official in March 2017!)

### 27/10/17 - Disable Web Security for Chrome 

This allows you to read local files 

On a mac: `open -a Google\ Chrome --args --disable-web-security --user-data-dir`
On windows: `chrome.exe --disable-web-security`

### 24/10/17 - \[UNIX\] Saving a directory for later

```
pushd . # this will save the current directory into a stack, for easier restoration
popd # pops the top directory out of the same stack, restores you back into that directory
```

### 11/10/17 - Node.JS 'Error: SSL Error: SELF_SIGNED_CERT_IN_CHAIN'

Sometimes when you do an `npm install` you encounter a SELF_SIGNED_CERT error, especially if you're behind a firewall or proxy. Here's the trick: `npm config set strict-ssl false`

### 05/10/17 - Positive Integer Solutions

[I can't solve this. Read it](https://www.quora.com/How-do-you-find-the-positive-integer-solutions-to-frac-x-y%2Bz-%2B-frac-y-z%2Bx-%2B-frac-z-x%2By-4/answer/Alon-Amit)

### 01/10/17 - Pandas Dataframe from Json

You can create a dataframe from a JSON object with the fields you want. 

Say you have a JSON object in which the data is an array of JSON objects within `hits` like follows: 
```
{
    "hits": [
        {
            "created_at": "2017-09-29T13:30:21.000Z",
            "title": "SCION: A Secure Internet Architecture [pdf]",
            "url": "https://www.scion-architecture.net/pdf/SCION-book.pdf",
            "author": "cimnine",
            "points": 1,
            "story_text": null,
...
```

Easily create a pandas dataframe with the fields you want: 
```
df = DataFrame()
requested_keys = ["title", "author", "url"]
data = DataFrame(data["hits"])[requested_keys]
```

### 02/09/17 - The Mathematics of Machine Learning

Perhaps its time to get a little into the heart of machine learning [From the IBM Blog](http://datascience.ibm.com/blog/the-mathematics-of-machine-learning/)

### 13/08/17 - Recursively download files in a directory

`wget -r --no-parent http://donhopkins.com/home/TheSimsDesignDocuments/`

### 10/08/17 - Batch rename .png to .jpg files with Bash

`for i in *.png; do mv $i $Pi%%.png}.jpg; done`

### 05/08/17 - Visual Studio Code Switch between Terminal and Editor

The great thing about Visual Studio Code is how everything is keyboard defined and your fingers don't have to leave the vicinity of the keyboard. But when you want to switch within the Integrated Terminal and Editor, you have to add the following keybindings into your user preferences keybindings json. 

```
{ "key": "ctrl+`", "command": "workbench.action.terminal.focus"},
{ "key": "ctrl+`", "command": "workbench.action.focusActiveEditorGroup", "when": "terminalFocus"}
```

### 24/06/17 - Concatenate smaller text files 

To concatenate smaller text files into one huge big text file, use `ls *.txt | xargs -L 1 cat >> input.txt`

### 24/06/17 - Neural Networks Reading

Just some links for later reading
* [Stanford University Deep Learning Course](https://web.stanford.edu/class/cs20si/syllabus.html?utm_campaign=Artificial%2BIntelligence%2BWeekly&utm_medium=email&utm_source=Artificial_Intelligence_Weekly_62)
* [Convolutional Methods for Text](https://medium.com/@TalPerry/convolutional-methods-for-text-d5260fd5675f?ref=aiweekly)
* [Picasso: A free Visualiser for CNN](https://medium.com/merantix/picasso-a-free-open-source-visualizer-for-cnns-d8ed3a35cfc5)

### 23/06/17 - Sendmail with HTML formatting

TIL we can use the Linux `sendmail` command and attach a .txt file with HTML formatting. Just add the following on the top of the .txt file 
```
Mime-Version:1.0
Content-Type: text/html
```

So you can now use html formatting like `<em> <br> <b>`. You can even do inline styles like `<h3 style="color: #1c70db;">NOW $159</h3>`. Cool stuff. 

### 22/06/17 - Seq2Seq Tensorflow Meetupsg

So we went to Tensorflow MeetupSG held at Google Singapore. Pretty tired so i'll just post some links here 
* Sam Witteveen [Twitter](https://twitter.com/sam_witteveen?lang=en) spoke on Seq2Seq. Here's [his Github](https://github.com/samwit/TensorFlowTalks) on these talks for some of the notebooks used
* [Tensorflow Playground](http://playground.tensorflow.org/#activation=tanh&batchSize=10&dataset=circle&regDataset=reg-plane&learningRate=0.03&regularizationRate=0&noise=0&networkShape=4,2&seed=0.88062&showTestData=false&discretize=false&percTrainData=50&x=true&y=true&xTimesY=false&xSquared=false&ySquared=false&cosX=false&sinX=false&cosY=false&sinY=false&collectStats=false&problem=classification&initZero=false&hideText=false) could potentially be useful for creating visualisations and outputs that you don't really know about, but are mostly a bunch of magic neural networks
* Matin Andrews from Red Cat Labs spoke about Captioning. [Here](https://github.com/mdda/deep-learning-workshop) are a bunch of his deep-learning workshops notebooks. 
* This is Red Cat Labs [Convolution demo](http://redcatlabs.com/2017-03-20_TFandDL_IntroToCNNs/CNN-demo.html#/)
* Flikr30K Feat(Image) was used to featureize all the images using InceptionV3
* The latest deep learning thing is called [Attention is all you need networks](https://arxiv.org/abs/1706.03762). Yes seriously, I need attention. 
* Martin Andrews also spoke about [Named Entity Recognitions](http://redcatlabs.com/2015-08-27_GTC-SA-2015-Poster/) from GPUs

* Random side link: [A Brief History of CNNs in Image Segmentation](https://blog.athelas.com/a-brief-history-of-cnns-in-image-segmentation-from-r-cnn-to-mask-r-cnn-34ea83205de4)

### 03/06/17 - Bash file manipulation

If you want to cut out the first column of a `csv` file, or any file with a delimited for that matter `cat filename.csv | cut -d ',' -f1`. Putting `f1` cuts the first column, `f2` cuts out the second column and so forth. 

Say you want to split a huge file into smaller chunks of n lines each: `split -l n filename.txt`

### 03/06/17 - Find latest file and timing command line

To find in a folder the file created the latest: 

```
ls -Artls | tail -n 1
```

### 15/04/17 - Pretty Print JSON on command line 

TIL how to pretty print JSON on command line using the `jq` library. 
```
cat file.json | jq '' > prettyfile.json
```

Alternatively, you can also use [JSON Lint](http://jsonlint.com)

### 15/04/17 - Setting up Nginx, uwsgi, Django

I have played with the Django basic tutorials for quite some time, but the thing I really dislike about basic "Getting Started" tutorials is the lack of transition guide from development to production environment. So I decided to figure out how exactly we can serve a Django app on a full-fledged web server like Nginx.

The stack that we want to set up is:
```
Web Client <-> Nginx <-> uwsgi <-> Django
```
I did this on RHEL7 VM in virtualbox. I followed the tutorial from [official uwsgi page](http://uwsgi-docs.readthedocs.io/en/latest/tutorials/Django_and_nginx.html), but there are so many things missing in the tutorial that I had to fill in the blanks from googling and stack overflow. This post is about those blanks I had to fill in.

#### 1. Set up Client <-> uwsgi <-> Python stack 

I assume that everyone reading this knows how to setup virtualenv and uwsgi using pip, and install nginx with yum, apt-get, or build from source. Installing pip using uwsgi requires python-dev tools to be installed in the base OS though, something which the tutorials left out.

After the installation is complete, write a simple test.py which gives a http response of "Hello World" and type the following cmd:
```
uwsgi --http :8000 --wsgi-file test.py
```
This initiates the uwsgi to python stack using the http protocol and uwsgi listens on port 8000. When working in RHEL/CentOS, we also need to open the port to firewall on public zone:
```
firewall-cmd --zone=public --add-port=8000/tcp --permanent
```
Now you should be able to visit **http://&lt;your IP address>:8000/** to see **"Hello World!"**

#### 2. Set up Client <-> uwsgi <-> Django stack
This one is straightforward. Just initiate a django project and in the project folder, type:
```
uwsgi --http :8000 --module mysite.wsgi
```
Don't worry about mysite.wsgi file. It is created temporarily (some black magic that I don't understand yet) to serve Django project. You should see the Django welcome page on **http://&lt;your IP address>:8000/**

#### 3. Set up Client <-> nginx <-> static files
This is where things get tricky due to lots of fill in the blanks, so I will try to be more comprehensive. Install nginx with yum, apt-get, build from source, whatever, and start the server with cmd:
```
nginx
```
You should see the **http://&lt;your IP address>:80/** serving an nginx welcome message (you might need to open port 80 in firewall too).

In Django project folder, create the "mysite_nginx.conf" following the tutorial in the link above. Once done, create the symbolic link for this file to /etc/nginx/sites-enabled:
```
sudo ln -s ~/path/to/mysite/mysite_nginx.conf /etc/nginx/sites-enabled
```
What the tutorials failed to tell me was that I actually have to mkdir /etc/nginx/sites-enabled because it doesn't come with nginx installation. They also failed to tell me that I have to create a line in /etc/nginx/nginx.conf to include the folder:
```
http{
  ...
  include /etc/nginx/sites-enabled/*.conf
  ...
}
```
Make all modifications and restart nginx with:
```
nginx -s reload
```
At this stage, you would think you are able to serve the static file locations as configured in mysite_nginx.conf (as the tutorials made me believe)...until you realized **http://&lt;your IP address>:8000/static** gives a **403 Forbidden**.

After 1 day of googling, I realized that the tutorials failed to mention we must set read and execute permissions for the entire path, not just the folder we are serving. So recursively **chmod 755** for all directories in your path to static files and **chmod 644** for files that you are serving.

#### 3. Set up Client <-> nginx <-> uwsgi <-> Django
While nginx can serve static files without any python/django involvement, we do want our applications to handle more complex requests from clients (such as a request for data from DB). This is where nginx need to pass request upstream to django via a Web Server Gateway Interface (wsgi), the standard which any python application talks to web servers.
```
uwsgi --socket :8001 --wsgi-file test.py
```
The above command sets up a webport socket, but the tutorial recommends using unix socket instead. So change the upstream django in "mysite_nginx.conf" to:
```
server unix:///path/to/mysite/mysite.sock;
# server 127.0.0.1:8001; # Comment this out when using unix socket
```
Test the interface with:
```
uwsgi --socket mysite.sock --wsgi-file test.py --chmod-socket=666
```
The socket permission is important.

If the above works, you can run the complete stack with:
```
uwsgi --socket mysite.sock --module mysite.wsgi --chmod-socket=666
```
Of course I would recommend creating a **mysite_uwsgi.ini** instead of typing out the whole command above, but now you have a working stack:
```
Client <-> nginx <-> uwsgi <-> Django
```

### 05/03/17 - Syntax highlighting in Markdown

I read Danqi's submission again and realised it was syntax-highlighted. I don't know why I didn't find this out earlier.

[Documentation here](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet#code).

### 04/03/17 - JavaScript Closure

I agree, JavaScript sucks. Part of the reason is because there are too many black magic going on. Closure is one of those black magic that irks me because we can still reference local variables once the function goes out of scope!

```javascript
function derp(){
  var someVar = 0;
  return function(param){
    someVar += param;
    alert(someVar);
  };
}

var myderp = derp();
myderp(10); //Alert 10
myderp(20); //Alert 30, wtf? I can still change someVar?
```

Coming from a background of C, this turns my whole world upside down. However, this black magic is quite useful when making charts reusable in d3.js (So I don't have to write that whole bunch of junk everytime). With closures and getter-setters, d3 charts can be quite reusable and reconfigurable.

```javascript
function chart() {
  var width = 720, // default width
      height = 80; // default height

  function my() {
    // generate chart here, using `width` and `height`
  }

  // This is brilliant. Its a getter if no argument is passed.
  // It is a setter if argument is passed. 
  // But'argument' is another one of those black magic though
  my.width = function(value) {
    if (!arguments.length) return width;
    width = value;
    return my;
  };

  my.height = function(value) {
    if (!arguments.length) return height;
    height = value;
    return my;
  };

  return my;
}

//Instantiate and render chart of width 720, height 80
var myChart = chart()
//Set height 100
myChart.height(100)
//Get height will return 100 instead of 80
myChart.height()
```
[More on reusable charts](https://bost.ocks.org/mike/chart/)

### 01/03/17 - Modern JavaScript

I have much to learn about modern JavaScript in 2017, as many things have changed (for the better, but JavaScript still sucks) since I stopped programming in it for a while.

#### `const` keyword

Does *not* mean that the variable is immutable, just that the *reference* is immutable.

#### `let` keyword

My favourite modern addition. This ensures *block* scope, which is far more saner than the function/global scope that JavaScript has been operating with. A good example will be:

```
for ( let i = 0; i < n; i++ ) {
  ...
}

for ( var j = 0; j < m; j++ ) {
  ...
}

console.log(j) // m-1
console.log(i) // error
```

This ensures that the variable `i` only exists during the `for` loop. Very useful for safe programming. The `let` keyword is reminiscent of the same keyword in ML and Haskell.

#### `for` loops

There's the `for...in` statement that **does not** (I cannot emphasise enough) do what you think it is, i.e. like in Python. This iterates over the **enumerable** properties of an object, **not necessarily in order**. Read this paragraph again.

```
for ( let key in obj ) {
  console.log(obj[key]);
}
```

Note that the iterating variable is the **key**. What happens if you use this for a plain old array?

```
var arr = [100, 200, 300];
for ( let v in arr ) { // using v here, because we all think it's a value, right?
  console.log(v);
}

// 0
// 1
// 2
// what?
```

What if we want Python semantics? Fortunately, there's the `for..of` statement, which iterates over an iterable object (e.g. an array. In JavaScript, an associative array is **not** an array).

```
for ( let value of arr ) {
  console.log(value);
}
```

Extra tip: you cannot have an associative array with numeric keys in JavaScript. This turns it automatically into a numeric array, which can have disastrous consequences:

```
var arr = [];
var id1 = 1;
var id200 = 200;
arr[id1] = "value1";
arr[id200] = "value200";
console.log(arr.length) // definitely not 2
```

The trick is to use `var arr = {}`, i.e. an *object*, rather than declaring it as an array.

### 23/02/17 - git add -p

`git add -p` allows you to stage *parts* of a modified file. This is very useful if you were suddenly overzealous, made many changes, only to realise that you broke the "commit early, commit often" rule.

### 20/02/17 - Adventures in /usr/bin

[Adventures in /usr/bin](http://ablagoev.github.io/linux/adventures/commands/2017/02/19/adventures-in-usr-bin.html) where we can all up our Linux skills 

### 18/02/17 - sudoedit

Once you've set `EDITOR` in your environment (it's `vim`, right? Right?), you can use `sudoedit` to edit files instead of `sudo vim ...`

Why? The first reason is that it keeps your editor customisations (`.vimrc`). The real reason is security: `sudo vim` allows the execution of other commands, which is obviously unsafe. `sudoedit` coupled with your favourite editor i.e. `vim` does not allow this to happen.

### 14/02/17 - Diner's Dilemma and German Tank Problem

* [Unscrupulous Diner's Dilemma](https://en.wikipedia.org/wiki/Unscrupulous_diner%27s_dilemma), also known as n-way Prisoner's Dilemma
* [German Tank Problem](https://en.wikipedia.org/wiki/German_tank_problem)

### 04/02/17 - Some Linux Command lines commands

- Print the last 5 lines of test.txt: `tail -5 test.txt`
- Print all the lines in test.txt containing the string "HELLO": `grep HELLO test.txt`
- Print the number of lines in test.txt containing the string "HELLO": `grep HELLO test.txt | wc -l`
- Print all files in the directory that contain the string "HELLO": `grep -rl * -e HELLO` 
- Print all file names that starts with test: `find . -name "test*"
- Print the contents of test.txt, sorted: `sort test.txt`
- Print number sequence 1 to 100 separated by space: echo {1..100}
- Print all the files in current directory recursively without leading directory path: `find . -type f -printf "%f\n"
- Replace spaces in file name with . in all the files in current directory: `find . -type f -printf "%f\n" | xargs -0 -T {} echo {} | tr ' ' '.'`
- Print the 15th line of the file test.txt: `sed '25q;d' test.txt`

### 29/01/17 - Bottle Python Framework

You can make web microservices with `bottle.py`. That simulates a server. 

```
@get("/")
def index():
	response.content_type = "application/json"
	return json.dumps([{"date":"08-01-2017", "id":1},{"date":"12-01-2017", "id":2},{"date":"19-01-2017","id":3}])

run(host="0.0.0.0", port=4242, reloader=False, debug=False, quiet=True)
```
Then head to `localhost:4242` and you will see the JSON string returned as output.

[More Bottle](https://bottlepy.org/docs/dev/)

### 18/01/17 - System Timings and Services 

THe system time bug bites. When you change the system timing, remember to update the services that use the timing, for an example, cron jobs and sys logs. That's done by: 
```
$sudo service cron restart
$sudo service rsyslog restart
```

Just to be sure that your system time and the cron jobs are running at the respective time, you can always do something like: 
`while [ True ]; do date; cat /var/log/syslog | tail -n 20; sleep 1; done`

### 14/01/17 - Some lessons

- If you are running a network of virtual machines on the same host, they are using a *virtual* switch. The packets never go out of the physical interface. The good thing is that you can throttle the bandwidth and latency to simulate a real network.
- Check everything. Time was wasted because something was not configured properly, but we *thought* it was configured properly and concluded that this configuration doesn't work. We were wrong. I hate XML files. And it was embarrassing.
- If your test conditions are wrong, your test is invalid by definition. A test may work at a small scale but fail at a large scale because you didn't cater for it.
- If you are doing a load test, check all resource usage. CPU, memory, and network. Measure while *slowly increasing* the load, not opening up the graphs when it's at full load and things start burning. This allows you to observe resource usage as a function of load.
- `gnome-system-monitor` is rubbish at measuring throughput. `iftop` and `iperf` are excellent. Trust numbers, not graphs.
- `tc` can add latency, but not throttle bandwidth.
- If there is a acknowledgement storm, the amount of traffic coming back must be large. It wasn't. So there wasn't an acknowledgement storm.
- Don't manually increase your TCP window sizes. Check if your operating system comes with TCP window scaling.
- Use a calculator for calculating network stuff. Conversion from bytes to bits and back is not mentally sustainable.
- Throughput = TCP window size / round trip time.
- Bursty traffic is *very different* from continuous, sustained traffic. The above equation will not work.
- Don't trust ping for RTT. Use Wireshark + `tcptrace`.
- Don't rely on the payload checksum in the TCP header. It's wrong. Google it.
- The best way to test if certain OS settings have been really changed is to restart the machine and check.
- JVisualVM is the undisputed king of monitoring a Java program.
- If something fails to start up, delete all remnants of a prevous invocation (the hard part is to know that they even exist), and start it up again. It's a bad sign if a system cannot handle the existence of remnants, or throws enigmatic error messages that lead users down the wrong path.
- If you have a network issue, especially if you are using readable addresses, the chances are very high that it's because of DNS.

### 13/01/17 - String concatenation in bash script

In bash script, you can concatenate strings with the `+=` operator. If they have new lines, you need to print new lines via echo with the `-e` flag. If you want to print new lines to a txt file, use `"${aString}"`.

```
aString=""
aString+="one\n"
aString+="two"
echo "${aString}" > aFile.txt
```

### 09/01/17 - HTML5 Speech Synthesis API

In HTML5, web apps can talk using the `SpeechSynthesisUtterance`. Like seriously. And there's a whole bunch of different languages and voices. It's really cool, like: 

```
var msg = new SpeechSynthesisUtterance('Hello World');
window.speechSynthesis.speak(msg);
```

Downside, you need to be able to host the web app, i.e. it cannot be a static HTML page. [Google tells you more about the API](https://developers.google.com/web/updates/2014/01/Web-apps-that-talk-Introduction-to-the-Speech-Synthesis-API)

### 02/01/17 - i++ vs ++i

[Is there a performance difference between i++ and ++i in C?](https://stackoverflow.com/questions/24886/is-there-a-performance-difference-between-i-and-i-in-c)

No. But (emphasis mine):

> So the question one should be asking is not which of these two operations is faster, it is **which of these two operations expresses more accurately what you are trying to accomplish**. I submit that if you are not using the value of the expression, there is never a reason to use i++ instead of ++i, because there is never a reason to copy the value of a variable, increment the variable, and then throw the copy away.

### 31/12/16 - Selecting the k-th largest item in O(n)

Let's suppose you need to find the k-th largest item in an array A[0...n-1]. This is trivial for k=n (smallest item) and k=1 (largest item) so we ignore those cases. How do you find the k-th largest item in O(n)?

Solution: Median-of-medians, which is based off quicksort.

The general idea is this - quicksort until the pivot is in the k-th position. If the pivot is not in the k-th position, quicksort the subarray that contains the k-th position.

Clearly, this does not work if pivot selection does not eliminate a proportion of the search array each iteration (This case degenerates to O(n<sup>2</sup>)). Otherwise, this approach is O(n). Here's how median-of-medians selects a suitable pivot to ensure deterministic O(n):

```
fn select_kth_largest(k, array):
   ...

fn select_pivot(subarray):
    if length of subarray <= 5,
        find true median using some sorting algo and return that.
    otherwise
        group_medians = []
        
        foreach group of 5 elements in subarray
            find true median in group.
            add true median to group_medians
        
        return select_kth_largest(# of groups / 2, group_medians) // I.e. find median-of-medians by calling itself.
```

Why does this work?

Observe that at least half of the groups have medians that are less than or equal to the median-of-medians. There are (subarray length / 5) groups, so the number of group medians is 20% of the subarray length. For each group, at least 3 items are less than or equal to their respective group median. Therefore 3 * 10% of the subarray will be less than or equal to the median-of-medians. This means the selected pivot will eliminate at least 30% of the search array each iteration.

### 30/12/16 - Building a reliable protocol

I found myself having to investigate why an implementation of a "reliable protocol" was losing packets like crazy. It turns out that building a reliable protocol on top of an unreliable one is extremely tricky, as I should have realised in my networking classes, and I encourage everyone to view network engineers with greater respect.

TCP is perhaps the most famous reliable protocol of all. But there are *many versions* of TCP, some very recent and some probably in development to adapt to the changing network needs of today. So it is *hard* to develop a reliable protocol, as I realised while investigating and trying to recall my networking stuff from 2.5 years ago.

Almost everything in computer science is about trade-offs. In this case, it's reliability versus performance. A fire-and-forget protocol is really fast and also really unreliable; requiring high reliability requires huge buffers and multiple round-trips for all those acknowledgements and retries, and you will still never get 100% reliability (this is something one must accept from the very beginning).

Here are my observations of what to try:

0. Don't send so many things in the first place
1. Acknowledgements
2. Sequence numbers for ordering (you do want your packets to be ordered, right?)
3. Buffers (i.e. sliding windows in TCP parlance) on transmitter and receiver to support the above
4. Congestion control (max number of unacknowledged packets in flight) to prevent choking the network
5. Flow control (changing the size of sliding windows on both ends in response to receiver's ability to receive) to make sure receiver doesn't get swamped
6. Buy better hardware

As more steps are taken to build The Reliable Protocol, there are extra processing costs and memory costs (but if you can, as I always say, "throw money at the problem"...) in exchange for higher reliability.

Implementing 1 to 4 was sufficient for low loads. A high load destroyed our hopes and wishes because it turns out that we were swamping the poor receiver (or even a poor switch in the middle!) without throttling our sending rate. It is quite counter-intuitive that reducing sending rate increases performance but that's networking engineering...

Implementing flow control showed *no lost packets*, but we were probably just lucky because it could handle our maximum expected load. With some imagination, it is easy to see that there is a point beyond which even flow control cannot help, and it's time to buy better hardware.

Of course, if you can afford not to send so many things in the first place, don't!

Addendum: raw Markdown shows that it's steps 0 to 6. Step 6 was meant to be step `n` but then it wouldn't be rendered properly as a list.

### 29/12/16 - SSH without password in GNOME terminal

If you have put your public key into the server, yet when you try to ssh from a GNOME terminal (i.e. CentOS6) and it doesn't work, add `SSH_AUTH_SOCK=0` before the ssh command. So type `SSH_AUTH_SOCK=0 ssh admin@<IPADDRESS>`. THat should get you in without password


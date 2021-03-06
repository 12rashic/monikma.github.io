---
layout: post
title: Node.js Code School course notes
date: '2015-01-02T19:10:00.000+01:00'
author: Monik
tags:
- Programming
- Node.js
modified_time: '2016-01-13T20:52:22.724+01:00'
blogger_id: tag:blogger.com,1999:blog-5940427300271272994.post-2952638391175076611
blogger_orig_url: http://learningmonik.blogspot.com/2015/01/nodejs-code-school.html
commentIssueId: 24
type: course
---

<div class="bg-info panel-body" markdown="1">
These are the notes I took during the Node.js basic course from <a
        href="http://www.codeschool.com/">www.codeschool.com</a>. It was a very good course. It guides through the basics of how to start with NodeJS.
</div>

<h3>Table of contents</h3>
- TOC
{:toc max_level=2}

### Lesson 1

<ul>
    <li>Node.js is not a framework. There are Web frameworks based on Node.js but Node.js itself is not. It is on much lower level than frameworks.</li>
    <li>Main thing with Node.js is that it's event based; the event loop; non blocking by design</li>
    <li>It's the V8 JavaScript Runtime from Chrome wrapped in extra stuff, and written in C</li>
    <li>There is no concurrency, it's like single threaded server</li>
    <li>JavaScript was chosen because it also has no concurrency and this fits more</li>
</ul>

#### Minimal server

<span
        style="font-family: Courier New, Courier, monospace;">var http = require('http');</span><br/><span
    style="font-family: Courier New, Courier, monospace;"><br/></span><span
    style="font-family: Courier New, Courier, monospace;">http.createServer(function(request, response) {</span><br/><span
    style="font-family: Courier New, Courier, monospace;">&nbsp; &nbsp;response.writeHead(200);</span><br/><span
    style="font-family: Courier New, Courier, monospace;">&nbsp; &nbsp;response.write("Hello, this is Bla");</span><br/><span
    style="font-family: Courier New, Courier, monospace;">&nbsp; &nbsp;response.end()</span><br/><span
    style="font-family: Courier New, Courier, monospace;">}).listen(8080);</span>
<ul>
    <li>it's registering the request event</li>
    <li>it tells what should happen on that event</li>
</ul>

#### Serving index.html

<div><span
        style="font-family: Courier New, Courier, monospace;">http.createServer(function(request, response) {</span>
</div>
<div>
    <div><span style="font-family: Courier New, Courier, monospace;">&nbsp; response.writeHead(200);</span></div>
    <div><span
            style="font-family: Courier New, Courier, monospace;">&nbsp; fs.readFile('index.html', function(error, contents){</span>
    </div>
    <div><span style="font-family: Courier New, Courier, monospace;">&nbsp; &nbsp; response.write(contents);</span>
    </div>
    <div><span style="font-family: Courier New, Courier, monospace;">&nbsp; &nbsp; response.end();</span></div>
    <div><span style="font-family: Courier New, Courier, monospace;">&nbsp; });</span></div>
    <div><span style="font-family: Courier New, Courier, monospace;">}).listen(8080);</span></div>
</div>

### Lesson 2: Events

<div>
    <ul>
        <li>in Node.js objects can emit events</li>
        <li>every such object is an instance of EventEmitter</li>
        <li>the http.createServer is in fact taking the callback for 'request' event, we could as well write server.on('request', callback)</li>
        <li>multiple handlers can be registered for same event</li>
    </ul>
</div>

#### Custom EventEmitter

<div>
    <div><span style="font-family: Courier New, Courier, monospace;">var events = require('events');</span></div>
    <div><span style="font-family: Courier New, Courier, monospace;">var EventEmitter = events.EventEmitter;</span>
    </div>
    <div><span style="font-family: Courier New, Courier, monospace;"><br/></span></div>
    <div><span style="font-family: Courier New, Courier, monospace;">var chat = new EventEmitter();</span></div>
    <div><span style="font-family: Courier New, Courier, monospace;">chat.on('message', function(message){</span>
    </div>
    <div><span style="font-family: Courier New, Courier, monospace;">&nbsp; console.log(message);</span></div>
    <div><span style="font-family: Courier New, Courier, monospace;">});</span></div>
</div>
<div><span style="font-family: Courier New, Courier, monospace;">chat.emit('message', 'blabla');</span></div>

### Lesson 3: Streams

<ul>
    <li>they are good for transmitting huge amount of data, so that we can do it chunk by chunk</li>
    <li>we cover the streams2 api</li>
    <li>streams are e.g. the request (readable stream) and response (writable stream) input parameters in the createServer callback function; they are both open inside the function</li>
    <li>readable stream is also an EventEmitter!</li>
</ul>

#### Echoing the request to the user

<span
        style="font-family: Courier New, Courier, monospace;">response.writeHead(200);<br/>request.pipe(response); // echo the request to the user</span><br/>

#### File upload

<span
        style="font-family: Courier New, Courier, monospace;">var file = fs.createWriteStream('file1.txt');</span><br/><span
    style="font-family: Courier New, Courier, monospace;">request.pipe(file);</span><br/><span
    style="font-family: Courier New, Courier, monospace;">request.on('end', function(){</span><br/><span
    style="font-family: Courier New, Courier, monospace;">&nbsp; response.end('uploaded!')</span><br/><span
    style="font-family: Courier New, Courier, monospace;">});</span>

#### File upload with progress


<div><span
        style="font-family: Courier New, Courier, monospace;">var file = fs.createWriteStream('file1.txt');</span><br/><span
        style="font-family: Courier New, Courier, monospace;"><br/></span><span
        style="font-family: Courier New, Courier, monospace;">var fileBytes = request.headers[content-length];</span><br/><span
        style="font-family: Courier New, Courier, monospace;">var uploadedBytes = 0;</span><br/><span
        style="font-family: Courier New, Courier, monospace;"><br/></span><span
        style="font-family: Courier New, Courier, monospace;">request.on('readable', function(){</span><br/><span
        style="font-family: Courier New, Courier, monospace;">&nbsp; var chunk = null;</span><br/><span
        style="font-family: Courier New, Courier, monospace;">&nbsp; while(null !== (chunk = file.read())){</span><br/><span
        style="font-family: Courier New, Courier, monospace;">&nbsp; &nbsp; uploadedBytes += chunk.length;</span><br/><span
        style="font-family: Courier New, Courier, monospace;">&nbsp; &nbsp; var progress = (uploadedBytes/fileBytes)*100;</span><br/><span
        style="font-family: Courier New, Courier, monospace;">&nbsp; &nbsp; response.write("Progress:"+ parseInt(progress,10)+"%\n");</span><br/><span
        style="font-family: Courier New, Courier, monospace;">&nbsp; }</span><br/><span
        style="font-family: Courier New, Courier, monospace;"></span><br/><span
        style="font-family: Courier New, Courier, monospace;">});</span><br/><span
        style="font-family: Courier New, Courier, monospace;"><br/></span><span
        style="font-family: Courier New, Courier, monospace;">request.pipe(file);</span><br/><span
        style="font-family: Courier New, Courier, monospace;">request.on('end', function(){</span><br/><span
        style="font-family: Courier New, Courier, monospace;">&nbsp; response.end('uploaded!')</span><br/><span
        style="font-family: Courier New, Courier, monospace;">});</span></div>

#### Other useful things

<div>
    <ul>
        <li><span
                style="font-family: Courier New, Courier, monospace;">file.pipe(process.stdout); </span>- like console.log
        </li>
        <li>reading in chunks:<br/><span style="font-family: Courier New, Courier, monospace;">file.on('readable', function(){<br/>&nbsp; var chunk;<br/>&nbsp; while(null !== (chunk = file.read())){<br/>&nbsp; &nbsp; console.log(chunk.toString());<br/>&nbsp; }<br/>});</span>
        </li>
        <li>don't close writer stream once pipe is done:<br/><span
                style="font-family: Courier New, Courier, monospace;">file.pipe(destFile, {end:false});</span></li>
    </ul>
    </div>

### Lesson 4: Modules

<div><span style="font-family: Courier New, Courier, monospace;"><u>my_module.js:</u></span><br/><span
        style="font-family: Courier New, Courier, monospace;">var foo = function(){...};</span><br/><span
        style="font-family: Courier New, Courier, monospace;">var bar = function(){...};</span><br/><span
        style="font-family: Courier New, Courier, monospace;">var baz = function(){...};</span><br/><span
        style="font-family: Courier New, Courier, monospace;">exports.foo = foo;</span><br/><span
        style="font-family: Courier New, Courier, monospace;">exports.bar = bar;</span><br/><span
        style="font-family: Courier New, Courier, monospace;"><br/></span><span
        style="font-family: Courier New, Courier, monospace;"><u>app.js:</u></span><br/><span
        style="font-family: Courier New, Courier, monospace;">var m = require("./my_module");</span><br/><span
        style="font-family: Courier New, Courier, monospace;">m.foo();</span><br/><span
        style="font-family: Courier New, Courier, monospace;">m.bar();</span><br/><br/>
    <ul>
        <li><span
        >you can also do module.exports = foo, and then call directly foo(), but in this way you can export only one function</span>
        </li>
        <li><span
        >if you don't provide the "./" prefix for the module name, it will look in node_modules directory of the app, then the same directory in your home directory, if not then in /home, and if not then at the root path.</span>
        </li>
        <li><span
        >npm - Package Manager for Node - module repository, dependency management, easily publish your own modules, see&nbsp;</span><span
        >npmjs.org</span></li>
        <li><span
        >package.json - good practice to have this file in root of your app, with app info and required dependency versions; then, when you run "npm install", these dependencies will be installed if not there yet;</span>
        </li>
        <li><span
        >package.json is also present in module root folder, as one module may depend on another one</span>
        </li>
        <li><span
        >semantic versioning: x.x.x, you can also specify ~1.8 to get the latest 1.8.x version; see www.semver.org</span>
        </li>
    </ul></div>

### Lesson 5: Express

<ul>
    <li>is a web framework for node</li>
</ul>

#### Basic example

<div><span style="font-family: Courier New, Courier, monospace;">require('express');</span></div>
<div><span style="font-family: Courier New, Courier, monospace;"><br/></span></div>
<div><span style="font-family: Courier New, Courier, monospace;">var app = express();</span></div>
<div><span
        style="font-family: Courier New, Courier, monospace;">app.get('/', function(request, response){</span>
</div>
<div><span
        style="font-family: Courier New, Courier, monospace;">&nbsp; response.sendFile(__dirname + "/index.html");</span>
</div>
<div><span style="font-family: Courier New, Courier, monospace;">});</span></div>
<div><span style="font-family: Courier New, Courier, monospace;">app.listen(8080);</span></div>

#### Request with dynamic URI param

<div><span
        style="font-family: Courier New, Courier, monospace;">app.get('/tweets/:username', function(request, response){</span>
</div>
<div><span
        style="font-family: Courier New, Courier, monospace;">&nbsp; var username = request.params.username;</span>
</div>
<div><span style="font-family: Courier New, Courier, monospace;">&nbsp; options = {</span></div>
<div><span style="font-family: Courier New, Courier, monospace;">&nbsp; &nbsp; protocol: "http:",</span>
</div>
<div><span
        style="font-family: Courier New, Courier, monospace;">&nbsp; &nbsp; host: "api.twitter.com",</span>
</div>
<div><span
        style="font-family: Courier New, Courier, monospace;">&nbsp; &nbsp; pathname: "/1/statuses/user_timeline.json",</span>
</div>
<div><span
        style="font-family: Courier New, Courier, monospace;">&nbsp; &nbsp; query: {screen_name: username, count: 10}</span>
</div>
<div><span style="font-family: Courier New, Courier, monospace;">&nbsp; };</span></div>
<div><span
        style="font-family: Courier New, Courier, monospace;">&nbsp; var twitterUrl = url.format(options);</span>
</div>
<div><span
        style="font-family: Courier New, Courier, monospace;">&nbsp; request(twitterUrl).pipe(response);</span>
</div>
<div>
    <div><span style="font-family: Courier New, Courier, monospace;">});</span></div>
    <div><span style="font-family: Courier New, Courier, monospace;">app.listen(8080);</span></div>
</div>

#### Templates - Embedded Java Script

<div>
    <ul>
        <li>npm install --save ejs</li>
        <li>the templates are in "views" directory of your app by default</li>
        <li>to put the data in the template, use a callback function in request function:<br/><span
                style="font-family: 'Courier New', Courier, monospace;"><strike>request(twitterUrl).pipe(response);</strike><br/>request(twitterUrl, function(err,res,body){<br/>&nbsp; var tweets = JSON.parse(body);<br/>&nbsp; //that's the data that will end up in the template:<br/>&nbsp; response.locals = {tweets:tweets, name:username};<br/>&nbsp; response.render('tweets.ejs');<br/>});<br/><br/><u>tweets.ejs:</u><br/>&lt;h1&gt;Tweets for &lt;%= name %&gt;&lt;/h1&gt;<br/>&lt;ul&gt;<br/>&nbsp; &lt;% tweets.forEach(function(tweet){<br/>&nbsp; &nbsp; &nbsp;&lt;li&gt;&lt;%= tweet.text %&gt;&lt;/li&gt;<br/>&nbsp; &lt;%});%&gt;<br/>&lt;/ul&gt;</span>
        </li>
    </ul>
</div>

### Lesson 6: Sockets

<ul>
    <li>npm install --save socket.io</li>
    <li><span
            style="font-family: Courier New, Courier, monospace;">var io = require("socket.io")(server) </span>- the server which was created with "createServer(app)", where app is express server.. in this way socket.io and express share same http server
    </li>
    <li>we write a chatroom app</li>
</ul>

#### Server side


<div><span style="font-family: Courier New, Courier, monospace;"><u>app.js</u></span></div>
<div><span
        style="font-family: Courier New, Courier, monospace;">var express = require('express');</span>
</div>
<div>
    <div><span style="font-family: Courier New, Courier, monospace;">var app = express();</span></div>
    <div><span style="font-family: Courier New, Courier, monospace;">var http = require('http');</span>
    </div>
    <div><span
            style="font-family: Courier New, Courier, monospace;">var server = http.createServer(app);</span>
    </div>
    <div><span
            style="font-family: Courier New, Courier, monospace;">var io = require('socket.io')(server);</span>
    </div>
    <div><span
            style="font-family: Courier New, Courier, monospace;">io.on('connection', function(client){</span>
    </div>
    <div><span
            style="font-family: Courier New, Courier, monospace;">&nbsp; console.log("New client connected..");</span>
    </div>
    <div><span
            style="font-family: Courier New, Courier, monospace;">&nbsp; //when client sends a message, emit it to all other clients</span>
    </div>
    <div><span
            style="font-family: Courier New, Courier, monospace;">&nbsp; client.on('messages', function(data){</span>
    </div>
    <div><span
            style="font-family: Courier New, Courier, monospace;">&nbsp; &nbsp;&nbsp;client.broadcast.emit('question',data);</span>
    </div>
    <div><span style="font-family: Courier New, Courier, monospace;">&nbsp; });</span></div>
    <div>
        <div><span
                style="font-family: Courier New, Courier, monospace;">&nbsp; //or just emit one dummy message to the client</span>
        </div>
        <div><span
                style="font-family: Courier New, Courier, monospace;">&nbsp; client.emit('messages',{foo:"bar"});</span>
        </div>
    </div>
    <div><span style="font-family: Courier New, Courier, monospace;">});</span></div>
    <div><span style="font-family: Courier New, Courier, monospace;">server.listen(8080);</span></div>
</div>

#### Client side

<div><u><span style="font-family: Courier New, Courier, monospace;">index.html:</span></u></div>
<div><span
        style="font-family: Courier New, Courier, monospace;">&lt;script src="/socket.io/socket.io.js"&gt;&lt;/script&gt;</span>
</div>
<div><span style="font-family: Courier New, Courier, monospace;">&lt;script&gt;</span></div>
<div><span
        style="font-family: Courier New, Courier, monospace;">&nbsp; var socket = io.connect('http://localhost:8080');</span>
</div>
<div><span
        style="font-family: Courier New, Courier, monospace;">&nbsp; //the client can receive messages like this</span>
</div>
<br/>
<div><span
        style="font-family: Courier New, Courier, monospace;">&nbsp; server.on('messages', function(data){</span>
</div>
<div><span style="font-family: Courier New, Courier, monospace;"></span><br/>
    <div><span
            style="font-family: Courier New, Courier, monospace;">&nbsp; &nbsp; console.log(data.foo);</span>
    </div>
    <span style="font-family: Courier New, Courier, monospace;"></span><br/>
    <div><span style="font-family: Courier New, Courier, monospace;">&nbsp; });</span></div>
    <span style="font-family: Courier New, Courier, monospace;"></span></div>
<br/>
<div><span style="font-family: Courier New, Courier, monospace;">&lt;/script&gt;</span></div>

### Lesson 7: Persisting Data

<div>
    <ul>
        <li>still the chatroom example: if someone joins later, they don't see the previous messages; so we store them in server in memory - but if server restarts, then it's bad</li>
        <li>so we can use a DB - Node.js has drivers for most DBses which are NON Blocking!</li>
        <li>example: Redis (key value store), very good docu: npm install --save redis:<br/>I'm skipping that part, it's redis specific
        </li>
    </ul>
</div>
<div><span><br/></span></div>

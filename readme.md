MassTransit JS
==============
Masstransit javascript messaging using STOMP over Websockets.

Introduction
------------
MassTransit JS is a javascript implementation of the Masstransit messaging framework. It provides a way to communicate with other MassTransit instances in your network via Stomp message brokering. 

At this moment you can both receive message from a MassTransit instance on the net, send subsciption information and publish messages, but again, at a very early stage.

Configuration
-------------
The code is designed to run both inside a web browser and via node-js. Each of them do requires a (little) different configration approche.

*node-js*
In node-js you have the advantage of being able to require just the package file and specify which transport you want to use via the scheme in the configuration object.
	
	var ServiceBus = require("masstransit-js").Servicebus;

	var bus = new ServiceBus({
		receiveFrom : "stomp://localhost:8181/client",
		subscriptionService: "stomp://localhost:8181/subscription",
		transport :'stomp'
	});

*browser*
Everything is packed into one big JS file so running from a browser should equal the node-js version pretty close.

	<html>
    	<script type="text/javascript" src="./masstransit.js"></script>
	
		<script>
			var bus = new ServiceBus({
				receiveFrom : "stomp://localhost:8181/client",
				subscriptionService: "stomp://localhost:8181/subscription",
				transport :'stomp'
			});
		</script>
	</html>
	
Logging
-------
To enable logging you need to specify the output destination. 

	
Component stack
---------------
The project currently relies on a Stomp broker that communicates via websockets, both serverside as well as in your browser. This means it will run in browsers like Chrome, Safari and recent Firefox builds.

Communication between your C# application and a browser application in done via component stack:

	Your server application				|
		> MassTransit .Net				| all in C#
			> MT Stomp transport 		|
				> Stomp client 			|
										
					> websockets		| 
						> stomp broker	| also in C#, but could be anything... 
					< websockets 		|
					
				< Stomp client 			|	  
			< MT Stomp transport 		| all Javascript
		< Masstranist JS 				|
	Your browser application			|

Internals
---------
The Javascript version loosly follows the .net implementation in it's naming and (very) general setup.There is a main object called ServiceBus that allows you to publish messages and subscribe consumers.

Next to that there is the SubsriptionService which is used to register a new client and tell the world there is a new consumer available on the network. The StompTransport is used to talk to the Stomp client and notifies the bus when there is a new message available. This is it differs somewhat from the .net version, we assume an active client and rely on a 'push' model to kick-off the message delivery.

And last but cerainly not least, there is the Serializer, a thin layer to convert JSON objects into strings and back again.
	
Example
-------
Check out the included example apps to see it in action. Run the *ServerApp* in Visual Studio; this will start the Stomp broker, the MT subscription service and a local servicebus. Next point your browser to the *index.html* in the *BrowserApp* directory. If you want to see the details open a javascript debugger console and watch it go. Press [enter] in the *ServerApp* console window and it will send a PingMessage to the client, you should see the pings coming in.

Libraries
---------
The following libraries are used to create the full stack

* Stomp Javascript client 	http://jmesnil.net/ 
* Math.uuid.js 				http://www.broofa.com
* EventEmitter				http://github.com/pete-otaqui
* uri.js					http://code.google.com/p/js-uri/
* Nimble functions			http://caolan.github.com/nimble/
* Ultralight Message Broker	http://github.com/enix/ultralight


Things to do
------------
This project is in a very early 'hurray, it works!' state, so that means a lot can and probably needs change.

For example:

* Only the 'happy happy joy joy everything is alright' flow is implemented at this moment. That means poor error handling, no reconnect support, no healtmonitoring and everything you would like to see in mature software.
	
* You can not unsubscribe a message consumer.

* .... and most likely a ton of other things
	
License
-------
Copyright 2011 Ernst Naezer, et. al.
 
Licensed under the Apache License, Version 2.0 (the "License"); you may not use 
this file except in compliance with the License. You may obtain a copy of the 
License at 

    http://www.apache.org/licenses/LICENSE-2.0 

Unless required by applicable law or agreed to in writing, software distributed 
under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR 
CONDITIONS OF ANY KIND, either express or implied. See the License for the 
specific language governing permissions and limitations under the License.

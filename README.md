# Bastion
Realtime notification using Socket I/O
#

## Running Bastion Api Service

Before you run ``` node app``` don't forget of ``` npm install ``` for getting all of bastion dependences.

### Nedded Environment Variables:

* **NODE_ENV**: 'DEV' or 'PRODUCTION'.
* **PORT**: HTTP Response port(**default: 10007**).
* **IOPORT**: Socket-IO response port(**default: 10008**).
* **CLUSTER**: Amount of clusters.

#

## Code Examples

## When in web application/cordova apps
### Connection
For connection to bastion API, you first need to refer bastion socket-io.js file, like this:

 **Code Example:**
``` javascript
    <script src="http://bastionHost:10008/socket.io/socket.io.js"></script>
```
*Note: It will always be this route, unless you change bastion source.*
<br/><br/>

Then in a javascript file, establish a connection to bastion socket.io port, like this:

 **Code Example:**
``` javascript
    //localhost:socket-io-port/bastion-namespace
    window.socket = io.connect('http://bastionHost:10008/bastion/notification');
```

After you connected at bastion, you'll recive an event called 'connect', it means you have connected successfully. [See more socket io events in this topic](http://stackoverflow.com/questions/24224287/list-of-socket-io-events) <br />
In your html file inside the `<script>` tag or in a `.js` file, you can implement an event listener as it:

 **Code Example:**
``` javascript

    socket.on('connect', function() {
        //your code
    });

```

### Joinning

For joinning a channel emit 'bastion-join' event. See this example above:

 **Code Example:**
``` javascript
    //Event upon a successfull Connection
    socket.on('connect', function() {
        // Connected, let's sign-up for receive messages for this room
        socket.emit('bastion-join', {
                customId: id
        },'channel-name');
    });
```

After this you are in a channel, let's message!

#
### Messaging

The only thing you have to care about, is to tell bastion what and who will receive your information. <br />

Bastion message is an object, and have two attributes:<br/>

**meta**: This attribute tell bastion some procedures about the receiving channel, like in the example.<br/>
**notification**: Its what you want to send, bastion do not care about what you will send to him, he only transports that to other sockets.
This attribute can be an array, object, string, int, float... Whatever you want, he realy doesn't care.

 **Code Example:**
``` javascript
    {
        meta:{
            to: 'socket-io-custom-id'// it says bastion who in the receiving channel will receive the notification attribute value
        },
        notification: {}// let this attribute value to your imagination
    }
```

When we need to send a information to other sockets, bastion provide us two ways to do it: <br />

**broadcasting & emiting**<br/>

**bastion-broadcast-notification**: It says bastion to send the message to all sockets in a channel but not to sender. <br/>
**bastion-emit-notification**: It says bastion to send the message to all sockets in a channel, even to sender.

 **Code Example:**
``` javascript
    //Broadcast
    socket.emit('bastion-broadcast-notification',message, 'receiving channel');

    //Emit
    socket.emit('bastion-emit-notification',message, 'receiving channel');

```
#
### Recieving Messages from Bastion
Since you emit ``` bastion-broadcast-notification ``` or ``` bastion-emit-notification ```, bastion will reply you with broadcasting or emitting ``` bastion-notification ``` event. <br/>
You can handle bastion responses by impementing this code:
 ``` javascript

    socket.on('bastion-notification', function(notification) {
        //Your Code here
    });

 ```
 Bastion will put out your data passed in the ``` notification``` param, he won't metter if you pass your data through socket-io event or in the body of a http post.
 Both will fire ``` bastion-notification ``` event.
#
## With HTTP-POST Request
Bastion also provide us a http post method, that send your message to any of his channels.<br/>
See this example above:<br/>

**URL**: http://localhost:10007/bastion/api/notification<br/>
**METHOD**: POST<br/>

**Notes**: <br/>
"http://localhost:10007/" this isn't bastion socket io port, this is bastion API port(a node server, like in a normal api). <br/>
"bastion/api/notification" this is the route for your messages. <br/>


The object that Bastion expected us to send to him at the request body is(**THE SAME STRUCTURE THAT "MESSAEGING" SECTION**):

 **Code Example:**
``` javascript
    {
        "meta": {
            "channels": ["index","channel1"] //Array of channels that will receive your message.
        },
        "notification": {} // let this attribute value to your imagination too.
    }
```

### Bastion Responses

### Success: <br/>
**Status Code** : 200 - OK

**Request Body**:

**Code Example:**
``` javascript
    {}
```

### Errors:
Bastion have a default response when somthing gets wrong, as this example:

**Request Body**:

 **Code Example:**
``` javascript

    {
        "developerMessage": "A operação solicitada não foi encontrada",
        "userMessageTranslated": "Não foi possivel concluir a operação solicitada",
        "errorCode": 404,
        "moreInfo": {
            "developerMessage": "Recurso não encontrado",
            "message": "A operação solicitada não foi encontrada",
            "status": 404
        }
    }
```
#
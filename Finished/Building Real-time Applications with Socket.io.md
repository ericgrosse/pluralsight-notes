# Welcome to the Course

## Introduction

Socket.io is a JavaScript framework

# Socket.io Basics

WebSockets is a browser based technology that was standardized for all major browsers in 2011. The WebSocket technology allows for direct, two-way connections between client and server. This makes it easy to build real-time apps because the server can dynamically push new data to connected clients whenever there is new data or the state has changed.

Socket.io was created to be a wrapper around WebSockets with a simplified API (similar to jQuery being a simplified DOM wrapper around JavaScript). Its other main purpose was to deal with connection issues caused by old browsers, sites with websockets turned off, timeouts, etc.

## Socket.io Features

Socket.io allows for event-based, bi-directional communication.

Socket.io is optimized to work with low latency operations. Examples include chat applications like Slack, real-time analytics like Google Analytics, and sending notifications you would see in places like Facebook and Twitter.

## Socket.io Uses

Real-time forms such as google docs for collaborative work

# Installing Socket.io

## First Connection

On the server-side (`app.js`), we have:

```
io.on('connection', function(socket) {
  console.log('new connection made');
});
```

On the client-side (scripts  in `index.html`), we have:

```
<script src="/socket.io/socket.io.js"></script>
<script>
  var socket = io('http://localhost:8080');
</script>
```

After running `nodemon app.js`, navigate to `localhost:8080` in the browser and you'll see `new connection made` printed to the terminal. Open another tab at `localhost:8080`, and `new connection made` will once again be printed to the terminal.

## Send and Receive Messages

`app.js`

```
io.on('connection', function(socket) {
  console.log('new connection made');

  socket.emit('message-from-server', {
    greeting: 'Hello from Server'
  });

  socket.on('message-from-client', function(msg) {
    console.log(msg);
  });
});
```

`index.html`

```
<script>
  var socket = io('http://localhost:8080');

  socket.on('message-from-server', function(evt) {
    document.body.appendChild(
    document.createTextNode(evt.greeting));

    socket.emit('message-from-client', {
      greeting: 'Hello from Client'
    });
  });
</script>
```

In the browser, you should see `Hello from Server` and in the terminal you should see `{ greeting: 'Hello from Client' }`.

Both the client and server can listen to and emit custom events. When the server emits an event, the client can listen to that same event and vice versa.

## The Pub/Sub Pattern

Short for the publish/subscribe pattern.

> Pub/Sub is a one-way messaging, events-driven manner of writing code

The publisher doesn't need to know who the recipients are in advance. This creates a loosely coupled relationship between the publisher and subscribers. This is an ideal pattern for distributed systems.

## Bitcoin App Build

```
<script src="http://code.jquery.com/jquery-2.1.4.min.js"></script>
<script src="/socket.io/socket.io.js"></script>
<script>
  var socket = io('https://websocket.btcchina.com/');
  socket.emit('subscribe', 'marketdata_cnybtc');
  socket.on('connect', function() {
    socket.on('ticker', function(data) {
      $('#market').text(data.ticker.market);
      $('#high-count').text(data.ticker.high);
      $('#low-count').text(data.ticker.low);
      $('#buy').text(data.ticker.buy);
      $('#sell').text(data.ticker.sell);
      $('#volume').text(data.ticker.vol);
    });
  });
</script>
```

`var socket = io('https://websocket.btcchina.com/')` connects to an external websocket that we draw data from.

When listening to the `ticker` event, properties of the returned data are placed into appropriate places in the DOM via jQuery selectors.

# Chat Application

## Send Messages

`socket.broadcast.emit` broadcasts the event to all the sockets except the one that sent the event.

`socket.broadcast.to(data.like).emit('user-liked', data)` emits an event to a single socket matching the socketid provided by `data.like`.

## Course Summary

It's worth digging through the source code of the final chat application to see how everything's wired up.
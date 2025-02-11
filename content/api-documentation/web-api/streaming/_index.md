---
title: Streaming
weight: 40
---

# Streaming

## Overview
Alpaca's API offers WebSocket streaming for account and order updates which follows the [RFC6455 WebSocket protocol](https://tools.ietf.org/html/rfc6455).

To connect to the WebSocket follow the standard opening handshake as defined by the RFC specification to the `/stream`
endpoint of Alpaca's API. Alpaca's streaming service supports both JSON and MessagePack codecs.

Once the connection is authorized, the client can listen to one or more streams
to get updates on particular changes.  These are the streams the client can
choose to listen to.

- account_updates
- trade_updates

The details of each stream will be described later in this document.

In order to listen to streams, the client sends a `listen` message
to the server as follows.
```
{
    "action": "listen",
    "data": {
        "streams": ["trade_updates"]
    }
}
```

The server acknowledges by replying a message in the `listening` stream.

```
{
    "stream": "listening",
    "data": {
        "streams": ["trade_updates"]
    }
}
```

If some of the requested streams are not available, they will not appear
in the `streams` list in the acknowledgement.
Note that the `streams` field in the listen message is to tell
the set of streams to listen, so if you want to stop receiving
updates from the stream, you must send an empty list of streams
values as a listen message.  Similarly, if you want to add more
streams to get updates in addition to the ones you are already
doing so, you must send all the stream names not only the new
ones.

In order to maintain the state of their brokerage accounts at Alpaca, along with requesting from the REST API, clients can also
listen to the trade streams for their accounts. This will ensure any running algorithms will always have the most up-to-date
picture of any accounts they are trading with at Alpaca.

## Authentication
The WebSocket client can be authenticated using the same API key when making HTTP requests. Upon connecting to the WebSocket
client must send an authentication message over the WebSocket connection with the API key, and secret key as its payload:
```
{
    "action": "authenticate",
    "data": {
        "key_id": "{YOUR_API_KEY_ID}",
        "secret_key": "{YOUR_API_SECRET_KEY}"
    }
}
```

The server will then authorize the connection and respond with either a successful response:

```
{
    "stream": "authorization",
    "data": {
        "status": "authorized",
        "action": "authenticate"
    }
}
```

or an unathorized response:

```
{
    "stream": "authorization",
    "data": {
        "status": "unauthorized",
        "action": "authenticate"
    }
}
```

In the case the socket connection is not authorized yet, a new message under
the `authorization` stream is issued in response to the listen request.

```
{
    "stream": "authorization",
    "data": {
        "status": "unauthorized",
        "action": "listen"
    }
}
```

## Order Updates
Updates with regards to orders placed at Alpaca are dispatched over the WebSocket connection under the event `trade_updates`, and include
any data pertaining to orders that are executed with Alpaca. This includes order fills, partial fills, as well as cancellations and
rejections of orders. Clients may listen to this stream by sending a listen message:

```
{
    "action": "listen",
    "data": {
        "streams": ["trade_updates"]
    }
}
```

Any listen messages received by the server will be acknowledged via a message on the `listening` stream. The message's
data payload will include the list of streams the client is currently listening to:

```
{
    "stream": "listening",
    "data": {
        "streams": ["trade_updates"]
    }
}
```

An example message sent over the `trade_updates` stream would look like:
```
{
    "stream": "trade_updates",
    "data": {
        "event": "fill",
        "order": {
            "id": "7b7653c4-7468-494a-aeb3-d5f255789473",
            "client_order_id": "7b7653c4-7468-494a-aeb3-d5f255789473",
            "asset_id": "904837e3-3b76-47ec-b432-046db621571b",
            "symbol": "AAPL",
            "exchange": "NASDAQ",
            "asset_class": "us_equity",
            "side": "buy",
            ...
        }
    }
}
```

The above message was for a `fill` event, however, there are many different events that can occur as an order progresses
through its life cycle. The most common events are described in detail below:

- `new`
- `partial_fill`
- `fill`
- `done_for_day`
- `canceled`
- `expired`

Less common events are described below. Note that these states only occur on rare occasions, and most users will likely never
receive stream messages for these events.

- `pending_cancel`
- `stopped`
- `rejected`
- `suspended`
- `pending_new`
- `calculated`

The common and rare events all correspond to order statuses. For more information on what those statuses mean, please refer
back to the [Orders]({{< relref "/orders/_index.md#order-lifecycle" >}}).

## Account Updates
Users may also listen to the account updates stream under: `account_updates`. This stream provides clients with updates pertaining
to their brokerage accounts at Alpaca, including balance information. The account updates stream can be listened to in the same
way as the trade updates stream, and in fact, both streams can be listened to simultaneously:

```
{
    "action": "listen",
    "data": {
        "streams": ["account_updates", "trade_updates"]
    }
}
```

It is highly recommended that clients listen to both streams when using Alpaca's streaming API. Any time there is a state change to the listening user's account, an update is sent over the WebSocket:
```
{
    "stream": "account_updates",
    "data": {
        "id": "ef505a9a-2f3c-4b8a-be95-6b6f185f8a03",
        "created_at": "2018-02-26T19:22:31Z",
        "updated_at": "2018-02-27T18:16:24Z",
        "deleted_at": null,
        "status": "ACTIVE",
        "currency": "USD",
        "cash": "1241.54",
        "cash_withdrawable": "523.71"
    }
}
```

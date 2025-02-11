---
title: Orders
weight: 20
---

# Orders API

The Orders API allows a user to monitor, place and cancel their orders 
with Alpaca. Each order has a unique identifier provided by the client. 
This client-side unique order ID will be automatically generated by the 
system if not provided by the client, and will be returned as part of the 
order object along with the rest of the fields described below. Once an 
order is placed, it can be queried using the client-side order ID to check 
the status. Updates on open orders at Alpaca will also be sent over the 
streaming interface, which is the recommended method of maintaining order 
state.

For further details on order functionality, please see the [Understand Orders page]({{< ref path="/orders/_index.md" >}}).

{{< rest-endpoint resource="orders" method="GET" path="/v2/orders" >}}
{{< rest-endpoint resource="orders" method="POST" path="/v2/orders" >}}
{{< rest-endpoint resource="orders" method="GET" path="/v2/orders/{order_id}" >}}
{{< rest-endpoint resource="orders" method="GET" path="/v2/orders:by_client_order_id" >}}
{{< rest-endpoint resource="orders" method="DELETE" path="/v2/orders/{order_id}" >}}

## Order Entity

### Example
{{< rest-entity-example name="order-v2">}}

### Properties
{{< rest-entity-desc name="order-v2" >}}

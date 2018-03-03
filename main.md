# initial notes on protocol

## brief

immdb will store event sourcing data. The data will be sequential and immutable.
All data will be sent encrypted. It will be the expectation that servers will cache a state of the system
and infrequently read large amounts of events. At any point a client should be able to read all events up to
a specific event. When events are written they will be verified on all read nodes to ensure it can be added.

Database will need to be able to scale out and work in a distibuted environment. All data stored will be immutable and
will not change

## lifecycle

- All requests begin with the client connecting to server
- Client will send public key from a private/public key combiniation
- Server will use match public key to stored private key and match
- Server will then generate a symmetic encryption key and send response to client with key
- Client will submit a query or write to server using encryption key
- Server will execute query or write and respond with data encrypted with key
- Server will then close the connection and delete key

## writing data

- write packets should contain event name
- write packets should contain event chain
- write packets should contain id
- id's are a md5 hash of the previous event
- a write will be successful if the id matches the hash of the last event on the chain
- a write will fail if any node cannot verify previous statement
- data will be in json format

```
{
  "event": String,
  "chain": String,
  "id": String,
  "data": Any
}
```

## reading data

- data will be read as a stream
- packet will contain event chain
- packet will contain id of event to read up to (not including)

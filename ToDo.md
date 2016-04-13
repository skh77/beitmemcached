# TODO-list for this project #

Here are some features that we would like to implement in the BeIT Memcached client:

### Parallel multi-get ###

Currently, the multi-get method builds a list of servers it needs to talk to, and then retrieves the objects sequentially. Using the asynchronous socket methods with callbacks, it should be possible to parallelize this, which would further speed up the client.

### Flexible Key Transformers, Serializers and Server Distributors ###

For compatibility reasons with other memcached clients in other languages, it would be nice if you could easily change the behaviour of the client so that it matches some other client better. For that, we need a key transformer if you for example want your keys base64-encoded instead of utf8 like our client does now. We also need to open up the serializer so you can choose serialization methods yourself, and choose the values of the flags parameter yourself. And finally, we should make it possible to change the algorithm for server distribution. The current implementation is kinda like libketama, but not exactly. All of this together would make our client more compatible with other clients, which is always a nice thing.

### Binary Protocol ###

It would be nice to also support the new binary protocol for memcached, but without a memcached server version for Windows which supports this, there isn't much need yet.

### Simple Replication ###

Another nice feature to have would be simple client replication as described by Trond here:

http://blogs.sun.com/trond/date/20090625
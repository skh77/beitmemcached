# Features #

This page describes the main features and goes into some detail on the underlying algorithms and design choices.

### Socket Pooling ###
The BeIT Memcached client contains a variable-size socket pool to increase performance by maximizing the reuse of connected sockets. The client maintains one pool per memcached server it is configured to use. Under heavy use, the pool will grow in size up to the value specified by the `MaxPoolSize` property. With less use, the pool will shrink in size to the value specified by the `MinPoolSize` property. It is also possible to configure the speed with which the pool shrinks back using the `SocketRecycleAge` property. Finally, there is a `SendReceieveTimeout` property which controls the socket send timeout, receive timeout and connection timeout.

Depending on your application and environment you may wish to tweak these properties for optimal performance.

It is also possible to disable socket pooling simply by setting both `MinPoolSize` and `MaxPoolSize` to 0.

### Hashing and Server Selection ###
The default hashing algorithm used for keys is the modified Fowler-Voll-No hash as described and discussed on http://www.isthe.com/chongo/tech/comp/fnv/ and http://home.comcast.net/~bretm/hash/6.html. This hash is very fast, has a good and uniform distribution, and a good avalanche behaviour, which is exactly the properties that are important for a memcached client since it's about scattering keys as much as possible.

We are using the "ketama" method for choosing a server depending on the hash of the key as described here: http://lists.danga.com/pipermail/memcached/2007-April/003834.html. The advantage of this method is that it consistently maps a given key to the same server, and if a server is added or removed from the configuration, the mapping stays mostly intact. If a server is removed, the keys that mapped to it will be evenly distributed among the remaining servers. If a server is added, it will take over an even distribution of keys that mapped to other servers.

### Custom Serialization and Encoding ###
To decrease the size of the objects stored in the memcached servers, the client implements a custom serialization scheme. Objects of the following types are serialized directly into their native byte representation:

`bool, byte, short, ushort, int, uint, long, ulong, float, double` are serialized into their native byte representation.

`DateTime` is serialized into a `long` containing the Ticks value.

`string` is encoded in UTF8.

`byte[]` is stored straight without any conversion.

All other objects go through the regular `BinaryFormatter` runtime serializer.

The type indicator is stored in the flags field according to the values given in the `SerializedType` enum.

### Compression ###
The client also supports compression for storing large objects. It uses the built-in DeflateStream compression, which compresses data into the Deflate format. If the bytearray that is to be stored is larger than the value specified in the property `CompressionThreshold`, the data will be compressed before stored in the memcached servers, and automatically decompressed when retrived. The default value of `CompressionThreshold` is 128 kB.

### Failover ###
This client does not support automatic server failover. At first glance, failover seems like a good idea. If a memcached server goes down, the application would automatically spread its items to the remaining servers, and when it goes up again, it would resume responsibility for its original keys.

However, if you look at it in detail, you will see that as soon as a server fails, this would be detected by the entire application at roughly the same time, and the failover would be smooth. But when that server comes back online, not all parts of the application would detect this at the same time, it would depend on how often each client retries the failing server. This means that automatic failover can cause application-wide synchronization errors, which according to the underlying philosophy of memcached, is something that should never, ever happen. Therefore, the BeIT Memcached client does not support automatic failover.

If a server should fail, the client will instead mark that server as dead, log the error, and retry at a specified time interval later. All method calls that would land on the dead server would instantly fail, and a portion of the data would simply not be cached. As a server administrator you can then either get the server back up, or remove it from the configuration. Either way, there will be no synchronization issues in the application.

### Errors, Exceptions and Logging ###
As a general rule, the client will **never** throw exceptions for runtime errors such as communicating with the servers. If something goes wrong while connecting, serializing, deserializing, sending data, or receiving data, it will simply appear as a cache miss in the get methods, and a failure in the store methods, and the actual error will be logged in the background. The reason for this is that these errors are not exceptional behaviour, and should not be handled differently by the program than regular cache misses.

Exceptions will generally be thrown on startup errors and programmatical errors. If a client is not configured properly, or if it is unable to resolve a host, it will throw a `ConfigurationErrorsException` with an appropriate message. If the client is used improperly such as given keys that do not conform to the memcached protocol specification, it will throw an `ArgumentException` or similar. This should allow you to quickly find errors from improper use of it while developing.

There are many logging frameworks for .Net, and rather than ship the client with our favourite logging framework and an unnecessary dependency, we created a `LogAdapter` that is easily modified to whatever logging framework you are using, and by default does simple logging to the Console. This means that everyone can get their logging the way they want it.
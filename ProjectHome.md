# BeIT Memcached is a client for [memcached](http://code.google.com/p/memcached/wiki/Start) written in C# 2.0 #

You can check out the [Features](Features.md), read about how to [Embed](Embed.md) this client, or check the [Examples](Examples.md). If you want to help out, you can go to the ToDo page. If you have any questions, comments or other feedback, feel free to [Contact](Contact.md) us.

### The client supports all memcached commands: ###
  * Set
  * Add
  * Replace
  * Append
  * Prepend
  * Cas
  * Get
  * Multi-Get
  * Delete
  * Increment
  * Decrement
  * Stats
  * Flush

### In addition, it has the following features: ###
  * Consistent hashing
  * Socketpool with configurable size
  * High-performance
  * Key prefixing
  * Compression
  * Detection of dead servers
  * Small code-size
  * Very easy to embed
  * No external dependencies
  * No unmanaged code
  * No background threads
  * Flexible logging
  * Implementation follows [memcache protocol specifications](http://code.sixapart.com/svn/memcached/trunk/server/doc/protocol.txt) strictly.
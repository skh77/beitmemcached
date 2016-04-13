# Getting the code #
Simply download the source archive from the project home, or check out the files from svn from the project source page. Either way, you will get a complete Visual Studio 2005 solution which contains the client and some example code that shows how it's used.

# Embedding the code into your project #
You have multiple options for embedding the client in your code.
  * Copy all the .cs files in the ClientLibrary folder to your project, include them, and make sure your project references System and System.Configuration.
  * _or_ Copy the entire BeIT Memcached project to your solution and include it.
  * _or_ Change the output type of the project to Class Library, compile, copy the resulting .dll to your project and include it.

# Using the code in your project #
The only class you need to access is the `BeIT.MemCached.MemcachedClient` class. Start by calling the static `Setup` method with a name and a list of memcached servers that the client should use. Then call the `GetInstance` method to get this instance of the client. On that object you can then call the various methods such as `Set` and `Get` to use the cache.

It is also possible to set up a client using the standard app.config file.

Check the [Examples](Examples.md) page for more detailed usage info, and check the code itself for a complete list of all methods and their overloads.
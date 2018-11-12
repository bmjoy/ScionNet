<p align="center"> 
  <img height="68" width="380" src="https://i.imgur.com/nQOjeEL.png" alt="alt logo">
</p>

Overview
--------
ScionNet is a concurrent networking backend for the .NET platforms and Unity.

Designed with high-performance in mind and optimized for efficient use of resources, to be lightweight and powerful tool with a low learning curve for rapid integration, prototyping and developing multiplayer games.

### Purpose

Concurrency is the way to run multiple tasks in parallel and take advantage of multi-core processors for building high-performance systems. ScionNet is a tool which is intended to help the programmer create a network logic within a few hours and automatically benefit from the concurrency and data management under the hood. ScionNet offers lightweight modules to boost the development process and parallelize the computations across multiple threads that can communicate with each other.

### System

The system is based on a lightweight inter-thread messaging architecture where the function calls, transport events and network message are translated into inter-thread messages with data for processing. Instead of polymorphic serialization that is widely used in the distributed concurrent systems to transfer messages across threads, ScionNet uses dynamically growing circular objects pools in order to avoid serialization cost, transfer data directly to te designated threads and reuse message objects on demand.

Core
--------

### Data management

To void GC allocations and pauses during runtime, the system utilizes thread-safe high-performance arrays pool and self-stabilizing concurrent objects pool which essentially a segmented circular buffer. The pools used when data must be transferred to a particular thread for further processing. The storages of the pools are warming-up at initialization stage based on a count of CPU cores, maximum allowed number of connections and other conditions. When resources are not enough due to high-loads, the pools will start allocating memory trying to adapt to a workload and stabilize. Every function call, transport event or network message is an inter-thread unit which rent/acquire resources of the pools.

### Messages and queues

All inter-thread messages are processed using multi-producer multi-consumer, array-based, GC independent, causal first-in-first-out, non-blocking queue. This queue is never interrupting threads, the cost of enqueue/dequeue functions is one compare-and-swap atomic operation plus volatile read/write based on memory barriers. Producers and consumers are separated from each other, they don't touch the same data while the queue is not empty. The data from dequeued messages is used to perform computations and execute functions concurrently. The queues are warming-up at initialization stage based on a count of a maximum allowed connections and maximum possible update frequency.

### Concurrent processing

In multiplayer games, it's very important to send, receive, and process data as fast as possible. A single-threaded code in the game loop is a bottleneck of any system which will perform only as fast as the game loop does the processing, and conversely, the systems are slowing down the game loop which leads to frame-rate drops. The core performs all the computations and execution of functions concurrently which allows continuously run costly operations like broadcasting network message across a large number of clients without any interruptions. The transport events and network messages can be consumed in any thread to parallelize computations and process data asynchronously.

### Networking

ScionNet utilizes the heavily modified reliable UDP networking library [ENet](https://github.com/nxrighthere/ENet-CSharp). It's a time-proven solution which used in many popular multiplayer games and game engines. ENet provides connection management, sequencing, channels, reliability, fragmentation and reassembly, compression, aggregation, adaptability, and portability. Great feature-set and outstanding performance perfectly fit the concept of the system. Reasonable parallelization of network logic across multiple threads and streams is the way to achieve stopless flow of data and low latencies.

### Encryption

Although we are developing multiplayer games, rather than a remote control system for the Death Star, encryption is still an important part. ScionNet uses [Hydrogen](https://github.com/jedisct1/libhydrogen) which provides high-entropy, curve25519 elliptic curve, gimli permutation, and zero dynamic memory allocations. The core utilizes the Hydrogen for secure per channel encryption based on the Noise protocol. After establishing a secured connection using keys exchange, a payload of the packets can be anonymously transmitted between server and client using specified channels. The core is verifying every operation and allows the programmer to handle errors.

Modules
--------

In addition to the core module which is using built-in modules internally, ScionNet has essential tools in the arsenal to boost the development process and help the programmer.

### Buffers

Buffers pool is a key part of any high-performance managed networking system to avoid GC pressure and reuse buffers instead of allocating them over and over again. The thread-safe buffers module is based on implementation made by the .NET team. The core utilizes this module for data management, and the programmer can freely use it for custom logic as well.

### Compression

Efficient packing of data is a crucial part in networking to reduce bandwidth consumption. Out of the box, the compression module provides three well-known algorithms which widely used in the multiplayer games. Compressed data can be efficiently serialized using built-in compact encoding of integer family.

[Half precision](https://en.wikipedia.org/wiki/Half-precision_floating-point_format)

[Bounded range](https://gafferongames.com/post/snapshot_compression/#optimizing-position)

[Smallest three](https://gafferongames.com/post/snapshot_compression/#optimizing-orientation)

### Serialization

BitBuffer designed with speed and size in mind for networking games. It means that serialized data will be as small as possible and the recipient should be able to deserialize the data as fast as possible. Bit-packing algorithms based on the encoding of integer family greatly beat popular general-purpose serialization solutions in terms of data size and space efficiency.

- Fast
- Lightweight
- Compact

### Culling



### Threading



### Port-forwarding

Computers behind NAT can't be reached from outside, and this is particularly painful for peer-to-peer or friend-to-friend networking. The main goal is to simplify communication among computers behind NAT devices with a transparent process of mapping an external IP address and ports to achieve communication between computers. 

[OpenNAT](https://github.com/lontivero/Open.NAT)

- UPnP / PMP support
- Easy to use

Scaling
--------



Building
--------



Usage
--------



Support
--------



References
--------

### Concurrency

(Dmitry Vyukov) [Scalable Architecture](http://www.1024cores.net/home/scalable-architecture/introduction)

(Dmitry Vyukov) [General Recipe](http://www.1024cores.net/home/scalable-architecture/general-recipe)

(Dmitry Vyukov) [First Things First](http://www.1024cores.net/home/lock-free-algorithms/first-things-first)

(Dmitry Vyukov) [Your Arsenal](http://www.1024cores.net/home/lock-free-algorithms/your-arsenal)

(Dmitry Vyukov) [Scalability Prerequisites](http://www.1024cores.net/home/lock-free-algorithms/scalability-prerequisites)

(Dmitry Vyukov) [Reader-Writer Problem](http://www.1024cores.net/home/lock-free-algorithms/reader-writer-problem)

(Dmitry Vyukov) [Producer-Consumer Queues](http://www.1024cores.net/home/lock-free-algorithms/queues)

(Trisha Gee) [Introduction to the Disruptor](https://www.slideshare.net/trishagee/introduction-to-the-disruptor)

(Jenkov Aps) [Non-blocking Algorithms](http://tutorials.jenkov.com/java-concurrency/non-blocking-algorithms.html)

### Garbage collector

(Sergey Teplyakov) [Understanding different GC modes with Concurrency Visualizer](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)

(Matt Warren) [Measuring the impact of the .NET Garbage Collector](http://mattwarren.org/2014/06/18/measuring-the-impact-of-the-net-garbage-collector/)

(Matt Warren) [Analysing Pause times in the .NET GC](http://mattwarren.org/2017/01/13/Analysing-Pause-times-in-the-.NET-GC/)

(Adam Sitnik) [Pooling large arrays with ArrayPool](https://adamsitnik.com/Array-Pool/)

### Networking

(Glenn Fiedler) [Networking for Physics Programmers](https://www.gdcvault.com/play/1022195/Physics-for-Game-Programmers-Networking)

(Glenn Fiedler) [Snapshot Interpolation](https://gafferongames.com/post/snapshot_interpolation/)

(Glenn Fiedler) [Snapshot Compression](https://gafferongames.com/post/snapshot_compression/)

(Glenn Fiedler) [State Synchronization](https://gafferongames.com/post/state_synchronization/)

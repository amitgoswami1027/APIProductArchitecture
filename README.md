# APIProductArchitecture
This repo evaluates and understand about the different aspects of end to end API Desing
# Introduction
On a typical day, Alice, a waiter at a restaurant, serves customers their orders. An example of an order they might receive is: "Alice, can I have a caffe latte with no sugar, please?" Alice would then take the order to the kitchen with the instructions in this format: "Table 10, Customer A: One caffe latte - single serving, no sugar." The chef, working in the kitchen, then prepares the order and hands it over to Alice, who serves it to the customer.

![image](https://github.com/user-attachments/assets/1c26d66c-ade4-4a94-abef-bd61901a6cc1)

## What is an Application Programming Interface?
An application programming interface (API) is an intermediary through which two software components communicate to share data and to get useful work done. As the name implies, exchanging information is possible because of the presence of a software interface between these components. The software components can reside on the same or different machines connected through a network.

We can relate this to the restaurant analogy above in the following way:
* The customers are software applications/clients requesting a service.
* Alice, the waiter, is the API acting as the interface between the clients and the back-end services.
* The kitchen and chef are analogous to downstream services, including the serving application and the persistence layer.

  ![image](https://github.com/user-attachments/assets/4e57612d-47ef-46d2-93f5-489a140488ee)

## Understanding interfaces
Just like Alice, an Instruction Set Architecture (ISA) serves as an intermediary between the hardware and software of a computer system. ISA has two types of instructions. There are instructions that can be called directly by applications, and then there are privileged instructions that can’t be called directly. Therefore, the operating system (OS) on a machine places the privileged requests to the ISA, which knows how to get the task done. For example, when software gives an assignment to a variable, both the compiler and the runtime translate that variable to a memory address and issue a set of instructions with the appropriate parameters to the hardware. Therefore, ISA acts as an interface that facilitates the operating system and abstracts the complexity of assigning a new value to a memory address.

Computers utilize the idea of interfaces to enable communication between software components as well. For example, system calls are performed by application programs that are installed on top of operating systems. This, again, is possible through another interface that enables developers to request the services of operating systems in the form of system calls. Standardized interfaces are commonly used in the computing world between hardware components, for example, the PCI bus interface connecting peripherals to the motherboard. They are also used between classes of software—for example, public functions of a class—and between distributed systems, such as interface exposed by S3 storage service to the clients.

![image](https://github.com/user-attachments/assets/beb5a7b1-07a5-4257-aa93-54cd7556ac5f)

## The Narrow Waist of the Internet
APIs send requests and receive responses over a network. The internet opts for a layered approach to its architecture to manage the complexity of sending data across two entities, which could potentially be in two far-away parts of the world. Initially, the internet standardized the IP as a middleware around which all the innovation happens. We could make any application hoping that the IP would relay its data, and any new communication technology could flourish underneath the IP layer, provided it supports IP. However, many hiccups have happened over the years in the attempt to achieve that goal. Today, most public-facing API calls use HTTP.

Let’s suppose we use a raw IP packet with our custom transmission protocol, something other than Transmission Control Protocol (TCP) or User Datagram Protocol (UDP). In that case, it probably won’t make it far, because most enterprises use firewalls that only allow a handful of well-known applications, and drop everything else. (Actually, such a packet might not pass through our ISP router!)

The Internet Protocol (IP) has mainly dominated the network layer. This is especially because it maximizes interoperability, makes addressing universal, and provides best-effort service. Due to this reason, the internet was said to have a narrow waist architecture because the IP extends its support to many upper-layer transport and application protocols and many bottom-layer communication technologies. The concept is depicted in the illustration below:

![image](https://github.com/user-attachments/assets/f65eed8c-6fa8-4442-87a9-790f03c1fa71)

## Latency and Throughput
We often use high-level abstractions like sockets, message passing, remote procedure calls, or even more advanced constructs through API calls to simplify the complexities involved in network communications. On the other hand, applications have different needs in terms of user-perceived latency and throughput. For example, if we have a maximum budget of 500 ms for an API call, and the call needs to traverse cross-continental boundaries, the network consumes about 150 to 250 ms of the available 500 ms. This leaves about 250 ms for the actual processing on the server-side and any minute processing on the client-side.

![image](https://github.com/user-attachments/assets/4e7e923d-cd27-4424-9fd1-e259c5e26ca8)

### Throughput
Throughput refers to the process-to-process logical data rate that inevitably passes through many network hops. If we have a 1 Mbps effective throughput of some link or network, we can’t send more than 1 Mb of data in a second. We need to send the remaining data in the next second. In a specific unit of time, the amount of data that can be transmitted from the sender to the receiver is measured by the throughput.
Let’s assume that some IoT device can only receive and process data at a 1 Mbps rate, and the network can support a rate of up to 2 Mbps. In that case, IoT devices are not able to fully utilize what the network is providing (2 Mbps).

![image](https://github.com/user-attachments/assets/455f3ca2-2dd2-4f4e-92f5-6f18901d1ee3)

### Latency
Latency means how long (in terms of time) a user-level message takes to travel from the sender to the receiver. We’re often more concerned about the user-perceived delay, which is a function of the round-trip time (RTT). Traditionally, throughput and latency have tradeoffs—for example, increasing the throughput or utilization might increase the latency as well. 

Typically, network latency has the following constituent components:
```
latency=transmission delay+propagation delay+queuing delay+switch/node processing delay
```
#### Transmission delay
The time it takes to place a packet on the transmission link is the transmission delay.
![image](https://github.com/user-attachments/assets/8341695f-388d-4e48-8b41-de8331aaaef2)

We can calculate this delay using the following formula:
```
Transmission Delay= bandwidth/packetsize

```
#### Propagation delay
Propagation delay is the time a bit takes to go from start-host to end-host. For example, in the case of cross-continental links, the propagation delay would be large because bits need to travel from one continent to another.
We can calculate the propagation delay using the following formula, where distance is directly proportional, and speed is inversely proportional to the propagation delay.
```
Propagation Delay= speed/distance

```

#### Queuing delay
Queuing delay is the time needed for a node to hold the packet before it can proceed. The packet is kept in a queue until its turn comes up. At times, the internet is called the “network of queues” because a network packet needs to relay through many network routers to reach the final destination. A packet might need to wait in a queue at a router before it can proceed, just like a traffic intersection, where cars on one side need to wait while others are crossing the intersection.

![image](https://github.com/user-attachments/assets/e745b37c-ee14-4a7c-98e8-809043895ddd)

#### Processing delay
Processing delay is the time the node (routers) and end host (server) take to process the packet. It depends on the processing speed of the nodes and end host.
![image](https://github.com/user-attachments/assets/8935aaf3-bce4-428b-8edf-ece54d46415b)

### Latency-bandwidth product and network utilization
Sending one packet and then waiting for a response is wasteful. Back-to-back packet transmission will increase network utilization. We should know how many packets a link can hold, which can be determined using the latency-bandwidth product. This refers to the amount of data that could be in transit through the network at any time. The formula for the latency-bandwidth product is:
```
latency∗bandwidth 
```
![image](https://github.com/user-attachments/assets/675000b3-9f92-4045-b491-1dbbf0fb22a7)

For example, if the effective bandwidth measured on the client-side is 1 Mbps, and the client-to-service user-perceived time is 200 ms, then how many bits will be in transit?
```
latency∗bandwidth =200∗10^−3sec∗1∗10^6 bits/sec=200∗10^3 bits=200 kbits
```
If the senders want to get the acknowledgment signal from the receiver, then the amount of bits the network can hold is RTT∗Bandwidth. We aren't fully utilizing the network if we don't send all of the data that a link can hold.

## The World Wide Web
The World Wide Web (WWW) is a hypertext-based information system that interlinks documents spread and stored across millions of machines all over the Internet. It’s commonly known as the web or w3.
Web Protocols - Since the web follows the client-server model, there should be well-defined protocols to make their communication interoperable. On the web, the client requests some data that is provided by the server in a proper format. The client application, usually a browser, displays the data received from the server in a specific format—a web page. This information flow between clients and servers is directed by a protocol called Hypertext Transfer Protocol (HTTP).Application layer protocols such as the File Transfer Protocol (FTP), Simple Message Transport Protocol (SMTP), HTTP, and so on, use lower layer protocols like Transmission Control Protocol (TCP), User Datagram Protocol (UDP), and Internet Protocol (IP) to provide services to end users.

![image](https://github.com/user-attachments/assets/95552466-60ba-46e3-9526-f9204ee6fc80)

The Hypertext Transfer Protocol (HTTP) is a stateless, application-layer protocol for distributed and hypermedia information systems. It's the foundation of data communication for the World Wide Web and is considered the de facto standard for client-server resource sharing. Web servers and client applications (browsers) must adhere to the message formats and transmission methods provided in the HTTP specifications. For instance, when we type a URL into a browser, the web server receives an HTTP request directing it to fetch and deliver the specified web page.

The following figure shows the HTTP/3.0 protocol stack:
![image](https://github.com/user-attachments/assets/08fa7132-df1c-48e5-9d18-ec04acd4b453)

## Remote Procedure Calls (RPCs)
A remote procedure call (RPC) architecture is a service or action-oriented style of creating APIs, where resources are distributed among different services running on remote machines. RPC facilitates interprocess communication by enabling clients to run remote procedures (functions) in a simple local function-call-like abstraction. A generic RPC architecture is shown in the illustration below:

![image](https://github.com/user-attachments/assets/eb3c9816-00f1-40da-8dc6-80a954000da8)

![image](https://github.com/user-attachments/assets/8183134a-cd49-45a7-ac75-df8c8041c9f3)

The RPC runtime is the actual software program that does the heavy lifting and glues all the components together. It communicates with the underlying operating system and network interface to transfer data over the wire. RPC uses the interface definition language (IDL) to set up templates that specify function names, parameters, and so on, to glue processes running in different development environments. The IDL compiler runs only once and generates code stubs to support data conversion between different formats understood by programs written in different languages. RPC-style APIs are easy to implement thanks to traditional function-call-like integration support provided by IDL headers.

While there are many implementations of the RPC mechanism, the following are a few modern implementations usually used in different kinds of APIs:
![image](https://github.com/user-attachments/assets/2498da31-3010-4dbc-8464-7e4b5100b383)
























## Benefits of APIs
  ![image](https://github.com/user-attachments/assets/9cf4063c-883c-4070-9223-8ad6b3aeff58)




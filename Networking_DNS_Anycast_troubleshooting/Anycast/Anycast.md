***
# What is Anycast? 

Anycast is a type of network routing where incoming requests can be routed to multiple locations and in multiple ways. By default, using Anycast method, incoming requests are routed to the nearest data center or data center that has more available resources to operate the request at the moment.

Anycast works because of BGP (routing protocol) that can search and cache the shortest way to ip address.
When we create multiple clients with the same ip, BGP doesn't know that it is different machines, BGP just route packets to nearest one. 

![Anycast](https://github.com/qqwerty222/obsidian/blob/main/Networking_DNS_Anycast_troubleshooting/Anycast/Anycast.png)

### Advantages:

- #### Reducing Latency:
	 Anycast systems are able to reduce latency of user requests using server that are closer to them or using routes that are less loaded with another requests

- #### DDoS Mitigation/ Load Balancing
	 In anycast system, traffic spreads in balance between different servers. It is reducing load on each single server. Also give some kind of passive resistance from DDoS attacks, because to drop service you need to overload each server around the world.

- #### Improved scalability 
	 Anycast system are easy to scale. All the servers run the same service, and it is not a problem to add another one in a location where load or latency is too high.

- ####  Failure resistance
	 Even if one of the servers drop, user will get access to service using another servers. 
---

## BGP - Border Gateway Protocol

It is a protocol that responsible for finding the shortest and the most efficient route from start to destination point. BGP runs in the whole internet, and every route is a combination of "hops" through different autonomous systems.

![BGP](https://github.com/qqwerty222/obsidian/blob/main/Networking_DNS_Anycast_troubleshooting/Anycast/BGP.png)

### Autonomous system (ASes)

Internet is a network that consists from smaller networks. And each smaller network is called an Autonomous system. Practically each AS is a huge combination of routers that some organization runs.

Every time you send a packet to the internet, BGP count through which combination of ASes route to destination will be shorter and faster.

Autonomous systems operated by service providers (ISPs) or other large organisations, such as tech companies, universities or government agencies. Each AS should have a registered autonomous system number (ASN). 

- External BGP (eBGP) routes traffic over the global internet
- Internal BGP (iBGP) routes traffic in local autonomous networks.
 

# Event-Driven Content Positioning

This repository is the home of an edge-native design meant to solve performance problems in the following scenario - 

1. User logins to a site or native application (or triggers an event in some other fashion, ex., by visiting a certain URL).
2. Following login, the user is expected to request a dynamic piece of content unique to them. For example, in the case of online commerce, the dynamic piece of content could be sales or incentive information unique to that user, generated dynamically at the time the event (such as login) occurs.
3. The backend system begins to generate the content upon seeing the event.
4. Once the content is generated, the backend system publishes the event to a distributed message queue.
5. The distributed message queue caches the messages locally.
6. The user can then request the dynamic content, and will find it alreedy generated and cached across the distributed nodes. 

The benefits of this design include - 

1. "Eager processing" - allowing the initial event (such as login) to kick off the object generation greatly reduces and can eliminate the impact of processing time on the user experience. 
2. Low-latency delivery- by positioning the content across the distributed nodes, download performance for the user can improve greatly by avoiding a round-trip to the backend system.
3. More security via less surface - by pre-positioning the content, the backend system no longer is in-line with the user request (as the distributed edge system becomes the origin source-of-truth for the content). This results in less traffic coming inbound to the backend system and reduces the potential attack surface in a security context.
4. System arbitrage - As pre-posisitioning takes the backend system out-of-line from the user request, the backend system can be placed nearly anywhere. This is advantageous in cases where the routine to generate the content is expensive from a computational standpoint. By completing the computation and publishing the content across the distributed nodes before the user makes a request, latency can be avoided as a placement factor, and computation can be located in places more cost-advantageous (regardless of latency). 

## Demonstration 

### Diagram

![edge positioning drawio](https://github.com/user-attachments/assets/5ab8b7d1-4c8b-441f-8bd1-7701903c49ad)

### Description

In the demonstration scenario, we are assuming that the user is connecting to an online retailer, and that the retailer is building unique offers or coupons for the user based on profile or other information (for instance, the user's geo-location derived from IP address).

The offer generation process can be compute intensive, and the retailer doesn't want the user to wait until the generation is complete before allowing them to continue into the store. 

However, the retailer also does not want to wait until a later point in their visit before the user is shown the offer. Ideally, the offer should come to user as soon as it's generated. 

### Components 

This repository builds an example of a lightweight content positioning system and includes a web client to show the benefits. The below steps include-

* An Offer Generator, which listens on an HTTP endpoint, and returns a unique ticket ID to user requests. The generator then builds a JSON object (representing an offer unique to that user), includes the ticket ID in the object, and publishes the offer to a distributed message queue.
* A node client, which is subscribed to the offers subject on the distributed message queue, and caches the offers objects locally.
* A simple HTTP endpoint, which serves the offer objects to clients, using the ticket ID as a request argument.
* An http client, which simulates the user story by generating a initial "login" event, then showing timing differences between the user methods for receiving their offers. 

Other components include-
* A Content Delivery and Security network in front of all of the endpoints.
* Global load-balancing (typically as part of the CDN), to bring users in the world to the closest distributed node.



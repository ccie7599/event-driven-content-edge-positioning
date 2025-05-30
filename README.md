# Event-Driven Content Pre-positioning

This repository is the home of an edge-native design meant to solve performance problems in the following scenario - 

1. User logins to a site or native application (or triggers an event in some other fashion, ex., by visiting a certain URL).
2. The event is a signal that the user will soon be to the point in a user story where they will request a dynamic piece of content. For example, in the case of online commerce, the dynamic piece of content could be sales or incentive information unique to that user, generated dynamically at the time the event (such as login) occurs.
3. The backend system begins to generate the content upon seeing the event.
4. Once the content is generated, the backend system creates another event indicated the content is ready.
5. The distributed edge system subscribes to these events, and once the content-complete event is seen, the edge system requests the content.
6. Following content receipt, the edge system distributes the event across it's nodes, making the content available for a content delivery edge network to serve the object to the user.

The benefits of this design include - 

1. Low-latency delivery- by positioning the content at the CDN edge, download performance for the user can improve greatly by avoiding a round-trip to the backend system.
2. More security via less surface - by pre-positioning the content, the backend system no longer is in-line with the user request (as the distributed edge system becomes the origin source-of-truth for the content). This results in less traffic coming inbound to the backend system and reduces the potential attack surface in a security context.
3. System arbitrage - As pre-posisitioning takes the backend system out-of-line from the user request, the backend system can be placed nearly anywhere. This is advantageous in cases where the routine to generate the content is expensive from a computational standpoint. By completing the computation and publishing the content to the CDN edge before the user makes a request, latency can be avoided as a placement factor, and the computation can be located in places more cost-advantageous (regardless of latnecy). 


---
author: "Daniel Jackson"
title: "System design reading exercise"
date: "2025-07-24"
description: "Learning about system design by reading concept specs and syncs"
ShowToc: true
TocOpen: true
hideMeta: true # removes date etc from post
summary: "System design reading exercise"
aliases:
hidden: true 
editPost:
  URL: "https://forum.essenceofsoftware.com"
  Text: "Forum" # edit text
  appendFilePath: false # to append file path to Edit link
---

To help you get going with designing systems of concepts, this exercise asks you to read a a few concepts and syncs  and answer some questions about them.

## Concepts
Consider the task of designing a URL shortening service. We might define three concepts: URLShortening, which maintains mappings between the short URLs and their expansions; ExpiringResource, which manages the relinquishing of resources that have finite lifetimes; and NonceGeneration, which generates the unique strings that the shortener uses. Here are some abbreviated specifications of the concepts:

### A UrlShortening Concept
**concept** UrlShortening  
**purpose** shorter or more memorable way to link  
**principle** after create generates a short url, lookup will return the original url  
**state**  
a set of Shortenings with  
- a targetUrl String  
- a shortUrl String  

**actions**    
register (shortUrlSuffix, shortUrlBase, targetUrl: String): (shortUrl: String)
- forms and saves a new short URL by appending the suffix to the base and associates it with the targetUrl  

lookup  (shortUrl: String): (targetUrl: String)  
- returns the target URL associated with a short URL

delete  (shortUrl: String)  
- deletes a short URL and its association with a target URL

### A NonceGenerator Concept
**concept** NonceGenerator \[Context\]  

**purpose** generate unique strings within a context  

**principle** each generate returns a string not returned before for that context  

**state**  
a set of Contexts with  
- a used set of Strings  

**actions**  
generate (context: Context) : (nonce: String)  
- return a nonce that is not already used by this context  

### An ExpiringResource Concept
**concept** ExpiringResource \[Resource\]  

**purpose** expire resources automatically to manage costs  

**principle** after setting expiry for a resource, the system will expire it after the given time  

**state**  
a set of Resources with  
- an expiry DateTime  

**actions**  
setExpiry (resource: Resource, seconds: Int)  

system expireResource () : (resource: Resource)  

### Some explanatory notes
- The expireResource action of ExpiringResource is a system action, performed spontaneously when its precondition is true (namely when the expiry time for some resource, which it returns, has arrived).
- The register action of UrlShortening takes a base URL; this ensures that the concept is not hardwired to a particular domain (such as tinyurl.com), and would allow the service to support user-specific domain names.

### Concept Questions
Read the concept specifications carefully and answer these questions:
- **Contexts**. The NonceGenerator concept ensures that the short strings it generates will be unique and not result in conflicts. What are the contexts for, and what might a context end up being in the URL shortening app?
- **Storing used strings**. Why must the NonceGenerator store sets of used strings? One simple way to implement the NonceGenerator is to maintain a counter for each context and increment it every time the generate action is called. In this case, how is the set of used strings in the specification related to the counter in the implementation? (In abstract data type lingo, this is asking you to describe an *abstraction function*.)
- **Words as nonces**. One attractive option for nonce generation is to use common dictionary words (in the style of [yellkey.com](http://yellkey.com), for example) resulting in more easily remembered shortenings. What are some disadvantages of this scheme? How would a variant of the NonceGenerator concept defined for this purpose differ from NonceGenerator? (Hint: consider in particular the state, and the generate action.)

## Synchronizations
Here are three of the synchronizations that might be included in a full design of the URL shortening application:

**sync** generate  
**when** Web.request (method: "shortenUrl", shortUrlBase)  
**then** NonceGenerator.generate (context: shortUrlBase)

**sync** register  
**when** Web.request (method: "shortenUrl", targetUrl, shortUrlBase) and
	NonceGenerator.generate (): (nonce)  
**then** UrlShortening.register (shortUrlSuffix: nonce, shortUrlBase, targetUrl)

**sync** setExpiry  
**when** UrlShortening.register (): (shortUrl)  
**then** ExpiringResource.setExpiry (resource: shortUrl, seconds: 3600)

### Some explanatory notes
- **Completions and invocations**. In the synchronization language, actions referred to in the when clause always correspond to action *completions*, and actions in the then clause to action *invocations*.
- **Named arguments and results**. The arguments and return values of actions are *named* (they're represented in the code simply as dictionaries), so, for example, the first sync invokes in its then clause the generate action of the NonceGenerator concept with the argument named *context* bound to shortUrlBase.
- **Bound variables**. The when clause can bind variables used in the other clauses; for example, the return of expireResource in the last sync says that the return value given by the return name *resource* is bound to the variable *shortUrl*. This bound variable is then passed as an argument to the delete action in the then clause.
- **Omitted names**. By convention, it is not necessary to include both the argument name and the bound variable name when they are the same (writing "x: x"), and mentioning the name once is short for this. So the mention of targetUrl in the when clause of the first sync is short for targetUrl: targetUrl, and binds the variable targetUrl to the argument of the request action called targetUrl.

### Synchronization Questions
Read the synchronizations carefully and answer these questions:
- **Partial matching**. In the first sync (called generate), the Web.request action in the when clause includes the shortUrlBase argument but not the targetUrl argument. In the second sync (called register) both appear. Why is this?
- **Omitting names**. The convention that allows names to be omitted when argument or result names are the same as their variable names is convenient and allows for a more succinct specification. Why isn't this convention used in every case?
- **Inclusion of request**. Why is the request action included in the first two syncs but not the third one?
- **Fixed domain**. Suppose the application did not support alternative domain names, and always used a fixed one such as "bit.ly." How would you change the synchronizations to implement this?
- **Adding a sync**. These synchronizations are not complete; in particular, they don't do anything when a resource expires. Write a sync for this case, using appropriate actions from the ExpiringResource and URLShortening concepts.
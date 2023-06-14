# Microservices

This document provides an overview of the Microservice architecture pattern, and associated patterns. It also introduces some technology associated with microservices, including containerized applications and CI/CD.

## Preamble

For about as long as we've been building software (which isn't _that_ long in the grand scheme human of engineering pursuits), we've been thinking of new ways to construct software to deal with the issues of _complexity_. This isn't a new thing. The Gandalf of the computer science world talked about it in 1968[^1]. He suggested building software in encapsulated layers of functionality, each at an increasing level of abstraction from the one below it, with an acknowledgement that keeping things that change together, while isolating their need from change from the other pieces that depend on them.

> Software development _is_ complex. And it is usually written by people that thrive on complexity. We _dig_ it. When it is our complexity, it is _awesome_ or _clever_. When it is someone else's though, it is _sucky_, or _a mess_. Work long enough in software, stuff you wrote will be your own _sucky_ code.

### A Tiny Bit of Woo Woo

Indulge me, please. Or skip this section. I've noticed a common thing when talking to some developers about Microservices. First, I'm usually either creating Microservices, or teaching others about them. When I am teaching about them, I like to create them with the students. We don't need any more high-level Powerpoint death marches. So often when we divorce the "view" of how something should be done from the "how", it is too easy to be dismissive. "Well, it sounds good on paper." is a common reproach.

However, when I actually _show_ how to do this, along with talking about it, there is another problem. It is based on the generosity of students and their ability to project upon the teacher the _best of intentions_. "Oh, he's showing a very simple example because there are other people here (not _me_ of course), that need super simple examples. But I write very complicated, important things! I get why he is doing this, but this wouldn't work for me."

I almost never do that. The code I write in class and the code I write "for real" are the same. Or, more, the process through which I write code in class mirrors exactly (or as exact as possible) the the process I use to write "real" code.

An Example: Perhaps I am teaching about how to write an app using Angular. At some point in the creation of the application, it inevitably becomes obvious that we are going to need an API of some type. Some server-side _thing_, maybe to persist our data between uses of the application, or something like that. I mean, you add some stuff to your todo list, you need to save it _somewhere_ right? So we will create a simple little API. Maybe an HTTP-based API that has a resource we can `GET` some data from, or `POST` some new stuff to. That kind of thing. If I'm doing it with .NET (a lot of my clients are from companies that use .NET), I'm create an API, maybe use Entity Framework and a local Sql Server, get the thing going. Just enough. The "seasoned" developers in the class just _know_ this is a "classroom" thing. That in real applications (a Todo list! I _wish!_), you need to get your data from the Mainframe, from Oracle, from DB2, whatever. They just assume that the way I am showing this in class is an _expedient_. A pedagogical device for their benefit.

I know this because they follow up with me, sometimes weeks later with questions like "How do you map Entity Framework to existing data in DB2? In Oracle?". Or they get blocked. Stalled out while another team considers merging their pull request to their API for the functionality they need.

I often have people that are "repeat customers" come to my class and actually tell me they love my classes because it is a "nice vacation". What they mean, I assume, is that they want a break from the complexity of their "real" applications, and want the thrill of "greenfield" development. "Must be nice!" they say.

As if I work in a world where there is no complexity. That every day I just "file new project" and create something from scratch without the need for testing, without the need for integration, without the need compliance, security, even dreaded requirements!. To be honest, I do. And to be double-honest, when I'm teaching, that stuff is sort of off in the margins. But that is the **exact point**. Your experience of writing software should be creating small, simple things. "Simple" here means the opposite of "Complex". Rich Hickey gave a great talk a few years ago about this that everyone should listen to each night as they fall asleep.[^2] Simple comes form the term "Simplex": "Dealing with one thing", and "Complex" means dealing with many things.

Microservices aren't _easy_, but they should be _simple_.

Here's the woo-woo[^3] part. Working as a software developer for a long time _can_ cause you to disassociate. It seems to force us to live "in our heads", to _hyperfocus_. We all _know_ that interrupting a software developer deep into their work is like poking a sleeping bear. They are holding a lot of stuff in their head (see: "Complex" above), and it's a fragile house of cards. Even a "schtick-tick-tick" notification from Slack can bring it all tumbling to the ground. That day is _gone_. Some of the absolute _best_ software developers I know have Tibetan Yogi levels of concentration when they are working. They skip meals, they don't notice it is dark out. Their children are gnawing on cans of beans from the cabinet out of hunger.

I know we glamorize this, and to some extent _rely_ on it from time to time. But it isn't good. It isn't healthy for you, and it isn't healthy for the code you are writing, either. Brief moments of it are necessary. Disassociating[^4] like that as a baseline way of being is pathological. It can be easily mistaken for the concept of "Flow"[^5]. It's a near neighbor to flow, for sure. But flow involves the sense of "complete involvement", and disassociation leaves some pieces of the "complete" thing out. If I were being very openly "woo woo" here I'd say it lacks the factor of "embodiment", which, while true, is easier to explain by simply saying we are cut off from our emotional reactions to what it is we are doing. I've often worked in that state, and had the sense that I was creating something _absolutely_ brilliant, genius even. Only to find that when I finally came down from my dissociative "high" that I had pretty much written the wrong thing entirely. Completely missed the point. I got so caught up in my own head that there was no room for feedback from the outside _at all_.

Several times a year I teach a two-week intensive course for new programmers at a large company. I love it. These are people that are new to the company I am contracted to teach for, and relatively new to the profession. They often have Computer Science degrees, or similar educational backgrounds, and it is an opportunity for me to show them some things that will help when writing code in the "real world".

We spend the entire first week doing Test Driven Development. I love TDD. I don't do it _all_ the time, and I don't prescribe it for all situations. The reason I have new developers do it is to break their sense of _hyper-focus_ and _disassociation_. "You have one minute to make the test you just wrote pass. If it takes you longer than one minute, delete your code and the test, and write another one you can get done in one minute" is one technique I use. It aggravates _the hell_ out of them. And that is the point. At least they are feeling _something_. They are forced to stop, check in, and think about what they are actually doing. It forces them to write _simple_ things. If the _simple_ thing they are writing needs something less simple, they are trained to "push it off", make it a responsibility of some _other_ code, and worry about that later.

Sort of like my new shiny Angular application needs an API, so I build a simple API. Implicit in that is that the API is an entirely different world of "Function and Variability"[^1] than the thing I am working on (my little Angular application). It doesn't belong "here", and by "here" I mean in my mind as I'm working on this "thing". In the API I do the simplest thing that could work. And it will work, for now.

#### The Simple Thing is the Right Thing

This shouldn't be interpreted to mean "Oh, I get it! The API is just a 'place holder'! Later on you will replace it with _real_ code that maps Entity Framework to Oracle, or uses our DB2 stored procedures!" No. Just No. What I am saying is something along the lines of: "For this application, we need a _definitive source_ of our data. That is this API. It is exactly what this application needs. And frankly, at this point, I don't even _care_ about other people's applications. I don't care that HR might need access to my Todo list data to track employee work or something. That's not my problem. Yet. And if it is going to my "my problem" in the future, that sounds complicated as heck, and I sure don't want that kind of thing in my simple, beautiful RESTy API. That would make it _complex_ as heck!

In Microservice architecture we have some guardrails that will keep us from doing the wrong thing, which is to say that will keep us from writing code that isn't _simple_. These are the "forms" of Microservice development. They are principals and behaviors we willingly adopt to keep us from accidentally getting disassociated and writing a mess. If you violate these, you will have a mess.

> Sidenote: Some things are really hard and, well, complicated. A student of mine told me how he had to go learn all about Kerberos authentication and implement it within his API. This is how we make a simple thing complex. Your job as a developer is to put aside your desire to be the hero, to be the one that will "slay the best", or even to avoid the glee of focusing in on that and feeling all proud of something you did that you can't explain to your mail carrier. It's to learn techniques to make that complicated thing simple, and never let it complex-ify your simple code again. We'll do it. It can be done. The answer is always some version of the same thing, though: Make something else responsible for that, and only that. You know, maybe a Microservice?

#### Final Woo Woo Thoughts

Here's my advice. Get emotional. Get annoyed. Get frustrated. There is a _lot_ of wisdom in those emotions. Feel them, don't disassociate, but do something about them! If you are working on something pretty simple and you start to get annoyed with it because now you are dealing with something really complicated, don't just "buckle down" and get it "done". **It. Does. Not. Belong. There**. It feels annoying and frustrating because it is "clashing" with the beautiful simple, elegant stuff you just wrote. If you are writing an Angular component and find yourself 18 tabs deep into learning about RXJS operators, push the yourself away from the desk like it is on _fire_. You are doing it wrong. Your component _needs_ something, but it doesn't need to know how that is done. That is the message. Have your component ask something else for it, define an _interface_, and finish up the component. Then go work on that thing. You will find it _much_ less frustrating when you (a) know exactly what it is you need, (b) can concentrate on _just_ that, and (c) aren't worried about protecting your code from other dummies. Just you.

## Our Guardrails

The most common, and, frankly, most boring question about microservices is the dreaded "How big?". Maybe I find it a boring question because it is so hard to answer. So, let's just bracket that question. "Bracket" in the philosophical sense. Just set it aside and see what happens through observation and following some basic forms. Making some rules for ourselves about what our services are allowed to do, what they aren't allowed to do, and I suspect you will find you end up with what will arguable be "micro", even if we can't find a way to quantify them.

### Guardrail One: Microservices are Created to Provide Value

A production team is always creating software to put in front of our customers. The longer we work on something, the bigger the risk. Are we building the right thing? This is very different than "building what they are asking for". Briefly in the history of software development there _were_ people that knew what the product should be and how it should work. In it's infancy, software development was the process of "digitizing" and "automating" existing systems. One of the first applications I wrote was to replace a dental office receptionist's paper calendar and post it notes with a database to track and schedule appointments. It was "done" when it worked as a suitable replacement for what she was already using. Hopefully more reliably and faster, but that was it.

On a bigger scale, legions of people were trained as "Systems Analysts". They knew _business_, and they knew _software_. They would figure out how to "modernize" your factory, your warehouse, your order processing. All that stuff.

I think one of the most damaging things in modern software development is the assumption that we are still living in that world. We are no longer simply coming up with faster, better ways to do what we've already done. We are creating entirely new concepts, new worlds, that have never existed before. There are no "subject matter experts". In anything but the most simple of software development tasks, nobody knows what it will end up looking like, how it will work, what is "best". And still we have overly long "planning sprints" were we get out our chrystal balls and speculate what we should do over the next 6 months. Nobody knows. They look to the "business", and those people _never_ want to admit they don't know things, so they make up some crap. And, for our part, we too easily fall into the "well, just tell me what you want, and we'll make it", which makes us perfect targets for being given a bunch of story cards that have no basis in reality, other than to be used against us when we don't deliver some imagined product on time.

Many developers (myself included) spent the interregnum between the two paradigms of software development (building computer stuff to replace analog stuff) and now building _bigger_ systems, _bigger_ frameworks to make development "easy". We pretended we knew the future, and we built big complicated things to prepare us for it. And we've left that crap for a generation of people that are building software for use cases and contexts that didn't even exist when we created these mega-frameworks. A mega framework that presupposes all content will be delivered through stateless HTML applications makes it a royal pain in the ass to build stateful applications that run on these new-fangled devices like phones and web browsers (I mean, 15 years ago, who would of thought that we'd be building applications that are written in JavaScript! A language that doesn't even know what an integer is!).

I have seen _so many_ applications that could have been written with 1/2 the code and 1/3 of the time, if they didn't have to bow down to some sacred "enterprise platform" that was designed to ensure we'd have absolute consistency across the enterprise. Unfortunately, that consistency is mostly "how we did things 10 years ago", and every new application is some form of lipstick on a pig monstrosity that tries to hide the ancient underpinnings of so many "enterprise service bus" implementations, or "universal API gateways".

Sometimes what we do is we try to "modernize" those application. Rewrite them. I talked to some people working on a rewrite of a huge call center management application recently. They told me that while doing "archeology" through the old code base, they found hundreds of business rules embedded in the code that were not documented anywhere else. You can imagine the temptation to "play it safe" and just reimplement those very same rules in the new system. I can't but help think they'll end up with an expensive version of the very thing they started with.

> _Eventually_ we will have to talk a bunch about how to do that kind of thing - how to go from "Monoliths to Microservices". I'm a big fan of Sam Newman's book "Monoliths to Microservices"[^6]. Going from a "monolith" to "microservices" is a great way to go. Even so, it doesn't need to be such a Manichean thing as "Oh, we have this 'legacy' monolith, so we _should_ move it to Microservices". I've created plenty of things that start life as a microservice, but complexity sneaks in and then they are a monolith. I've refactored monoliths to microservices to the point that the monolith disappears and nobody even realy notices. I don't think, though, that if I hadn't already created applications using a microservice architecture from the start I would have done a very good job. I think it would have been too tempting to just stay in the snug monolith world.

### Guardrail Two: Microservices must be Independently Deployable

A microservice is owned by a team, it often exposes some interface that other services can interact with. It is responsible for a particular _bounded context_[^7] within a domain.

The microservice must be *independently deployable*. That means it has to be designed in such a way that a new version of that service can be put into production at any time with no collaboration or synchronization with other teams that rely on that service.

That means, for example, a service or application that relies on *our* service could be using version 1 for one request, and version 2 for the next request. And this has to happen completely transparently to all dependent services. They cannot know.



### Guardrail Three: Failure is a Feature

## Kinds of Services

Because of inertia, when most developers think of a Microservice, they think of a service that exposes an HTTP interface. Many services *do* expose an HTTP interface, and it is a reasonable *default* choice for many situations. But there are other kinds of services that fall into the category of "microservice". 

### HTTP Services ("Web APIs")

Deploying services that expose an HTTP Interface (See RFC 2616[^8]) benefit from the advantages (and disadvantages) of the protocol that is behind the WorldWide Web.

Advantages:

- "Reach"
    - HTTP is *ubiquitous*. Just about any programming language and environment is easily able to create HTTP Requests and process HTTP Responses.
    - HTTP is usually the way applications and services *outside* our domain access functionality within our domain. For example, Apps (Angular, iOS, Android, etc.), and partner APIs.
- "Known" (to API Developers)
    - HTTP is *prescriptive*. A good HTTP-based API follows *resource oriented architecture* and the *consistent interface constraint*. 
    - This means that as API designers, we take time to convert our business functionality into a set of resources, based on the common HTTP methods (GET,POST,PUT,DELETE), to allow clients to access our data, and cause state changes within our domain. 
    - HTTP implicitly provides strong *domain separation*. The implementation of HTTP APIs is *malleable* over time.
    - HTTP is *extensible*. New resources can be added as the API grows/evolves. New Media Types can be declared.
    - Bottom Line: HTTP already takes care of a large part of the design of your API. You learn HTTP's "way" of doing things, and adapt your domain to accommodate that. 
- "Known" (to Clients)
    - When a developer wants to access your functionality and it is exposed through a well-reasoned HTTP interface, they *already know* much of what they need to know to use your API. They know they use a `GET` request to retrieve information, etc. They need to learn your metaphors (resources), and look at the representations (the data) that the API returns or requires for operations, but the basic approach is the same for all HTTP based APIs.
- Relatively Easy to Secure
    - Transport Layer Security (TLS) is easily added to HTTP-based APIs as a cross-cutting concern.
    - Authentication and Authorization is fairly standardized with things like OAUTH2, OpenID Connect (OIDC), JSON Web Tokens (JWT), etc.
    - Because of its prolific nature, organization like OWASP provide guidance on threats and mitigation techniques.
- Supports Standards-Based Caching
    - HTTP Responses *should* contain a `Cache-Control` header that the client and/or intermediaries can use to decide to retain and reuse responses.

Disadvantages:

- "Speed"
    - HTTP is an *application layer* protocol that sits on top of TCP. It is basically text messages sent back and forth between the two parties (the Origin server, and the User-Agent client).
    - TCP requires a lot of overhead to establish a connection, and in HTTP, connections are short-lived.
        - HTTP 1.1 added the concept of "Keep-Alives" for multiple requests to the same origin server.
        - HTTP/2 (and 3) extend this so multiple requests can be sent to the same origin in parallel, and the responses can be received in parallel. This makes the "chatty API" anti-pattern almost entirely obsolete.
- "Request/Response"
    - This is the biggest limitation in HTTP. Calls to an HTTP API are processed *synchronously*. That means that a request is made, the server processes the request, and the response is sent to the client. 
    - Many operations in Microservices take longer than reasonable to make a client "block" for a response.
        - My rule of thumb is anything over 100ms in my dev environment is too long.
    - Various techniques have to be employed to facilitate long-running processes, including:
        - Client Polling
        - Switching over from an HTTP connection to a TCP connection (using technologies like Websockets).
- "Client/Server"
    - This means that the client (User-Agent in HTTP parlance) must always initiate communication with the server. Servers cannot send response messages to the client without the client first making a request.
    - The makes modern interactions like notifications difficult.
    - Notifications are also useful when client applications are using *mutable shared data*. 
        - Mutable Shared Data is when representations are sent to multiple clients and each client has the authority to change that data.
- "Lack of Strong Contracts"
    - While HTTP provides the *consistent interface* constraint, the actual resources (identified by URIs) are malleable (can change over time), and the data sent to the server as part of a request (Headers, route parameters, query string parameters, and representations sent on a `POST` or `PUT` request) as well as the data returned as part of the response is largely ad-hoc.
    - The HTTP Specification deals with this (especially for the representations) by requiring the `Content-Type` header on requests or responses to refer to a named format for how the data should be interpreted and validated. [^9]
    - Modern tooling allows the generation of an OpenAPI Specification in many Web API development tools.
        - This is a blessing because it saves developers time.
        - It is dangerous because the contract can change as a result of your code changes without notification, indicating that you may "break" clients, defeating the *independently deployable* guard-rail.
- "No Standard Versioning Scheme"
    - HTTP was designed, along with the REST architectural style, to negate the need for versioning. In the problem domain where it exists, namely the World Wide Web, it has succeeded. 
    - The commonly applied approach to versioning in HTTP is to create new resources, or create new authorities (servers).
    - HTTP, without REST, is often very brittle.
    - HTTP, with REST, is technically challenging to implement on both the client and the server.

### GraphQL

GraphQL is a protocol that tunnels inside of HTTP.  It is a query language that allows clients to request, in an ad-hoc way, data in the shape, level of detail, etc. that is needed for the application. It is considered by many to be the replacement for ODATA.

It is particularly useful for shared store resources with diverse, often unknown, clients.

Advantages:

- Flexibility for the client
    - HTTP APIs often need to be changed to support various clients.
    - GraphQL allows clients to make ad-hoc queries against the data.

Disadvantages:

- Requires data to be exposed in a particular way.
- Breaks HTTP Caching
    - Cache needs to be reinvented and reimplemented on the client.
- Performance tends to be an issue on scaling services.



### Websocket Services

Websockets are a technology most commonly utilized in Apps running in web browsers, however support for them in other native client applications development environments is also supported.

A Websocket connection begins life as an HTTP connection, and then "downgrades" to a (nearly) raw TCP socket. This alleviates the constraints on TCP applied by HTTP. Namely:

Advantages:

- Connected
    - Websocket clients maintain a persistent connection to their server.
- Two-Way
    - The Request/Response constraint is dropped and servers can send messages to the client over time.
    - Data can be simultaneously sent and received across the connection.
- Nearly instantaneous
    - Websockets are used in real-time games, video streaming, and other high demand, low(er) latency settings.
- Fairly Simple
    - Client and Server libraries for Websocket communication make this kind of service fairly easy to implement.
    - .NET has the SignalR client and server libraries for implementing websockets. Other libraries exist (for NodeJS, Go, Java, etc.)

Disadvantages:

- The biggest disadvantage is the requirement for server *affinity*.
    - Clients that connect to a particular server using websockets generally have to maintain that connection with that specific instance of the service for the life of the connection.
    - This can be slightly challenging in load-balanced scenarios, and requires extra configuration of your reverse proxy or ingress.
- Websockets require an HTTPs  connection
    - Not a huge disadvantage, but something to be aware of.
- No Strong Contracts
    - While OpenAPI provides some limited support for specifying websocket communication, websocket messaging is mostly ad-hoc.

### gRPC Services

gRPC is a cross-platform protocol for service communication. It relies on HTTP/2, and is similar to websockets, but with an emphasis on performance and overcoming the limitations of HTTP.

Microsoft considers gRPC to be the replacement for WCF.

It gains a huge performance benefit because data is passed in a binary format defined by Protobuf schema, overcoming the limitations of JSON serialization/desexualization.

Advantages:

- Speed
    - Much faster in most scenarios than other approaches.
- Strong Contracts
    - Protobuf is an expressive format for designing service interfaces and messages, with built-in support for common versioning scenarios.
- Expressive call models
    - gRPC allows for the following call models:
        - Request/Response - similar to HTTP
        - Client streaming - the client streams data over time using a persistent connection to the server.
        - Server streaming - the server streams data over time using a persistent connection to the client.
        - "Unit" - bi-directional client/server streaming.

Disadvantages:

- Still RPC
    - As discussed below in [Integration Patterns](#integration-patterns), RPCs are tight-coupling.
    - They suffer from network errors (transient, and actual), and must be designed to accommodate that (retries, etc.)

### Worker or "Job" Services

Some services do not expose a public interface, but do "work" behind the scenes. These services can be triggered to do the work on a timer, or using other reactive programming techniques (subscribing to a message queue, a Kafka topic, etc.). 

Sometimes special services are created for use in Kubernetes to do jobs like initializing data and configuration needed for another service before that service is deemed "ready" for traffic.

Other services run alongside services in "pods" in Kubernetes working as sidecars, adapters, or embassadors. We will look at this patterns in the Kubernetes section.



## Integration Patterns 

In a microservice architecture, by definition, no one service is able to provide user-facing meaningful functionality all on its own. 


### Shared Database
> **Shared Database** is an *anti-pattern* in Microservice architecture. It is an almost certain violation of the "independently deployable" guard rail.

Two or more microservices may share a database, and this is often a common step on decomposing a monolithic application into Microservices. The problem with this approach, long term, is the diverging concerns of each service will most likely require schema changes in the shared database, and that invalidates the "independently deployable" guard rail.

Using a shared database that your team "owns" is less of a liability than using a database not owned by your team.

When using relational databases the challenges become related to database optimization. If your microservices are focused on a single responsibility, they tend to be largely "write" intensive, or "read (query)" intensive, and it is difficult to adequately index and optimize a database for both at the same time.

When Microservices are each making changes to the database, you may have the need to transactions that have to be distributed. Distributed transactions aren't just an anti-pattern, they just don't work reliably and can cause huge problems in service availability.

**Shared databases are technical debt**. They may - and often are - worth it in the short term, but should be factored to other techniques in the long term.

Note: When I say "shared databases require coordination between service owners" that means you *MUST* provide that coordination. That means someone has to "own" the database and coordinate any schema changes, etc.

Shared Databases are such a bulwark in software architecture that it seems impossible to move away from them. We will look at some patterns and ways of thinking about software architecture later that will give you alternatives.



### Remote Procedure Calls

A remote procedure call is when, in your code you call a method that *could* reasonably be on some code in the same process as your code, but is actually running in another process, across some sort of boundary (network, usually).

For example, in the following snippet:

```csharp
var balance = await _account.GetBalance();
// do something with the balance
```

The implementation of the `GetBalance()` method could be some code running locally, or it could be a proxy around a network call of some sort.

👍 Advantages:

- Easy to reason about.
    - Because RPCs look like local procedure calls, they are familiar.
    - Usually are "request/response", and the response can be used immediately.
    - Can be run in parallel if making several related calls.
- Software tooling can make this almost *too* easy.
    - Clients for HTTP Services and gRPC services can be *generated* based on schema or specification.

⚠️Disadvantages :

- Super tight coupling
    - RPCs assume the continuous presence and reliability of the remote service and the intervening network.
    - Contract changes on the remote service can break the RPC
- Performance
    - Particularly for Request/Response RPCs performance is uneven.
    - Degraded performance in a service you rely upon impacts the performance of your service.
- Transient Network Errors
    - RPC calls must have policies in place to accommodate transient network issues, including service upgrades/restarts.
- Failure
    - Outright failures of the remote service must be anticipated. A remote service failing is not sufficient reason for your service to fail.
        - This can be not only a reliability issue, but a security issue. Poorly designed service interactions can cause unintentional (or malicious) "denial of service" like failures in a microservice architecture.
- "Shotgun" Changes:
    - The introduction of a new service in the solution often requires multiple new versions of services to call that new service. 
### Messaging 📨

This is a broad heading for any asynchronous processing that occurs when one service starts work and leaves information where another process (or thread) can pick up and continue the work.

Messages are sent to a durable store of some type by service "a", and received from the store by service "b".

While databases *can* work as a sort of "poor person's messaging system", this introduces a shared database problem, amongst others. Special built software is widely available for this sort of interaction (NServiceBus, RabbitMQ, MSMQ, MQ-Series, Kafka).

It is hard to talk about the advantages and disadvantages of messaging solutions without talking about specific implementations, but generally:

Advantages:

- Loose Coupling
    - A service dropping a message in the Queue (or publishing to a topic) is not directly tied to, or necessarily knowledgeable of, the service (or services) that will process the result.
    - This tends to smooth out performance differences between services.
    - Reliability
        - Techniques like sharding and other HA techniques allow messages to be distributed for performance and reliability
            - databases do not typically allow for many writers and require techniques to replicate data.

Disadvantages:

- Additional complexity / software
- Need strong schema support.



[^1]:
    Complexity Controlled by Hierarchical Ordering of Function and Variablity
    Dijkstra, Edsger W. "Complexity controlled by hierarchical ordering of function and variability." Software Engineering: Report on a conference sponsored by the NATO Science Committee. 1968.

[^2]:
    Simple Made Easy
    Hicky, Rich "Simple Made Easy"
    [InfoQ](https://www.infoq.com/presentations/Simple-Made-Easy/)

[^3]: "Woo Woo" here meaning this is sort of opinion mixed with pop psychology as a way to explain a phenomena.

[^4]:
    Psychological Disassociation
    [Wikipedia](<https://en.wikipedia.org/wiki/Dissociation_(psychology)>)

[^5]:
    Flow - Csíkszentmihályi
    [Wikipedia](<https://en.wikipedia.org/wiki/Flow_(psychology)>)

[^6]: [Monoliths to Microservices](https://www.amazon.com/Monolith-Microservices-Evolutionary-Patterns-Transform-ebook/dp/B081TKSSNN/ref=sr_1_1?keywords=monolith+to+microservices&qid=1639860376&sprefix=monolith+to+%2Caps%2C91&sr=8-1)

[^7]: [Bounded Contexts and Microservices](https://docs.microsoft.com/en-us/azure/architecture/microservices/model/domain-analysis#:~:text=A%20bounded%20context%20is%20simply,necessarily%20isolated%20from%20one%20another.)

[^8]: [RFC 2616 - HTTP](https://datatracker.ietf.org/doc/html/rfc2616)

[^9]: [IANA Media Types](https://www.iana.org/assignments/media-types/media-types.xhtml)

[^10]: [OpenAPI](https://www.openapis.org/)
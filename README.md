# GraphQL Protocols

> WARNING: We're working on this 💪. For discussion purposes only at this moment.

[Read the introduction post here](http://emilebosch.com/2019/06/27/why-we-need-graphql-protocols/)

The simplest way to describe protocols for peer-to-peer and client to server communication via GraphQL interfaces, built for the [social](https://github.com/graphql-protocols/social) protocol family. 

🚀 Check out a [sinatra sample](https://github.com/graphql-protocols/graphql-protocols-sinatra-sample) if you're in a hurry.

![GraphiQL](https://raw.githubusercontent.com/graphql-protocols/graphql-protocols-sinatra-sample/master/images/graphiql.jpg)

## What is this?

Basically, a repository of common GraphQL service interfaces for everyone to implement. Think 'services' as WSDL that defines how servers or servers-to-client can communicate to each other via a common GraphQL interface. The core idea is that a server can simply request what protocols (interfaces) a GraphQL server implements and can start communicating with that server if it supports that queried protocol.

## What does this solve?

It solves the rules of engagement and lack of standardization on how server-to-server or clients should talk to eachother and therefore enhancing interoperability for the open web. It makes data and operations on it _interchangable_.

An example; there are 20 domain name registrars where you can buy domain names and automating registering domains would require implementing 20 specific APIs. What if there would be _just_ one simple interface that allows you to talk with all these registars? 

With the help of GraphQL protocols, one could define a single interface that all registrars would need to implement. This way you could build a registration client allowing to register at those vendors easily. That's the aim of GraphQL protocols. Making it easy to build interfaces for distributed services.

The same goes for an _notifcation_ service. You define the protocol and the resulting notification implementation can differ from Slack to email as long as you adhere to the GraphQL SDL protocol, the resulting consumers won't break either.

Some other usecases where GraphQL protocols would shine:

- A federation protocol for peer-to-peer or federated applications, think of federation applications such as Mastodon or Pixelfed.
- Peer to peer communication between servers.
- Interoperability between vendors, having a GraphQL protocol for spawning machines at Amazon the same way as at Digital Ocean.

## Why GraphQL for protocols

GraphQL is a simple concept for humans and machines. It is elegant and the SDL is descriptive, inuitive, shareable, and easy to  and implement.

- Simple introspection of what interfaces/protocols an endpoint implements by just viewing the query or mutation type.
- Makes humans and machines both happy ❤️
- Composable. Simple to compose and extend.
- A dead simple principle, if you know GraphQL you know it already .

And of course the benefits of GraphQL itself, such as:
- Self-documenting
- Strongly typed
- Supports deprecation and versioning
- The whole GraphQL ecosystem such as client description.

## Current draft protocols

All of the protocols are currently in DRAFT state and used as input for discussion.

- [Request Tokens](draft/token-request.md) - A self-service way of retrieving an API token from a service for communication. With this protocol you can identify yourself to the service and start using other protocols (APIs) on that service if they require authentication.
- [Postbox](draft/postbox.md) - Send messages to a service / site

## How does it work technically?

The protocols are just _Plain Old GraphQL Interfaces_ written in [SDL](https://graphql.org/learn/schema/). Servers and clients need to implement these interfaces and types to talk with each other. GraphQL protocols are a _best practice pattern_ rather than an actual technical solution.

Usually, such a protocol exists out of a couple of parts defined in the GraphQL schema definition. For example, let's say you have a bunch of servers that send messages to each other. By connecting to a server that supports this protocol, you can easily send a message to the owner of that protocol.

The types you need for your service (You will share this file with your implementers):

```graphql

# The type you need for your service
type Message {
  message: String
}

# The query interface
interface Postbox {
  messages: [Message]
}

# The mutation interface
interface PostboxMutatations {
  postMessage(message: String): Message
}
```

Then you need to hook them up to your query type.

Add to your query type:

```graphql
type Query implements Postbox
```

Add to your mutation types:

```graphql
type Mutation implements PostboxMutatations
```

And that's it. The idea is that the protocol part is _reusable_. And that other implementers only need to implement your interface SDL for clients to talk to.

## Protocol writing best practices

This will help you building protocols that are easy to maintain.

### Keep protocols small

Keep your protocols as easy and small as possible. The chances that complicated protocols will get implemented are quite small. The easier to implement, the higher chances of adoption.

### Write it for humans

- Make it conversational in terms of naming for mutations and fields
- Make it easy to implement with just GraphiQL. If its painful to use the protocol in GraphiQL it will be hard to implement too.
- Keep it intuitive in terms of naming and flow
- Communicate as if you're communicating to a human

### Adhere to GraphQL conventions

- Things under `viewer` should be tied to the `viewer` (i.e current authenticated user)
- Mutations under mutations field
- Things that are public available for al consumers outside the scope of `viewer`

### Make it resilient

- Think that your service might not be available so make things nullable that could be nullable in an outage.

## Want to make your protocol?

Fork this repo, use the [template](template.md) and raise a PR.

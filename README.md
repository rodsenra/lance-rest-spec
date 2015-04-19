# Lance-REST Specification

## A super-lean hypermedia and metamodel API spec

* __Authors:__  [Tiago Luchini][1] <[luchini@work.co](mailto:luchini@work.co)>
* __Version:__  0.1

## Background & Targets
Designing good REST APIs is an art in itself but it's a process that should
not necessarily be a mystery. Exceptionally good REST APIs offer a consistent language
with designs that enable exploration, documentation and discoverability from within
the API itself.

Lance-REST is a simple format that provides this consistency. It prescribes an easy
way to identify resources, describe endpoints and explicitely expose the relationships
between resources.

APIs that adopt Lance-REST are considered Maturity Level 3 APIs according to
[Richardson's Maturity Model][2].

## General Description
Lance-REST provides a set of conventions for expressing hyperlinks and relationships
between resources and the meta-descriptions for these.

**The rest of a Lance-REST document is just plain old JSON.**

Instead of using ad-hoc structures, or spending valuable time designing
your own format; you can adopt Lance-REST's conventions and focus on building
and documenting the data and transitions that make up your API.

Lance-REST is machine-friendly and human-friendly convention that allows
your API to be self-discoverable, self-documented and self-exploratory by
both, computers and humans.

## Key Concepts

### 1) Resource-driven

Endpoints should always refer to specific resources. A call to `POST` a `PurchaseOrder`
is resource-driven (as opposed to a call to `ExecutePurchase` - which would be process-driven).
Lance-REST takes the resource-driven approach.

### 2) Meta-descriptors

Each resource, its relationships, composition and the API's structure itself are all described
by meta-descriptors added to the Lance-REST document. This minute overhead allows machines
and humans to parse the API intelligently.

### 3) Simplicity

Lance-REST is built upon a simple JSON document. Therefore, adopting it is as simple as
adopting the conventions described here. No extra overhead is required.

## Examples

### Single Contact

The example below is how you might represent a simple resource named `contact` with
Lance-REST. Things to look for:

* Certain attributes starting with `_` (underscore)
* The non `_` attributes (`name` and `email`) are attributes of the contact
* The URI of the main resource being represented (`/contact/88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1`) 
  expressed with a `self` link
* A class definition that describes this resource as a `Contact` (at `_meta/class`)
* Think of this representation as a simple JSON describing a contact named `Miles Davis` with
  email `miles.davis@gmail.com`

### application/lance+json document
```javascript
{
  "_links": {
    "self": { "href": "/contact/88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1" }
  },
  "_meta": {
    "class": "Contact"
  },
  "_id": "88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1",
  "name": "Miles Davis",
  "email": "miles.davis@gmail.com"
}
```

### Collection of Contacts

The example below is how you might represent a simple collection of contacts with
Lance-REST. Things to look for:

* Similarly to a single contact, certain attributes also start with `_` (underscore)
* The URI of the collection being represented (`/contacts`) expressed with a `self` link
* The URI for the next page (`/contacts?page=2`) expressed with a `next` link
* A class definition that describes this resource as a `ContactCollection` (at `_meta/class`)
* An indicator of the node where the collection resides (`contactList`)
* Each entity of the collection representing its own resource according to Lance-REST conventions
* Extended meta-descriptors that describe the collection in more detail (`totalCount`, `currentPage` 
  and `pageCount`)
* Think of this representation as a collection of contacts (specifically page 1 of a collection
  containing a total of 6 contacts). We also see the two contacts on this page and know how to
  fetch more contacts as well as more details of each contact if needed.

### application/lance+json document
```javascript
{
  "_links": {
    "self": { "href": "/contacts" },
    "next": { "href": "/contacts?page=2" }
  },
  "_meta": {
    "class": "ContactCollection",
    "collectionNode": "contactList",
    "totalCount": 6,
    "currentPage": 1,
    "pageCount": 3
  },
  "contactList": [
    {
      "_links": {
        "self": { "href": "/contact/88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1" }
      },
      "_meta": {
        "class": "Contact"
      },
      "_id": "88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1",
      "name": "Miles Davis",
      "email": "miles.davis@gmail.com"
    },
    {
      "_links": {
        "self": { "href": "/contact/fdb7a214-e3c4-11e4-8a00-1681e6b88ec1" }
      },
      "_meta": {
        "class": "Contact"
      },
      "_id": "fdb7a214-e3c4-11e4-8a00-1681e6b88ec1",
      "name": "John Coltrane",
      "email": "john.coltrane@gmail.com"
    }
  ]
}
```

### Relationships (advanced example)

The example below is how you might represent an advanced product model with
Lance-REST. The product in question has embedded models in itself (the product category and
a collection of tags). Things to look for:

* Similarly to a single contact (example above), certain attributes start with `_` (underscore)
* The URI of the product being represented (`/product/5abb57d4-e3c6-11e4-8a00-1681e6b88ec1`)
* The URI of the product category being represented (`/category/ee8263cc-e3c6-11e4-8a00-1681e6b88ec1`)
* The URI of the product's collection of tags (`/product/5abb57d4-e3c6-11e4-8a00-1681e6b88ec1/tags`)
* A class definitions describing each resource `Product`, `ProductCategory`, `ProductTagCollection`, and `ProductTag` (at `_meta/class`)
* An indicator of the node where the tag collection resides (`tagList`)
* Each entity of the collection representing its own resource according to Lance-REST conventions

### application/lance+json document
```javascript
{
  "_links": {
    "self": { "href": "/product/5abb57d4-e3c6-11e4-8a00-1681e6b88ec1" }
  },
  "_meta": {
    "class": "Product"
  },
  "_id": "5abb57d4-e3c6-11e4-8a00-1681e6b88ec1",
  "name": "Super stain remover",
  "price": 9.99,
  "category": {
    "_links": {
      "self": { "href": "/category/ee8263cc-e3c6-11e4-8a00-1681e6b88ec1" }
    },
    "_meta": {
      "class": "ProductCategory"
    },
    "_id": "ee8263cc-e3c6-11e4-8a00-1681e6b88ec1",
    "name": "Cleaning Products"
  },
  "tags": {
    "_links": {
      "self": { "href": "/product/5abb57d4-e3c6-11e4-8a00-1681e6b88ec1/tags" }
    },
    "_meta": {
      "class": "ProductTagCollection",
      "collectionNode": "tagList"
    },
    "tagList": [
      {
        "_meta": {
          "class": "ProductTag",
        },
        "name": "Novelty"
      },
      {
        "_meta": {
          "class": "ProductTag",
        },
        "name": "Cleaning"
      }
    ]
  }
```

## Convention 1: Root URL and API Metadata (mandatory)

In order to provide a full self-discoverable experience for clients, Lance-REST relies on an API
entry point called Root URL. This Root URL yields a Lance-REST document with metadata about the
API per se.

This endpoint MUST be the root (`/`) of your API. The following example shows an
API with two available endpoints:

```javascript
{
  "_links": {
    "self": { "href": "/" },
    "contacts": { "href": "/contacts" },
    "contact": { "href": "/contact/{uuid}" }
  },
  "_meta": {
    "class": "Metadata"
  },
  "version": "1.0",
  "serverName": "Snakebar"
}
```

Things to obseve:

* This is a simple Lance-REST document. Therefore, every Lance-REST convention applies
* We've used an optional `class` called `Metadata` to make clients easier to implement
* A `contacts` link points to `/contacts` so clients know to go there in order to fetch contacts
* In order to get one specific contact, clients must check the `contact` link template (`/contact/{uuid}`)
  and fill the `uuid` parameter accordingly (see [URI Templating](#uri-templating) below)
* The payload also carries other relevant data about the server (`version` and `serverName`). The
  API designer is free to carry any relevant payload

*The Root URL is mandatory for Lance-REST*

*A parseable collection of links documenting each endpoint of the API is also mandatory*

In other words, your Lance-REST *MUST* respond on its Root URL with a Lance-REST document containing
all API endpoints.

In Lance-REST, every endpoint must support the `GET` method. Additionally, endpoints can support
also `POST`, `PUT`, and `DELETE`. Read more about how Lance-REST clients and servers should deal
with these methods on [Convention 5](#convention-5-_post_-put-and-delete-responses).

### URI Templating

The templating used on the metadata response follow the standard below:

1) `{parameter}`: represents a mandatory placeholder named `parameter`. Clients MUST replace this
   placeholder when composing a full URL
2) `{?parameter}`: represents an optional placeholder named `parameter`. Clients MAY replace this
   placeholder with a parameter, but, if the parameter is not available, the client MUST remove the
   placeholder altogether.

The following template has one mandatory (`type`) and one optional (`pageSize`) parameters:

`/orders?type={type}&pageSize={?pageSize}`

Clients MUST replace `{type}` when composing this URL. Therefore, if a client wants only `open`
orders and does not care about the optional `pageSize`, the following URL would be composed:

`/orders?type=open&pageSize=`

In a subsquent call (or a different client) may want to fetch `processed` orders and have 25 orders
per page:

`/orders?type=closed&pageSize=25`

Of course, API designers can play around any URL formating strategy and design they see fit. Lance-REST
servers and clients simply follow this simple placeholder replacement logic.


## Convention 2: Underscore and Non-Underscore Attributes

Lance-REST uses simple JSON documents. It is recommended that the `Content-Type` and `Accept` headers
used point to `application/lance+json` for clarity sake.

The payload itself is as simple as an "empowered" JSON model. Take for instance the contact model
example above. In practice, to represent a simple contact we would have a model like this:

```javascript
{
  "name": "Miles Davis",
  "email": "miles.davis@gmail.com"
}
```

Lance-REST extends on this simple JSON by differentiating non-underscore attributes
(`name` and `email` above) with internal, meta-controlled underscore attibutes. See below:

```javascript
{
  "_links": {
    "self": { "href": "/contact/88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1" }
  },
  "_meta": {
    "class": "Contact"
  },
  "_id": "88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1",
  "name": "Miles Davis",
  "email": "miles.davis@gmail.com"
}
```

On the example above, the `_links` and `_meta` nodes are Lance-REST's conventions that describe
this resource as a `Contact` and tell any prospective client that, if the client wants to fetch
more details about this specific contact, a request to `/contact/88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1`
should be made. (See [Convention 2](#convention-2-_links-node-optional) and
[Convention 3](#convention-3-_meta-node-optional)
for more details).

You can also notice that we added an `_id` node to the representation of this contact. This is not
a Lance-REST specific need but it is here to represent the possibilities. Any underscore attribute
(such as `_id`) would be considered part of the meta representation of this instance. Refer to
[Convention 5](#convention-5-post-and-put-responses) for more information about the implications
of such architecture.

## Convention 3: _meta node (optional)

The `_meta` node is where API designers can add payload the describe the resource being represented
in detail.

### The _meta/class node (recommended)

Despite not being mandatory, the `_meta/class` node is highly recommended as it allows clients to
dynamically react based on the content type of the resource being described.

Take our traditional contact example. The following would be a totally valid Lance-REST document:

```javascript
{
  "name": "Miles Davis",
  "email": "miles.davis@gmail.com"
}
```

However, clients (humans or machines) are not able to easily identify which kind of resource this
instance refers to. It could be a person, a contact, or even a robot :)

Therefore, the following representation instructs clients/readers that the entity is, indeed,
a `Contact`:

```javascript
{
  "_meta": {
    "class": "Contact"
  },
  "name": "Miles Davis",
  "email": "miles.davis@gmail.com"
}
```

Within the design of your API, classes should be used to identify unique resource types. In practice,
this means that each class name is unique.

### The _meta/collectionNode node (mandatory for collections)

In Lance-REST, collections receive special treatement. A Lance-REST enabled collection has the
same meta-descriptor wrappers as any other object. In addition, it also has a `_meta/collectionNode`
entry that instructs clients to the location of the collection proper.

Suppose you want to model a collection of contacts, you could go about doing the following:

```javascript
{
  "_links": {
    "self": { "href": "/contacts" }
  },
  "_meta": {
    "class": "ContactCollection",
    "collectionNode": "contactList"
  },
  "contactList": [
    {
      "_links": {
        "self": { "href": "/contact/88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1" }
      },
      "_meta": {
        "class": "Contact"
      },
      "_id": "88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1",
      "name": "Miles Davis",
      "email": "miles.davis@gmail.com"
    },
    {
      "_links": {
        "self": { "href": "/contact/fdb7a214-e3c4-11e4-8a00-1681e6b88ec1" }
      },
      "_meta": {
        "class": "Contact"
      },
      "_id": "fdb7a214-e3c4-11e4-8a00-1681e6b88ec1",
      "name": "John Coltrane",
      "email": "john.coltrane@gmail.com"
    }
  ]
}
```

Some observations about this design:

* Despite being optional, a `class` has been defined. This helps clients wrap this response in a
  model if they want to.
* `collectionNode` points to node `contactList` and instructs clients to find the collection items
  over there.
* This collection is not paginated (there's no `next` or `prev` nodes within `_links` as well as no
  `currentPage`, `totalCount` and `pageCount` nodes in `_meta` - see below).

### The _meta/currentPage, _meta/totalCount and _meta/pageCount nodes (mandatory for paged collections)

As described above about `_links/prev` and `_links/next`, collections can be paginated.
Lance-REST takes an opinionated approach to pagination. In order to expose the pagination details
to Lance-REST clients, the following nodes are *mandatory* if either `_links/prev` or `_links/next`
are present:

1) `currentPage`: the number of the page the document refers to (1-based - meaning 1st page is 1)
2) `totalCount`: the total number of entries this collection has
3) `pageCount`: the total number of pages this collection has

The following example is a paged collection of contacts with a total of 6 contacts, this document
shows page 1 of this collection and there are 3 pages in total:

```javascript
{
  "_links": {
    "self": { "href": "/contacts" },
    "next": { "href": "/contacts?page=2" }
  },
  "_meta": {
    "class": "ContactCollection",
    "collectionNode": "contactList",
    "totalCount": 6,
    "currentPage": 1,
    "pageCount": 3
  },
  "contactList": [
    {
      "_links": {
        "self": { "href": "/contact/88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1" }
      },
      "_meta": {
        "class": "Contact"
      },
      "_id": "88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1",
      "name": "Miles Davis",
      "email": "miles.davis@gmail.com"
    },
    {
      "_links": {
        "self": { "href": "/contact/fdb7a214-e3c4-11e4-8a00-1681e6b88ec1" }
      },
      "_meta": {
        "class": "Contact"
      },
      "_id": "fdb7a214-e3c4-11e4-8a00-1681e6b88ec1",
      "name": "John Coltrane",
      "email": "john.coltrane@gmail.com"
    }
  ]
}
```

### Other _meta nodes

Other nodes can be added to `_meta` but they are not needed for Lance-REST purposes. Uses of these
are normally related to extra information about the resource at hand that may be relevante to the
client but is not usually created or updated by the client.

The follow example shows a collection of contacts where the API designer decided to tell the clients
that some extra meta data about the collection per se (`ordersProcessed`, `ordersUnderReview` and
`ordersPending`):

```javascript
{
  "_links": {
    "self": { "href": "/orders" },
    "next": { "href": "/orders?page=2" }
  },
  "_meta": {
    "class": "OrderCollection",
    "collectionNode": "orderList",
    "totalCount": 26,
    "currentPage": 1,
    "pageCount": 9,
    "ordersProcessed": 13,
    "ordersUnderReview": 5,
    "ordersPending": 8
  },
  "orderList": [
    {
      "_links": {
        "self": { "href": "/order/88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1" }
      },
      "_meta": {
        "class": "Order"
      },
      "_id": "88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1",
      "total": 452.12
    },
    {
      "_links": {
        "self": { "href": "/order/fdb7a214-e3c4-11e4-8a00-1681e6b88ec1" }
      },
      "_meta": {
        "class": "Order"
      },
      "_id": "fdb7a214-e3c4-11e4-8a00-1681e6b88ec1",
      "total": 645.55
    },
    {
      "_links": {
        "self": { "href": "/order/618f076e-e488-11e4-8a00-1681e6b88ec1" }
      },
      "_meta": {
        "class": "Order"
      },
      "_id": "618f076e-e488-11e4-8a00-1681e6b88ec1",
      "total": 321.23
    }
  ]
}
```

## Convention 4: _links node (optional)

A `_links` node represents all or some of the relationships this specific resource has with other
entities. The absence of a `_links` node would indicate to clients that there are no further
relationships that can be inferred.

Lance-REST follows a basic convention for meta-functionality but Lance-REST servers are allowed
(and should feel free to) add as many business-specifc links as needed for the purposes of the
API being designed.

### The _links/self node (optional)

The `self` node points to the very URI where clients can fetch a more complete representation of
the resource in question. I.e. let's assume the client has already fetched a simple instance of
a contact such as the one below. This may have been a simple entry on a larger collection or
an embedded reference on another larger resource.

```javascript
{
  "_links": {
    "self": { "href": "/contact/88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1" }
  },
  "_meta": {
    "class": "Contact"
  },
  "_id": "88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1",
  "name": "Miles Davis",
  "email": "miles.davis@gmail.com"
}
```

Now let's suppose the client wants to know more about Miles Davis. Since the `_links/self/href` is
available, the client will follow it and the following representation could be used to represent
the "fuller", more complete version of this entity:

```javascript
{
  "_links": {
    "self": { "href": "/contact/88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1" }
  },
  "_meta": {
    "class": "Contact"
  },
  "_id": "88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1",
  "name": "Miles Davis",
  "email": "miles.davis@gmail.com",
  "phone": "+1 555 4321 6677",
  "country": {
    "_meta": {
      "class": "Country"
    }
    "code": "US",
    "name": "United States of America"
  },
  "friends": {
    "meta": {
      "class": "ContactCollection",
      "collectionNode": "friendList"
    },
    "friendList": [
      {
        "_links": {
          "self": { "href": "/contact/fdb7a214-e3c4-11e4-8a00-1681e6b88ec1" }
        },
        "_meta": {
          "class": "Contact"
        },
        "_id": "fdb7a214-e3c4-11e4-8a00-1681e6b88ec1",
        "name": "John Coltrane",
        "email": "john.coltrane@gmail.com"
      },
      {
        "_links": {
          "self": { "href": "/contact/f75c07d6-e449-11e4-8a00-1681e6b88ec1" }
        },
        "_meta": {
          "class": "Contact"
        },
        "_id": "f75c07d6-e449-11e4-8a00-1681e6b88ec1",
        "name": "Dizzy Gillespie",
        "email": "dizzy.gillespie@gmail.com"
      }
    ]
  }
}
```

This representation is way more complete than the simpler one the client had before. It carries one
more simple attribute (`phone`) but also a Lance-REST empowered relationship to a `Country` resource
(`Country`, as represented here, doesn't have a fuller representation of itself - no `_links/self`
node is provided) and a relationship to a list of friends (Lance-REST empowered `friends` ndode).

The `friends` node points to a collection of `Contact` entities which, in turn, could be fetched
by the client on their own respective `_links/self` URIs.

### The _links/prev and _links/next node (mandatory for paged collections)

There are two other nodes that may go into the `_links` node: `prev` and `next`. These are used
by Lance-REST clients to go through paged collections. The `_links/next` points to the URI where the
next page can be fetched and the `_links/prev` points to the URI where the previous page can be
fetched.

If present, these nodes may be used by clients wishing to get more results. If a previous page is not
available, then the `prev` node must not to be presented. Similarly, if a next page is not available
then the `next` node must not be present.

The following example shows a possible representation of a paged collection of contacts:

```javascript
{
  "_links": {
    "self": { "href": "/contacts" },
    "next": { "href": "/contacts?page=2" }
  },
  "_meta": {
    "class": "ContactCollection",
    "collectionNode": "contactList",
    "totalCount": 6,
    "currentPage": 1,
    "pageCount": 3
  },
  "contactList": [
    {
      "_links": {
        "self": { "href": "/contact/88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1" }
      },
      "_meta": {
        "class": "Contact"
      },
      "_id": "88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1",
      "name": "Miles Davis",
      "email": "miles.davis@gmail.com"
    },
    {
      "_links": {
        "self": { "href": "/contact/fdb7a214-e3c4-11e4-8a00-1681e6b88ec1" }
      },
      "_meta": {
        "class": "Contact"
      },
      "_id": "fdb7a214-e3c4-11e4-8a00-1681e6b88ec1",
      "name": "John Coltrane",
      "email": "john.coltrane@gmail.com"
    }
  ]
}
```

Please note that there are some other conventions for collections (such as `collectionNode`,
`totalCount`, `currentPage` and `pageCount`). These are explained in more details on
[Convention 3](#convention-3-_meta-node-optional). The `next` link on the above example points
to `/contacts?page=2` where the second page for this collection (of 3 pages, as one can see)
can be fetched.

### Other _links nodes

Other nodes within `_links` are to be used for named relationships of the entity in question. Let's
return to our contact example. This time around the API design is such that the contact's friends
is linked on a name connection (`friends`) for the contact being represented. A Lance-REST enabled
client must implement a support to fetch the URL specified by the `_links/friends/href`. The
key difference between this design and the one we saw before is that the friends' collection is
linked to the current contact; the previous example had the friends' collection embedded in
the current contact.

By enabling this kind of design flexibility, Lance-REST servers and clients can self-negotiate the
best strategy to consume the entities and their relationships.

```javascript
{
  "_links": {
    "self": { "href": "/contact/88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1" },
    "friends" { "href": "/contact/88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1/friends" }
  },
  "_meta": {
    "class": "Contact"
  },
  "_id": "88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1",
  "name": "Miles Davis",
  "email": "miles.davis@gmail.com"
}
```


## Convention 5: POST, PUT and DELETE responses

In Lance-REST, every endpoint must support the `GET` method. Lance-REST clients are expected to
alwyas use `GET` when fetching resources.

Clients are also expected to have facilities for creating, updating and deleting resources.

### Update logic (PUT)

The following logic is used by Lance-REST clients when updating a resource:

1) The client must first `GET` the resource's instance and therefore discover its `self` link.
2) A resource without a `self` link cannot be updated
3) With the `self` link in hands, the client MAY then send the updated Lance-REST document back to the
   `self` link using the `PUT` method
4) If the transaction was successful, the server MUST respond with a `200` and the updated
   representation of the resource in Lance-REST format
5) If the transaction was not successful, the server MUST respond with the proper HTTP status code
6) The three traditional and most common responses for failed `PUT` requests are `406 Not Acceptable`
   (something was not acceptable on the request - i.e. some data is invalid), `409 Conflict` (there
   is a conflict on the server reguarding this resource - i.e. someone else updated it before) and
   `410 Gone` (the representation of this resource does not exist on the server anymore)
7) The failed requests should also carry the latest representation of the resource on their bodies
   in Lance-REST format

Lance-REST enabled clients and server SHOULD use HTTP status code to convey proper status in
addition to the conventions listed above.

### Deleting logic (DELETE)

The following logic is used by Lance-REST clients when deleting a resource:

1) The client must first `GET` the resource's instance and therefore discover its `self` link.
2) A resource without a `self` link cannot be deleted
3) With the `self` link in hands, the client MAY then send a `DELETE` request to the  `self` link
4) If the transaction was successful, the server MUST respond with a `200`
5) If the transaction was not successful, the server MUST respond with the proper HTTP status code
6) The two traditional and most common responses for failed `DELETE` requests are `409 Conflict` (there
   is a conflict on the server reguarding this resource - i.e. someone else updated it before) and
   `410 Gone` (the representation of this resource does not exist on the server anymore)

Lance-REST enabled clients and server SHOULD use HTTP status code to convey proper status in
addition to the conventions listed above.

### Creating logic (POST)

Clients may try to `POST` on any of the available link offered by the Root URL API. However, it's
recommended that only links related to collections support this method for clarity sake.

1) The client must first be aware of the links available by the API metadata Root URL (i.e. a
   `contacts` link that points to `/contacts`)
2) The client MAY then send a completely new resource in Lance-REST document back to the
   `contacts` link using the `POST` method
3) If the transaction was successful, the server MUST respond with a `201 Created` and the created
   representation of the resource in Lance-REST format
4) If the transaction was not successful, the server MUST respond with the proper HTTP status code
5) The most common responses for failed `POST` requests is `406 Not Acceptable`
   (something was not acceptable on the request - i.e. some data is invalid)
6) A failed `POST` MAY not carry the representation of the resource on its body

Lance-REST enabled clients and server SHOULD use HTTP status code to convey proper status in
addition to the conventions listed above.

### Negotiating  methods between server and client

Lance-REST expects HTTP status codes to be used according to traditional HTTP practices. If a certain
method is not supported, both server and client should understand that the status code
`501 Not Implemented` indicates that such a method is not available.

Therefore, if a client tries to update a certaing resource by doing a `PUT` on the resource's `self`
link and the server replies with `501 Not Implemented`, then the server does not support updates
for that resource.

### Documents for POST and PUT

Lance-REST clients MAY remove or keep underscore attributes from documents payloads but servers
MUST ignore.

Therefore, when creating a contact, the following transaction would be expected:

Body of a `POST` to `/contacts` (assuming that the root URL yielded this link as a possibility):

```javascript
{
  "name": "Miles Davis",
  "email": "miles.davis@gmail.com"
}
```

Response is a `201 Created` with the body:

```javascript
{
  "_links": {
    "self": { "href": "/contact/88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1" }
  },
  "_meta": {
    "class": "Contact"
  },
  "_id": "88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1",
  "name": "Miles Davis",
  "email": "miles.davis@gmail.com"
}
```

The server would have ignored any underscore attribute if the client had sent them.

A `PUT` flow is similar but clients MAY or MAY NOT keep underscore attributes from previous `GET`
on the resource proper.

Therefore, when updating a contact, the following transaction would be expected:

Body of a `PUT` to `/contact/88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1` (assuming that the `self` link
of this entity pointed to such URL):

```javascript
{
  "_links": {
    "self": { "href": "/contact/88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1" }
  },
  "_meta": {
    "class": "Contact"
  },
  "_id": "88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1",
  "name": "Mr. Miles Davis",
  "email": "miles.davis@gmail.com"
}
```

Response is a `200 Ok` with the body:

```javascript
{
  "_links": {
    "self": { "href": "/contact/88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1" }
  },
  "_meta": {
    "class": "Contact"
  },
  "_id": "88b4ddfe-e3c1-11e4-8a00-1681e6b88ec1",
  "name": "Mr. Miles Davis",
  "email": "miles.davis@gmail.com"
}
```

The server ignored all underscore attributes but still proceeded with the change (`Mr. Miles Davis`).


 [1]: http://work.co/
 [2]: http://martinfowler.com/articles/richardsonMaturityModel.html
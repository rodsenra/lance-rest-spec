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

## Convention 1: Underscore and Non-Underscore Attributes

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

## Convention 2: _links node (optional)

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

## Convention 3: _meta node (optional)

### The _meta/class node (recommended)

### The _meta/collectionNode node (mandatory for collections)

### The _meta/currentPage, _meta/totalCount and _meta/pageCount nodes (mandatory for paged collections)

### Other _meta nodes

## Convention 4: Root URL and API Metadata

## Convention 5: POST and PUT responses

## Templating the URIs


 [1]: http://work.co/
 [2]: http://martinfowler.com/articles/richardsonMaturityModel.html
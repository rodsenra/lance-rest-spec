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

* Equally to a single contact, certain attributes also start with `_` (underscore)
* The URI of the collection being represented (`/contacts`) expressed with a `self` link
* The URI for the next page (`/contacts?page=2`) expressed with a `next` link
* A class definition that describes this resource as a `ContactCollection` (at `_meta/class`)
* An indicator of the node where the collection resides (`contactList`)
* Each entity of the collection representing its own resource according to Lance-REST conventions
* Extended meta-descriptors that describe the collection in more detail (`totalCount`, `currentPage` 
  and `pageCount`)

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

The example below is how you might represent a simple collection of contacts with
Lance-REST. Things to look for:

TODO: this section
* Equally to a single contact, certain attributes also start with `_` (underscore)
* The URI of the collection being represented (`/contacts`) expressed with a `self` link
* The URI for the next page (`/contacts?page=2`) expressed with a `next` link
* A class definition that describes this resource as a `ContactCollection` (at `_meta/class`)
* An indicator of the node where the collection resides (`contactList`)
* Each entity of the collection representing its own resource according to Lance-REST conventions
* Extended meta-descriptors that describe the collection in more detail (`totalCount`, `currentPage` 
  and `pageCount`)

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

## The Underscore Attributes

## Convention Underscore Attributes (_links and _meta)

## Root URL

## _links/self

## _links/prev and _links/next

## _meta/class

## _meta/collectionNode

## _meta/currentPage _meta/totalCount _meta/pageCount


## Links

 [1]: http://work.co/
 [2]: http://martinfowler.com/articles/richardsonMaturityModel.html
# Introduction

# A Practical REST API Design Method

## You should think of the API as a product.

Much attention in the industry is placed on user experience.  For developers, the API experience is THEIR user experience.  You should keep the customer in mind so that an EXTERNAL developer can easily understand the API.  Prefer ordinary language as opposed to describing properties and concepts in computer science terms unless absolutely necessary.  For example, not everyone knows what idempotent means.  If you must use a term like that, try to define it in terms that can be broadly understood.

## Don't Break The Contract

You should not change your API in a willy-nilly fashion that will break your customers' integrations.  Breaking changes should be introduced only through introducing a new version of your API.  [See Versioning](#versioning)

## Where to start

Start with a REST API design language.  We recommend using Swagger (aka Open API Specification) as it has a rich ecosystem of integrations into development stacks.  You should start modeling the API without writing any code.  Share this model with your peers and if possible developers who can give you feedback on the API.  Think lean product principles where you are setting a design hypothesis and proving or disproving your design by testing it with users.

The Swagger specification is not sufficient as the ONLY form of API documentation.  You should create documentation for the things that surround your API such as getting started, authentication, edge cases, and so on.  Make it easier on your developer customer to get going.

The API definition should be version controlled, and you should expose your specification as an endpoint that a client can use to programmatically understand your API.

## Think About Your Resources

Generally speaking, a REST API should be designed around the state of the objects that you will be exposing via resources.  You should use HTTP methods (GET, POST, PUT, DELETE) to allow the developer to manipulate the state of the objects exposed.  HTTP headers should be used for passing mandatory arguments such as authentication, accepted content types, etc.  Query parameters should be used for optional arguments and can be omitted as required.  Return codes should mirror the meaning and semantics of the core HTTP specification (i.e., 1xx for informational, 2xx for success, 3xx for redirection, 4xx for cases where the client erred, and 5xx for cases where the server erred).

Resources should be represented as nouns and not verbs.  Nouns would be things like, User, Account, Message, Voicemail, File, and so on.  Verbs are things like, Action, Subscribe, Notify.  In most cases, you should be able to come up with alternative schemes to represent the meaning behind a verb into a noun.

For example:

`GET /users/:id/subscribe` could be more RESTful if you convert it to `GET /users/:id/subscriptions/:sub_id`

## How To Format The Objects You Return

You should use JSON.  

Consider a fictional quiz API.  Notice that the endpoint returns an array of `quiz` objects.  

`GET /v1/quizzes`

```JSON
[
  {
    "quiz":
      {
        "id": "1",
        "name": "Movie Names",
        "description": "A quiz about movie names",
        "created": "2016-11-23T19:47:15Z",
        "updated": "2016-11-23T19:47:15Z"
      },
    "author": {
      "id": "1",
      "name": "Ashley Roach",
      "email": "asroach@cisco.com"
    }
  },
  {
    "quiz":
      {
        "id": "2",
        "name": "Coffee Drink Quiz",
        "description": "A quiz about coffee drinks",
        "created": "2016-11-23T19:47:15Z",
        "updated": "2016-11-23T19:47:15Z"
      },
    "author": {
      "id": "1",
      "name": "Ashley Roach",
      "email": "asroach@cisco.com"
    }
  }
]
```

## Errors

As mentioned above, when errors occur, your API should respond with the correct [HTTP error code](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).  You should also return a useful message to the user.  The message returned should be formatted as JSON.

Examples:

Error code `400`

```JSON
{
  "message": "Missing a parameter."
}

Error code `500`

```JSON
{
  "message": "Failed to save quiz answer!"
}
```

# Versioning

Versions should be expressed in the URI of the the API, and should version the API of the service to allow for independent upgrading of services.

```
https://api.cisco.com/<service>/v1/<method>
```

When an API has multiple versions, we recommend two versions should be supported at the same time.

Incrementing versions should only happen when breaking changes are necessary to be introduced in the API.  Otherwise, APIs should be backwards compatible as new functionality is introduced.  This can be achieved by introducing new values as optional as opposed to required.

# How To Handle Large Results

Some endpoints, such as collections/lists/arrays, will return more results than are wise to send over the network.  In these cases, you should implement paging.

Pagination should be implemented using the RFC5988 (Web Linking) standard for pagination. When requesting a list of resources the response may contain a `Link` header containing the URLs to the first, next and previous page.

Page sizes should be requested by including the parameter `page_size`.


**Notes**

# Data

What data should be returned: For example, how a unique identifier for a given member of a collection should be expressed, support for pagination, filtering of large GETs, etc.;

# Encoding

What encoding should be supported: The majority of new web APIs tend to implement JSON only, but there are cases where APIs support alternative encoding specifications such as XML. The style guide should define what is mandatory, what alternatives can be implemented, and how to deal with the transposition between different specifications;

# Links

The use of links: Hypermedia APIs have long been considered by many in the industry as being the way to implement data linkages across multiple endpoints or APIs, with identifiers to related data items implemented as links rather than as a simple surrogate or canonical identifier. However, doing so cohesively across an organization demands a singularity of purpose that a style guide can help deliver. Moreover, there are times when including rather than linking to data for the purposes of efficiency is desirable. The style guide therefore needs to define an organizational approach to hypermedia APIs and, where they are being promoted, define practical steps for making them work.

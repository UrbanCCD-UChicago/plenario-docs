## Detailed Metadata

_Currently under development_

While the high-level metadata endpoints give you
everything you need to know to form queries and stream over Websockets,
the detailed metadata endpoints let you learn more about how an observation
was derived.
Observations that you receive from the query and streaming endpoints
come with a `meta_id` property.
You can then query a detailed metadata endpoint with that id
to learn more about how that observation was derived.

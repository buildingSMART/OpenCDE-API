This was originally taken from the Aconex repository at [https://github.com/Aconex/opencde-api-draft](https://github.com/Aconex/opencde-api-draft) by @nicktindall.

# opencde-api-draft

A draft specification for the OpenCDE API, written using OpenAPI

There is only one endpoint in the specification that should have a standard path, that's 
the start select document flow endpoint. I've attempted to illustrate this by putting the
non-standard paths in the spec as /unspecified/this-is-not-a-standard-path/...

OpenAPI isn't particularly good for specifying HAL APIs.

I decided it would make sense to use the HAL standard shape for the responses because it's
basically the way we are specifying the API. I thought there might be good tools for it,
apparently OpenAPI is not one.

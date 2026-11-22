# Before the First Call: API & MCP Contract Desk

Five small checks to resolve before an integration makes its first request.
The [compact reference desk](https://biolinky.co/mcp-reference-desk) keeps the
official references together. This companion explains what to check and why.

## 1. What instant does a timestamp describe?

`2026-11-22T09:00:00Z` and `2026-11-22T10:00:00+01:00` describe the same
instant. A local clock value without an offset does not tell the receiving
system which instant you intended.

Before sending a test event, check that its timestamp includes `Z` or a numeric
offset. Keep the original offset when it matters to the application; compare
instants using a date-time parser rather than by sorting mixed-offset strings.
Do not infer a timezone from the location of the computer running the client.

Reference: [RFC 3339](https://www.rfc-editor.org/info/rfc3339/).

## 2. Is a field missing, null, or the wrong type?

These two objects are different:

```json
{}
```

```json
{"source_id": null}
```

A schema can require the property to exist while separately restricting its
value to a string. Presence alone does not make `null` a string. Likewise,
declaring a property does not automatically make that property required.

Read `properties`, `required` and the accepted types together. Check the
contract's treatment of extra properties rather than silently discarding
unknown data or assuming every extra key will be accepted.

Reference: [JSON Schema object reference](https://json-schema.org/understanding-json-schema/reference/object).

## 3. Which method and path define the operation?

For a test contract, `GET /items` and `POST /items` are separate operations.
The existence of one does not establish that the other is supported. A successful
request to a browser page also does not prove that an API accepts the same path.

Find the documented method, path, server URL and authentication requirements.
Then check the parameter locations, request body if present, and documented
responses. Start with a documented read operation when you only need to read
data; do not probe an undocumented write to learn what it does.

Reference: [OpenAPI Specification 3.1.0](https://spec.openapis.org/oas/v3.1.0).

## 4. What access is the client requesting?

For an HTTP-based MCP connection, read the provider's authentication contract
and the protocol version it supports. Inspect requested permissions and the
intended resource before granting access. A token issued for one resource is
not permission to forward it to another service.

Keep bearer tokens out of query strings and public examples. Request the access
the application actually needs, and keep a read task separate from permission
to write. The linked specification describes HTTP authorization; it is not a
claim that every transport or provider uses that flow.

Reference: [MCP authorization, version 2025-06-18](https://modelcontextprotocol.io/specification/2025-06-18/basic/authorization).

## 5. Where is the product-specific contract?

Apply the same questions to the actual product documentation rather than
inferring capabilities from a logo, directory entry or generic protocol guide.

For example, the [ClosedLoop AI API documentation](https://closedloop.sh/developers/api)
distinguishes MCP access with OAuth, a read-only `/v1` API and signed webhooks.
Choose the documented surface for the job and follow its own authentication
and request contract. Reading a reference does not establish that an integration
has been installed, tested or adopted.

## A compact handoff

Before handing an integration to someone else, record the timestamp convention,
required properties and null handling, exact method/path, requested access,
and the URL and version of the product contract. Keep credentials in the
application's approved secret storage, outside this handoff.

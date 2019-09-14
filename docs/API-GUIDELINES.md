# SwissRETS-API Standard Guidelines

## General design principles
* The API is RESTful.
* Only full imports are supported. All properties not referenced in the import file will be deleted/deactivated on the platform. 
* API actions are not necessarily idempotent. E.g. an import for an owner can be blocked while another import for that owner is running (returns `HTTP 409 Conflict`).
* The import processing is asynchronous. The state of the import can be polled by a specific endpoint.
* The support of local assets with file:// URIs (e.g. from an FTP-Server) in the SwissRETS attachment is platform specific. A platform can, but is not forced to support local assets. The `/meta` endpoint informs whether local endpoints are supported. 
* Authentication happens via [OAuth 2.0 standard](https://oauth.net/2/) with the [resource owner password credentials grant type](https://tools.ietf.org/html/rfc6749#section-4.3). The authenticated owner should match the ownerId in the API request URL.
* The API may restrict the mime types of uploaded files (application/xml, application/gzip, application/zip, ...) and return `HTTP 415 Unsupported Media Type`. The `/meta` endpoint informs about the list of supported media types. 
* Resumable uploads may, but must not be supported by the platform in the form of the [tus.io](https://tus.io/) standard. 
* Error codes returned by the API are standardized to ensure a cross platform consistency in behaviour. Custom error codes can be added by the platform if needed.
* Data validation is performed by the XSD validation against the [SwissRETS schema](https://github.com/qualipool/swissrets/blob/master/schema/schema.xsd). It is up to the platform to decide, if XSD validation should happen synchronously or asynchronously. The `/meta` endpoint informs about how the platform supports schema validation.
* Additional validation can happen on the platform. All validation errors should return an xpath expression pointing to the affected node in the XML document. 

## Endpoints

### GET /api/meta

> Returns meta information about the API, e.g. about supported or unsupported functionality.

### POST /api/owners/{ownerId}/imports

> Schededules an import for the given owner and returns an import identifier, which can be used to poll the status of the import.

### GET /api/owners/{ownerId}/imports/{importId}

> Returns status information about the specified import.
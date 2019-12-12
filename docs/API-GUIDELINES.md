# SwissRETS-API Standard Guidelines

## General design principles

* The API is RESTful.

* Only full imports are supported. All properties not referenced in the import file will be deleted/deactivated on the platform. 

* Only one XML import file can be uploaded per import request.

* API actions are not necessarily idempotent. E.g. an import for an owner can be blocked while another import for that owner is running (returns `HTTP 409 Conflict`).

* The import processing is asynchronous. The state of the import can be polled by the `/api/owners/{ownerId}/imports/{importId}` endpoint.

* The support of local assets with file:// URIs (e.g. from an FTP-Server or by https upload with the import request) may, but must not be supported by the API. The `/api/meta` endpoint informs whether local assets are supported. Assets with http(s) URIs must be supported by the API.

* The import endpoint must support the processing of compressed files. Supported compression formats are *.zip, *.gz, *.tgz.

* The import endpoint expects a multipart/form-data request, even if only a single file is uploaded.

* Authentication and authorization happens against the `POST /api/users/{userId}/authorize` endpoint. The endpoint receives the API user's credentials and supplies an accesstoken which must be provided for bearer authorization with all protected resources. The authorization header must be sent as follows: `Authorization: Bearer <accesstoken>`

* The API may restrict the mime types of uploaded files (application/xml, application/gzip, application/zip, ...) and return `HTTP 415 Unsupported Media Type`. The `/api/meta` endpoint informs about the list of supported media types. 

* Resumable uploads may, but must not be supported by the API in the form of the [tus.io](https://tus.io/) standard. The `/api/meta` endpoint informs about the support of this feature.

* Error codes returned by the API are standardized to ensure a cross platform consistency in behaviour. Custom error codes can be added by the platform if needed.

* Data validation is performed by XSD validation against the [SwissRETS schema](https://github.com/qualipool/swissrets/blob/master/schema/schema.xsd). It is up to the platform to decide, if XSD validation should happen synchronously or asynchronously. 

* Additional validation can happen on the platform. 

## Endpoints

A detailed swagger specification of the API can be found [here](/docs/swagger.json).

### GET /api/meta

> Returns meta information about the API, e.g. about supported or unsupported functionality, system maintenance status, ...

### POST /api/users/authorize

> Authenticates the given user with username and password. Returns an accesstoken which must be provided for bearer authorization with all protected resources. 

### POST /api/owners/{ownerId}/imports

> Schededules an import for the given owner and returns an import identifier, which can be used to poll the status of the import.

### GET /api/owners/{ownerId}/imports/{importId}

> Returns status information about the specified import.

### POST /api/owners/{ownerId}/imports/validate

> Returns validation information about the uploaded SwissRETS document. The document may contain only a single property. The validation result is returned synchronously.

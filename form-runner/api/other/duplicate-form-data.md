# Duplicate form data API

## Availability

Since Orbeon Forms 4.7.

## Internal uses

The API is also used internally by the Summary page via the Duplicate button.

## Purpose

The purpose of the `duplicate` API is to duplicate form data, including form data attachments. This API cannot be used to duplicate [auto-saved data](/form-runner/persistence/autosave.md), as it doesn't provide a way for the caller to specify whether the data to duplicate was auto-saved or not.

## Interface

- URL: `/fr/service/$app/$form/duplicate`
- Method: `POST`

Request body:

- `Content-Type: application/xml`
- the element contains the document id to duplicate

Example request body:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<document-id>51dfcf49bb4b7f994906a26911003e4a999f1e39</document-id>
```

Response body:

- `Content-Type: application/xml`
- the element contains the newly created document id

Example response body:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<document-id>c0f6dd2e75e94f60b9493768843e3fdef2af6bc0</document-id>
```

## Permissions

The caller must either call the service internally or have proper credentials to access the data (username, group, roles).

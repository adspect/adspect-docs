# ComsignDB

ComsignDB is an ad-hoc NoSQL content database software with on-disk persistence and Redis-like access semantics.

Copyright &copy; 2019&ndash;2026 [Comsign](https://www.comsign.io/).  All rights reserved.

## Usage

Running ComsignDB from command line:

```
# comsigndb [--config /etc/comsign/comsigndb.json]
# comsigndb --help
```

## Configuration

By default, ComsignDB will load configuration from the file `/etc/comsign/comsigndb.json`.  You may specify one or more alternative configuration files on the command line via the `-c` (or `--config`) option.  If several configuration files are specified, they will be loaded in the order they appear on the command line.

## HTTP API

Available HTTP API endpoints are detailed below.

Some endpoints either expect JSON-encoded data in request body, or return JSON-encoded data in response body.  In the latter case, ComsignDB will set the `Content-Type: application/json` response header.

### List Databases

```
GET /
```

Response example:

```
[
  "database1",
  "database2"
]
```

### Save Database to Disk

```
SAVE /[database]
```

If the database is not specified, all databases will be saved.

### Load Database from Disk

```
LOAD /[database]
```

If the database is not specified, all databases will be loaded.

### List Keys

```
GET /<database>
```

This endpoint returns an object with key types as property names and arrays of keys as property values.

Response example:

```
{
  "set": [
    "key1",
    "key2"
  ],
  "string": [
    "key1",
    "key2"
  ]
}
```

### List Keys by Type

```
GET /<database>/set
```

Response example:

```
[
  "key1",
  "key2"
]
```

### Create Keys

Unlike Redis, keys are not implicitly created by insertion operations and must be created manually before they can be used.

```
PUT /<database>/set
[
  "key1",
  "key2"
]
```

### Delete Keys

```
DELETE /<database>/set
[
  "key1",
  "key2"
]
```

### Set String

```
PUT /<database>/string/<key>
Hello, world!
```

This endpoint stores the whole raw request body as a string.  It does **not** decode the request body as JSON.

### Get String

```
GET /<database>/string/<key>
```

### Get String Length

```
SIZE /<database>/string/<key>
```

### Add Set Elements

```
PUT /<database>/set/<key>
[
  "element1",
  "element2"
]
```

### Remove Set Elements

```
DELETE /<database>/set/<key>
[
  "element1",
  "element2"
]
```

### Get Set Cardinality

```
SIZE /<database>/set/<key>
```

This endpoint returns the number of elements in the set (an integer value).

### Get All Set Elements

```
GET /<database>/set/<key>
```

Response example:

```
[
  "element1",
  "element2",
  "element3"
]
```

### Test Set Membership

```
TEST /<database>/set/<key>
[
  "element1",
  "element2",
  "element3"
]
```

This endpoint accepts an array of elements and returns an array of those elements that are present in the set.  The output array preserves the order of elements in the input array.  If the same element appears several times in the input array, it will appear the same number of times in the ouput array, given that it is present in the set.

Response example:

```
[
  "element1",
  "element3"
]
```

### Get Random Set Elements

```
RANDOM /<database>/set/<key>?n=<number>&seed=<seed>
```

This endpoint returns `<number>` of randomly picked set elements (defaults to 1.)  Uniqueness is not guaranteed, i.e. the resulting array may contain the same elements multiple times.

Optionally, you may seed the random number generator with an arbitrary string `<seed>`.  If not specified, the RNG will be seeded from the system entropy source.

Response example:

```
[
  "element4",
  "element27",
  "element11"
]
```

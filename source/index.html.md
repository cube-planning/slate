---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - python

toc_footers:
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the Cube API documentation.

# Authentication

Cube uses API keys to allow access to the API. You can receive an access token for a user by providing their email and password to an access token endpoint.

The API expects the access token to be included in all API requests to the server in a header that looks like the following:

`Authorization: example-access-token`

## Get Access Token

```python
import requests

r = requests.post('<url>', data={
    'email': 'user@domain.com',
    'password': '1234'
})
r.json()
```

> The above command returns JSON structured like this:

```json
{
    "access_token": "example-access-token"
}
```

This endpoint retrieves an access token for a user at a company.

Parameter | Description
--------- | -----------
email | The user's email address
password | The password for the user

### HTTP Request

`POST https://dev.planwithcube.com/api/v1/access_token`

# Dimensions

Dimensions store the chart of accounts for a company and outline how their data is structured. All companies have top level dimensions called Account, Scenario, Department, and Time. They also can have custom dimensions.

## Get All Dimensions

```python
import requests

r = requests.get('<url>', headers={
    'Authorization': 'example-access-token'
})
r.json()
```

> The above command returns JSON structured like this:

```json
[
    {
        "id": 5190,
        "name": "Account",
        "active": true,
        "source": null,
        "formula": null,
        "created_at": "2019-03-12T12:26:28.896518+00:00"
    },
    {
        "id": 5191,
        "name": "Balance Sheet",
        "active": true,
        "source": null,
        "formula": null,
        "created_at": "2019-03-12T12:26:28.915209+00:00"
    },
    {
        "id": 5192,
        "name": "Income Statement",
        "active": true,
        "source": null,
        "formula": null,
        "created_at": "2019-03-12T12:26:28.927012+00:00"
    }
]
```

This endpoint retrieves all dimensions for a company.

### HTTP Request

`GET https://dev.planwithcube.com/api/v1/dimensions`

## Get Deepest Dimensions

```python
import requests

r = requests.get('<url>', headers={
    'Authorization': 'example-access-token'
})
r.json()
```

> The above command returns JSON structured like this:

```json
[
    {
        "id": 5421,
        "name": "Sales and Marketing",
        "active": true,
        "source": null,
        "formula": null,
        "created_at": "2019-03-13T17:07:26.513460+00:00",
        "children": []
    },
    {
        "id": 5422,
        "name": "Sports",
        "active": true,
        "source": null,
        "formula": null,
        "created_at": "2019-03-13T17:07:31.190547+00:00",
        "children": []
    },
    {
        "id": 5196,
        "name": "Actuals",
        "active": true,
        "source": null,
        "formula": null,
        "created_at": "2019-03-12T12:26:28.966437+00:00",
        "children": []
    }
]
```

This endpoint retrieves all the deepest level dimensions for a company.

### HTTP Request

`GET https://dev.planwithcube.com/api/v1/dimensions/deepest`

## Get Dimensions Tree

```python
import requests

r = requests.get('<url>', headers={
    'Authorization': 'example-access-token'
})
r.json()
```

> The above command returns JSON structured like this:

```json
[
    {
        "id": 5190,
        "name": "Account",
        "active": true,
        "source": null,
        "formula": null,
        "created_at": "2019-03-12T12:26:28.896518+00:00",
        "children": [
            {
                "id": 5192,
                "name": "Income Statement",
                "active": true,
                "source": null,
                "formula": null,
                "created_at": "2019-03-12T12:26:28.927012+00:00",
                "children": [
                    {
                        "id": 5693,
                        "name": "Revenue",
                        "active": true,
                        "source": null,
                        "formula": null,
                        "created_at": "2019-03-21T17:27:30.420024+00:00",
                        "children": [
                            {
                                "id": 5695,
                                "name": "Product Revenue",
                                "active": true,
                                "source": null,
                                "formula": null,
                                "created_at": "2019-03-21T20:44:45.150093+00:00",
                                "children": []
                            }
                        ]
                    },
                    {
                        "id": 5694,
                        "name": "COGs",
                        "active": false,
                        "source": null,
                        "formula": null,
                        "created_at": "2019-03-21T17:27:38.178389+00:00",
                        "children": [
                            {
                                "id": 5696,
                                "name": "Shirts",
                                "active": true,
                                "source": null,
                                "formula": null,
                                "created_at": "2019-03-21T21:18:01.786801+00:00",
                                "children": []
                            }
                        ]
                    },
                    {
                        "id": 5700,
                        "name": "Gross Revenue",
                        "active": true,
                        "source": null,
                        "formula": "<5693>-<5694>+<5701>",
                        "created_at": "2019-03-23T17:38:31.638926+00:00",
                        "children": []
                    }
                ]
            }
        ]
    },
    {
        "id": 5193,
        "name": "Department",
        "active": true,
        "source": null,
        "formula": null,
        "created_at": "2019-03-12T12:26:28.932454+00:00",
        "children": [
            {
                "id": 5194,
                "name": "Consolidated Only",
                "active": true,
                "source": null,
                "formula": null,
                "created_at": "2019-03-12T12:26:28.932990+00:00",
                "children": []
            },
            {
                "id": 5421,
                "name": "Sales and Marketing",
                "active": true,
                "source": null,
                "formula": null,
                "created_at": "2019-03-13T17:07:26.513460+00:00",
                "children": []
            }
        ]
    }
]
```

This endpoint retrieves a companies dimensions structured in their hierarchy.

### HTTP Request

`GET https://dev.planwithcube.com/api/v1/dimensions/tree`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
inactive | false | If set to true, the result will also include inactive dimensions.

# OLAP Cube Data

The data stored in the OLAP cube for a company, organized by its dimensions.

## Get Data Slices

```python
import requests

r = requests.get('<url>', headers={
    'Authorization': 'example-access-token'
})
r.json()
```

> The above command returns JSON structured like this:

```json
[
    {
        "id": "07c991586dfd4593add48225d06352b6",
        "value": "1.00",
        "dimensions": {
            "Account": [
                {
                    "id": 5192,
                    "name": "Income Statement",
                    "active": true
                },
                {
                    "id": 5693,
                    "name": "Revenue",
                    "active": true
                },
                {
                    "id": 5695,
                    "name": "Product Revenue",
                    "active": true
                }
            ],
            "Scenario": [
                {
                    "id": 5196,
                    "name": "Actuals",
                    "active": true
                }
            ],
            "Time": [
                {
                    "id": 5217,
                    "name": "2019",
                    "active": true
                },
                {
                    "id": 5218,
                    "name": "Q1",
                    "active": true
                },
                {
                    "id": 5219,
                    "name": "January",
                    "active": true
                }
            ],
            "Department": [
                {
                    "id": 5194,
                    "name": "Consolidated Only",
                    "active": true
                }
            ],
            "Product": [
                {
                    "id": 5420,
                    "name": "Consolidated Only",
                    "active": true
                }
            ],
            "Region": [
                {
                    "id": 5692,
                    "name": "Consolidated Only",
                    "active": true
                }
            ]
        }
    },
    {
        "id": null,
        "value": "3.00",
        "dimensions": {
            "Account": [
                {
                    "id": 5192,
                    "name": "Income Statement",
                    "active": true
                },
                {
                    "id": 5700,
                    "name": "Gross Revenue",
                    "active": true
                }
            ],
            "Scenario": [
                {
                    "id": 5196,
                    "name": "Actuals",
                    "active": true
                }
            ],
            "Time": [
                {
                    "id": 5217,
                    "name": "2019",
                    "active": true
                },
                {
                    "id": 5218,
                    "name": "Q1",
                    "active": true
                },
                {
                    "id": 5221,
                    "name": "March",
                    "active": true
                }
            ],
            "Department": [
                {
                    "id": 5194,
                    "name": "Consolidated Only",
                    "active": true
                }
            ],
            "Product": [
                {
                    "id": 5420,
                    "name": "Consolidated Only",
                    "active": true
                }
            ],
            "Region": [
                {
                    "id": 5692,
                    "name": "Consolidated Only",
                    "active": true
                }
            ]
        }
    }
]
```

This endpoint retrieves values stored in the OLAP cube for a given company. Any record with a `null` ID is a calculated value (i.e. rollups) whereas records with IDs are values explicitly stored in the cube.

Data can be narrowed down to various slices by providing dimension paths for top level dimensions (Account, Scenario, etc). These slices can be specified in any of the following ways:

### Top Level Dimension Names With String Path Names

Parameter | Description
--------- | -----------
Account | Income Statement
Department | Consolidated Only
Time | 2019:Q1
Scenario | Actuals

### Top Level Dimension Names With IDs

Parameter | Description
--------- | -----------
Account | 5192
Department | 5194
Time | 5218
Scenario | 5196

### Top Level Dimension Names With Numerous IDs
In this instance, ID 312 might be year 2018 and ID 456 might be year 2019 if you want to avoid loading 2020, etc.

Parameter | Description
--------- | -----------
Account | 5192
Department | 5194
Time | 312&#124;456
Scenario | 5196

### Top Level Dimension IDs With IDs

Parameter | Description
--------- | -----------
12 | 5192
43 | 5194
54 | 5218
56 | 5196

### HTTP Request

`GET https://dev.planwithcube.com/api/v1/cube`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
require_all | false | If set to true, a slice for each parent level dimension will be required.
default_to_consolidated | false | If set to true, the any top level parent dimensions not provided will default to "Consolidated Only"
rollup_rows | none | The result will rollup aggregates based on the name of this top level dimension (i.e. "Account").
rollup_columns | none | The result will rollup aggregates based on the name of this top level dimension (i.e. "Time").

## Update Data

```python
import requests

r = requests.post('<url>', headers={
    'Authorization': 'example-access-token'
})
r.json()
```

> The above command expects to include JSON structured like this:

```json
{
    "message": "A message for this change",
    "data": [
        {
            "Account": 432,
            "Department": 5345,
            "Scenario": 345,
            "Time": 455,
            "value": 30000,
        },
        {
            "id": "01cceb66acf402166515da14d64c7f7e",
            "value": 30000,
        }
    ]
}
```

> The above command returns JSON structured like this:

```json
[
    {
        "id": "01cceb66acf402166515da14d64c7f7e",
        "value": "30000"
    }
]
```

This endpoint can be used to update data in the OLAP cube. The `message` will be used in the changelog for any changes to data. The keys for each record in `data` are expected to be top level dimension names or IDs (similar to [retrieving data slices](#get-data-slices)). The value for each key should be the name or ID of a deepest level dimension belonging to that parent dimension. Inside of each `data` record should also be a `value` key with the actual value of that data in the cube.

If you know the ID of the particular value in the cube you want to update, you can also use that instead in the `data` records.


### HTTP Request

`POST https://dev.planwithcube.com/api/v1/cube/update`

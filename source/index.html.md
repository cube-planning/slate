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

If a user belongs to more than one company, an `X-Company-ID` header can be passed specifying the company ID context to use for that particular API call.

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
    "access_token": "example-access-token",
    "user": {
        "created_at": "2019-11-16T13:02:01.076997+00:00",
        "first_name": "Dan",
        "last_name": "Bricklin",
        "email": "email@company.com",
        "id": 1
    },
    "company": {
        "id": "6a42ac12-ea84-490b-85b9-e80eaf5a72de",
        "created_at": "2019-06-23T16:32:38.249315+00:00",
        "name": "Example Company"
    },
}
```

This endpoint retrieves an access token for a user at a company. It also provides some information about the company the access token was authenticated against for the given user.

In the event the user has 2FA enabled, an error code of `"2FA_REQUIRED"` will be returned. A subsequent call with `2fa_token` provided should then be made to authenticate the user properly.

Parameter | Description
--------- | -----------
email | The user's email address
password | The password for the user
2fa_token | The token for the user if they have 2FA enabled

Error responses to this endpoint include a `"code"` to help decode the specific error occurring.

Error Code | Description
--------- | -----------
INVALID_CREDENTIALS | The email address and password do not match
2FA_REQUIRED | The email and address are correct, but a 2FA token is also required
INVALID_2FA_CODE | The provided 2FA code was not accepted

### HTTP Request

`POST https://api.cubesoftware.com/v1/auth/access_token`

## List 2FA Devices

```python
import requests

r = requests.post('<url>', data={
    'email': 'user@domain.com',
    'password': '1234'
})
r.json()
```

> The above request returns a JSON structured like:

```json
{
    "devices": [
        {
            "name": "backup",
            "is_interactive": false,
            "message": "Lost your device? Enter a backup code"
        },
        {
            "name": "default",
            "is_interactive": true,
            "message": "Send text message to +1 ***-***-**19"
        }
    ]
}
```

This endpoint lists all a user's verified two factor authentication devices.

Parameter | Description
--------- | -----------
email | The user's email address
password | The password for the user

Each device has relevant information about next steps stored in a dictionary:

Key | Value
--- | -----
name | The name of this device for internal lookups
is_interactive | Whether this device needs to generate a challenge (e.g. send an SMS, email, etc)
message | Human-friendly copy describing the action for this device

Note: A device such as an authenticator app will have `is_interacive: false` because the user already has the
token they need by accessing the app.

### HTTP Request

`POST https://api.cubesoftware.com/v1/auth/two_factor/devices`

## Generate 2FA Challenge

```python
import requests

r = requests.post('<url>', data={
    'email': 'user@domain.com',
    'password': '1234',
    'device_name': 'default'
})
r.json()
```

> The above request returns a JSON structured like:

```json
{
    "success": true,
    "mesage": null
}
```

This endpoint generates a challenge for interactive two factor authentication devices (e.g. send an SMS, start
a phone call, or send an email with the token).

Parameter | Description
--------- | -----------
email | The user's email address
password | The password for the user
device_name | The internal device name (e.g. "default", "backup")

Error responses to this endpoint include a `"code"` to help decode the specific error occurring.

Error Code | Description
--------- | -----------
NO_CHALLENGE | This device is not interactive (i.e. not capable of generating a challenge)
NO_SUCH_DEVICE | The given device could not be found

### HTTP Request

`POST https://api.cubesoftware.com/v1/auth/two_factor/challenge`

# Companies

A user in Cube belongs to one or more companies. Companies are how various teams of employees manage their data (dimensions, etc) and permissions (users, etc).

## Get User's Companies

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
        "created_at": "2019-06-23T16:32:38.249315+00:00",
        "id": "6a42ac12-ea84-490b-85b9-e80eaf5a72de",
        "name": "Example Company One"
    },
    {
        "created_at": "2019-06-23T16:32:30.599032+00:00",
        "id": "8e2dffb6-30cf-46eb-b2f8-5030a9aebf01",
        "name": "Example Company Two"
    }
]
```

This endpoint retrieves all the companies a user has access to.

### HTTP Request

`GET https://api.cubesoftware.com/v1/user/companies`

# Attributes

Attributes in Cube are additional metadata that can be applied to values in the cube.

## Get Company's Attributes

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
        "id": 1,
        "table": {
            "id": 1,
            "created_at": "2019-07-29T12:47:58.441019+00:00",
            "name": "Travel and Entertainment"
        },
        "created_at": "2019-07-01T15:10:43.002851+00:00",
        "name": "Vendor"
    }
]
```

This endpoint retrieves all the attributes of a company.

### HTTP Request

`GET https://api.cubesoftware.com/v1/attributes`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
table | none | The ID of an attribute table to limit results to

# Dimensions

Dimensions store the chart of accounts for a company and outline how their data is structured. All companies have top level dimensions called Account, Scenario, Department, and Time. They also can have custom dimensions.

## Create A Dimension

```python
import requests

r = requests.get('<url>', headers={
    'Authorization': 'example-access-token'
})
r.json()
```

> The above command returns JSON structured like this:

```json
{
    "name": "New Dimension Name",
    "parent": 123
}
```

This endpoint creates a dimension.

### HTTP Request

`POST https://api.cubesoftware.com/v1/dimensions`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
name | none | The name to use for the dimension
parent | none | The ID of a dimension to use as the parent for this dimension
write_protection | NONE | The write protection to use (allowed values include "NONE", "BLOCK", and "ACTUALS")

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

This endpoint retrieves a company's dimensions structured in their hierarchy.

### HTTP Request

`GET https://api.cubesoftware.com/v1/dimensions/tree`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
inactive | false | If set to true, the result will also include inactive dimensions.

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

`GET https://api.cubesoftware.com/v1/dimensions/deepest`

# OLAP Cube Data

The data stored in the OLAP cube for a company, organized by its dimensions.

## Get Data By Slice (Deprecated)

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

**Note**: This endpoint is deprecated. Please use the [pivot (rows/columns) endpoint](#get-data-by-rows-columns) instead.

This endpoint retrieves values stored in the OLAP cube for a given company. Any record with a `null` ID is a calculated value (i.e. rollups) whereas records with IDs are values explicitly stored in the cube. It is most useful when only one dimension exists in the rows of the data and one dimension exists in the columns of the data.

Data can be narrowed down to various slices by providing dimension paths for top level dimensions (Account, Scenario, etc). These slices can be specified in any of the following ways:

### Top Level Dimension Names With String Path Names Example

Parameter | Description
--------- | -----------
Account | Income Statement
Department | Consolidated Only
Time | 2019:Q1-19
Scenario | Actuals

### Top Level Dimension Names With IDs Example

Parameter | Description
--------- | -----------
Account | 5192
Department | 5194
Time | 5218
Scenario | 5196

### Top Level Dimension Names With Numerous IDs Example
In this instance, ID 312 might be year 2018 and ID 456 might be year 2019 if you want to avoid loading 2020, etc.

Parameter | Description
--------- | -----------
Account | 5192
Department | 5194
Time | 312&#124;456
Scenario | 5196

### Top Level Dimension IDs With IDs Example

Parameter | Description
--------- | -----------
12 | 5192
43 | 5194
54 | 5218
56 | 5196

### HTTP Request

`GET https://api.cubesoftware.com/v1/cube`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
require_all | false | If set to true, a slice for each parent level dimension will be required.
default_to_consolidated | false | If set to true, the any top level parent dimensions not provided will default to "Consolidated Only"
rollup_rows | none | The result will rollup aggregates based on the name of this top level dimension (i.e. "Account").
rollup_columns | none | The result will rollup aggregates based on the name of this top level dimension (i.e. "Time").

## Get Data By Rows / Columns

```python
import requests

r = requests.post('<url>', data=data, headers={
    'Authorization': 'example-access-token'
})
r.json()
```

> The above command expects to include JSON structured like this:

```json
{
    "filters": {
        "Department": "Consolidated Only"
    },
    "rows": [
        [
            "Revenue",
            "COGS"
        ]
    ],
    "columns": [
        [
            "Budget",
            "Actuals",
            "Budget",
            "Actuals"
        ],
        [
            "Jan-19",
            "Jan-19",
            "Feb-19",
            "Feb-19"
        ]
    ]
}
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
            ]
        }
    }
]
```

This endpoint retrieves values stored in the OLAP cube for a given company. Any record with a `null` ID is a calculated value (i.e. rollups) whereas records with IDs are values explicitly stored in the cube.

This endpoint is useful when creating reports out of data in the cube. Like a pivot table, you are able to specify which dimensions are in your rows / columns for the report, and the API will figure out the appropriate data to pull. In the example on the right, the "Account" dimension is provided as rows with "Scenario" and "Time" being provided as columns. Therefore, the "Department" dimension must be provided as a filter.

Like [retrieving data by slices](#get-data-by-slice), you are able to pass either the names of the dimensions or their IDs as parameters.

### HTTP Request

`POST https://api.cubesoftware.com/v1/cube/pivot`

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

### HTTP Request

`POST https://api.cubesoftware.com/v1/cube/update`

## View Details For A Value

```python
import requests

r = requests.get('<url>', headers={
    'Authorization': 'example-access-token'
})
r.json()
```

> The above command returns JSON structured like this:

```json
{
    "id": "d9b44dbe-4636-412b-84cb-407d1fa3987b",
    "value": "4.00",
    "formula": null,
    "calculated": false,
    "attributes": {},
    "splits": [
        {
            "value": "2.00",
            "attributes": {
                "Vendor": "Soup Co",
                "Reimburseable": "True"
            }
        },
        {
            "value": "2.00",
            "attributes": {
                "Vendor": "Coffee Co",
                "Reimburseable": "False"
            }
        }
    ]
}
```

This endpoint retrieves detailed information about a specific value in the cube. This information includes any attributes that are associated with this data as well as any splits that make up the overall value stored in the cube.

Retrieving single cube value data is very similar to [retrieving data by slices](#get-data-by-slice) in that you are able to pass either the names of the dimensions or their IDs as parameters. However, a dimension value must be provided for each top level dimension and those values must be for deepest level dimensions (since data lives at the cross section of deepest level dimensions).

### HTTP Request

`GET https://api.cubesoftware.com/v1/cube/value`

### Top Level Dimension Names With String Path Names Example

Parameter | Description
--------- | -----------
Account | Income Statement:Revenue:Product Revenue
Department | Consolidated Only
Time | 2019:Q1-19:Jan-19
Scenario | Actuals


## Drilldowns

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
        "id": "5615e9dfda074543a10913d8777a2dd5",
        "value": "88.29",
        "formula": null,
        "calculated": false,
        "dimensions": {
            "Account": [
                {
                    "id": 27179,
                    "formula": null,
                    "has_children": true,
                    "name": "Income Statement",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": null,
                    "date": null
                },
                {
                    "id": 27261,
                    "formula": null,
                    "has_children": true,
                    "name": "Revenue",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": null,
                    "date": null
                },
                {
                    "id": 27262,
                    "formula": null,
                    "has_children": false,
                    "name": "Product Revenue",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": null,
                    "date": null
                }
            ],
            "Scenario": [
                {
                    "id": 27184,
                    "formula": null,
                    "has_children": false,
                    "name": "Actuals",
                    "active": true,
                    "write_protection": "ACTUALS",
                    "special_case_id": null,
                    "date": null
                }
            ],
            "Time": [
                {
                    "id": 27205,
                    "formula": null,
                    "has_children": true,
                    "name": "2019",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": null,
                    "date": "2019-12-31"
                },
                {
                    "id": 27206,
                    "formula": null,
                    "has_children": true,
                    "name": "Q1-19",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": null,
                    "date": "2019-03-31"
                },
                {
                    "id": 27208,
                    "formula": null,
                    "has_children": false,
                    "name": "Feb-19",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": null,
                    "date": "2019-02-28"
                }
            ],
            "Department": [
                {
                    "id": 27182,
                    "formula": null,
                    "has_children": false,
                    "name": "Department - Other",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": "DEFAULT",
                    "date": null
                }
            ]
        },
        "attributes": {},
        "splits": []
    },
    {
        "id": "b2ea4a21d64744ef88ac8d6722fe861b",
        "value": "58.76",
        "formula": null,
        "calculated": false,
        "dimensions": {
            "Account": [
                {
                    "id": 27179,
                    "formula": null,
                    "has_children": true,
                    "name": "Income Statement",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": null,
                    "date": null
                },
                {
                    "id": 27261,
                    "formula": null,
                    "has_children": true,
                    "name": "Revenue",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": null,
                    "date": null
                },
                {
                    "id": 27262,
                    "formula": null,
                    "has_children": false,
                    "name": "Product Revenue",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": null,
                    "date": null
                }
            ],
            "Scenario": [
                {
                    "id": 27184,
                    "formula": null,
                    "has_children": false,
                    "name": "Actuals",
                    "active": true,
                    "write_protection": "ACTUALS",
                    "special_case_id": null,
                    "date": null
                }
            ],
            "Time": [
                {
                    "id": 27205,
                    "formula": null,
                    "has_children": true,
                    "name": "2019",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": null,
                    "date": "2019-12-31"
                },
                {
                    "id": 27206,
                    "formula": null,
                    "has_children": true,
                    "name": "Q1-19",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": null,
                    "date": "2019-03-31"
                },
                {
                    "id": 27209,
                    "formula": null,
                    "has_children": false,
                    "name": "Mar-19",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": null,
                    "date": "2019-03-31"
                }
            ],
            "Department": [
                {
                    "id": 27182,
                    "formula": null,
                    "has_children": false,
                    "name": "Department - Other",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": "DEFAULT",
                    "date": null
                }
            ]
        },
        "attributes": {},
        "splits": []
    },
    {
        "id": "d5f014dc9ad34ad289a8655a39c18b82",
        "value": "969.40",
        "formula": null,
        "calculated": false,
        "dimensions": {
            "Account": [
                {
                    "id": 27179,
                    "formula": null,
                    "has_children": true,
                    "name": "Income Statement",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": null,
                    "date": null
                },
                {
                    "id": 27261,
                    "formula": null,
                    "has_children": true,
                    "name": "Revenue",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": null,
                    "date": null
                },
                {
                    "id": 27262,
                    "formula": null,
                    "has_children": false,
                    "name": "Product Revenue",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": null,
                    "date": null
                }
            ],
            "Scenario": [
                {
                    "id": 27184,
                    "formula": null,
                    "has_children": false,
                    "name": "Actuals",
                    "active": true,
                    "write_protection": "ACTUALS",
                    "special_case_id": null,
                    "date": null
                }
            ],
            "Time": [
                {
                    "id": 27205,
                    "formula": null,
                    "has_children": true,
                    "name": "2019",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": null,
                    "date": "2019-12-31"
                },
                {
                    "id": 27206,
                    "formula": null,
                    "has_children": true,
                    "name": "Q1-19",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": null,
                    "date": "2019-03-31"
                },
                {
                    "id": 27207,
                    "formula": null,
                    "has_children": false,
                    "name": "Jan-19",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": null,
                    "date": "2019-01-31"
                }
            ],
            "Department": [
                {
                    "id": 27182,
                    "formula": null,
                    "has_children": false,
                    "name": "Department - Other",
                    "active": true,
                    "write_protection": "NONE",
                    "special_case_id": "DEFAULT",
                    "date": null
                }
            ]
        },
        "attributes": {},
        "splits": []
    }
]
```

This endpoint retrieves any values in the cube that make up the overall value at the provided cross section of dimensions. For instance, in the example JSON "Q1-19" was provided as the "Time" dimension and you can see that it returned a value for "Jan-19", "Feb-19", and "Mar-19" which all make up "Q1-19".

Drilling down in to a cube value is very similar to [retrieving data by slices](#get-data-by-slice) in that you are able to pass either the names of the dimensions or their IDs as parameters. A dimension value must be provided for each top level dimension.

### HTTP Request

`GET https://api.cubesoftware.com/v1/cube/drilldown`

### Top Level Dimension Names With String Path Names Example

Parameter | Description
--------- | -----------
Account | Income Statement:Revenue:Product Revenue
Department | Consolidated Only
Time | Q1-19
Scenario | Actuals

# Currencies

Cube supports fetching data in other currencies as long as the company has the alternate currency enabled.

All currency requests must be made via the [pivot (rows/columns) endpoint](#get-data-by-rows-columns).

## Fetch in an Alternate Currency

```python
import requests

r = requests.post('<url>', data=data, headers={
    'Authorization': '<your-access-token>'
})
r.json()
```

> The above request expects a `data` JSON object structured like:

```json
{
    "currency": {
        "code": "GBP",
        "custom_rates": {
            "past": "1.234567"
        }
    },
    "dimensions": {
        "filters": {
            "Department": "Consolidated Only"
        },
        "rows": [
            [
                "Revenue",
                "COGS"
            ]
        ],
        "columns": [
            [
                "Actuals",
                "Actuals"
            ],
            [
                "Jan-19",
                "Feb-19"
            ]
        ]
    }
}
```

You can send currency information to the [pivot (rows/columns) endpoint](#get-data-by-rows-columns).

### `currency` JSON Keys

Key | Description
--- | -----------
code | A three-letter currency code (the currency must be enabled for your company)
custom_rates | User-specified rates that override the historical rates (see below)

### `custom_rates` JSON keys

Custom rates override historical rates for a given time period.

Key | Description
--- | -----------
past | Override rate for past months' data, as a string (e.g. "1.25")
current | Override rate for this month's data, as a string (e.g. "1.25")
future | Override rate for future months' data, as a string (e.g. "1.25")

# Report Templates

User configuration of sheets ranges, with their corresponding rows, columns, and filters.

For development purposes, a Report is a given range on a user's sheet, with row/column/filter data. A Report Template is a collection of Reports on a given sheet (aka multiple ranges on a single sheet).


## Retrieve Report Templates

```python
import requests

r = requests.get('<url>', headers={
    'Authorization': 'example-access-token'
})
r.json()
```

> The above request returns a JSON structured like this:

```json
[
    {
        "created_at": "2019-09-25T18:49:53.260147+00:00",
        "id": 8,
        "name": "My Template",
        "modified_at": "2019-09-25T18:53:01.163997+00:00",
        "reports": [
            {
                "id": 34,
                "sheet_range": "B1:D4",
                "rows": {
                    "10": [ "34", "35", "36", "33" ]
                },
                "columns": {
                    "6": [ "7", "9", "8" ]
                },
                "filters": {
                    "4": [ "5" ],
                    "1": [ "65" ]
                }
            },
            {
                "id": 35,
                "sheet_range": "B7:E10",
                "rows": {
                    "1": [ "65", "66", "67", "68" ]
                },
                "columns": {
                    "10": [ "29", "29", "33", "33" ],
                    "6": [ "7", "8", "7", "8" ]
                },
                "filters": {
                    "4": [ "5" ]
                }
            }
        ]
    }
]
```

This endpoint fetches an array of all the Report Templates available to the currently authenticated user. This includes all Report Templates anyone at the user's company has created.

Report Templates may be made up of one or many reports.

### Report Template Response Format

Item | Type | Explanation
-----|------|------------
id | integer | The unique primary key for this Report Template
name | string | User-specified name for the Report Template, unique for the authenticated user
created_at | string | When this Report Template was first created
modified_at | string | When this Report Template was last modified
reports | array | All the Reports in this Report Template see [Report Response Format](#report-response-format)

### Report Response Format

Item | Type | Explanation
-----|------|------------
id | integer | The unique primary key for this Report
sheet_range | string | The cell range for placing this report on a spreadsheet (e.g. "A1:D5")
rows | dictionary | Format: `{top level dimension id: [list of individual row dimensions]}`
columns | dictionary | Format: `{top level dimension id: [list of individual column dimensions]}`
filters | dictionary | Format: `{top level dimension id: [one filter dimension]}`

### HTTP Request

`GET https://api.cubesoftware.com/v1/templates`


## Create Report Template

```python
import requests

r = requests.post('<url>', data=data, headers={
    'Authorization': 'example-access-token'
})
r.json()
```

> The above request requires a `data` JSON object structured like this:

```json
{
   "name":"New Template",
   "reports":[
      {
         "range":"B1:E4",
         "rows":{
            "Scenario":[
               "Actuals",
               "Forecast",
               "Budget",
               "User-created String"
            ]
         },
         "columns":{
            "Time":[
               "Jan-19",
               "Feb-19",
               "Mar-19",
               "Q1-19"
            ]
         },
         "filters":{
            "Department":[
               "Consolidated Only"
            ],
            "Account":"Revenue"
         }
      },
      {
         "range":"B7:E10",
         "rows":{
            "Account":[
               "Revenue",
               "COGS",
               "Raw COGS",
               "Gross Margin"
            ]
         },
         "columns":{
            "Time":[
               "Q1-19",
               "Q1-19",
               "Q2-19",
               "Q2-19"
            ],
            "Scenario":[
               "Actuals",
               "Budget",
               "Actuals",
               "Budget"
            ]
         },
         "filters":{
            "Department":"Consolidated Only"
         }
      }
   ]
}
```

This endpoint allows the creation of new Report Templates in the database.

You can send row, column, and filters as dimension names or IDs. The API will convert dimension names into IDs. Row/column name strings that do not match the name of a dimension are saved to preserve user-inputted data (e.g. sheet subheadings, spacing rows)

Filters must only have one value. However, you can send that value as `{'top-level': 'value'}` or `{'top-level': ['value']}`. They are equivalent.

The request and response JSONs follow the same format and all fields are required. See [Retrieve Report
Templates](#retrieve-report-templates) for more information on JSON format and expected values.

### HTTP Request

`POST https://api.cubesoftware.com/v1/templates`


## Retrieve One Report Template

```python
import requests

r = requests.get('<url>', headers={
    'Authorization': 'example-access-token'
})
r.json()
```

This endpoint works much like [Retrieve Report Templates](#retrieve-report-templates), but it only fetches one template, given an ID specified in the URL.

See [Retrieve Report Templates](#retrieve-report-templates) for more information on response format and expected values.

### HTTP Request

`GET https://api.cubesoftware.com/v1/templates/<id>`


## Update Report Template

```python
import requests

r = requests.post('<url>', data=data, headers={
    'Authorization': 'example-access-token'
})
# alternatively, you can use requests.put() - equivalent
r.json()
```

> Request also requires a `data` JSON object that looks like this:

```json
{
   "name":"Update My Template",
   "reports":[
      {
         "range":"B1:C5",
         "rows":{
            "Time":[
               "Apr-19",
               "May-19",
               "Jun-19",
               "Q2-19"
            ]
         },
         "columns":{
            "Scenario":[
               "Forecast",
               "Budget"
            ]
         },
         "filters":{
            "Department":[
               "Consolidated Only"
            ],
            "Account":"Revenue"
         }
      },
      {
         "range":"B7:E10",
         "rows":{
            "Account":[
               "Revenue",
               "COGS",
               "Raw COGS",
               "Gross Margin"
            ]
         },
         "columns":{
            "Time":[
               "Q1-19",
               "Q1-19",
               "Q2-19",
               "Q2-19"
            ],
            "Scenario":[
               "Actuals",
               "Budget",
               "Actuals",
               "Budget"
            ]
         },
         "filters":{
            "Department":"Consolidated Only"
         }
      }
   ]
}
```
> And returns a JSON with the same data as hitting the endpoint via GET, including the newly updated Report Template data
> See [Retrieve One Report Template](#retrieve-one-report-template)

This endpoint allows you to update a specific Report Template. It uses much of the same logic as [Create Report Template](#create-report-template), but it updates an existing template rather than creating a new one.

See [Create Report Template](#create-report-template) for details on format and expected values.

### HTTP Request

`POST/PUT https://api.cubesoftware.com/v1/templates/<id>`

# User Settings

User settings contain various defaults tied to users.


## Spreadsheet Settings

```python
import requests

r = requests.get('<url>', headers={
    'Authorization': 'example-access-token'
})
r.json()
```

> The above request returns a JSON structured like this:

```json
{
    "dimensions": {
        "filters": [
            {
                "top_level_id": 18000,
                "selected_id": 20270
            },
            {
                "top_level_id": 18003,
                "selected_id": 18004
            },
            {
                "top_level_id": 18005,
                "selected_id": 18006
            },
            {
                "top_level_id": 18009,
                "selected_id": 18027
            }
        ]
    }
}
```

This endpoint returns reasonable defaults when the user hasn't specified dimensions to use for rows, columns, filters inside of a Cube add-on. These defaults are based on commonly used dimensions for a given top level dimension (i.e. "Actuals" for "Scenario").

### Response Format

Item | Type | Explanation
-----|------|------------
top_level_id | integer | The ID of the top level dimension the default is for
selected_id | integer | The selected dimension (will be a child of the top level dimension)

### HTTP Request

`GET https://api.cubesoftware.com/v1/user/settings/spreadsheets`

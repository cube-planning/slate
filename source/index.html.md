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
    "company": {
        "id": "6a42ac12-ea84-490b-85b9-e80eaf5a72de",
        "created_at": "2019-06-23T16:32:38.249315+00:00",
        "name": "Example Company"
    },
}
```

This endpoint retrieves an access token for a user at a company. It also provides some information about the company the access token was authenticated against for the given user.

Parameter | Description
--------- | -----------
email | The user's email address
password | The password for the user

### HTTP Request

`POST https://portal.cubesoftware.com/api/v1/access_token`

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

`GET https://portal.cubesoftware.com/api/v1/user/companies`

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

`GET https://portal.cubesoftware.com/api/v1/attributes`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
table | none | The ID of an attribute table to limit results to

# Dimensions

Dimensions store the chart of accounts for a company and outline how their data is structured. All companies have top level dimensions called Account, Scenario, Department, and Time. They also can have custom dimensions.

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

`GET https://portal.cubesoftware.com/api/v1/dimensions/tree`

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

`GET https://portal.cubesoftware.com/api/v1/dimensions/deepest`

# OLAP Cube Data

The data stored in the OLAP cube for a company, organized by its dimensions.

## Get Data By Slice

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

This endpoint retrieves values stored in the OLAP cube for a given company. Any record with a `null` ID is a calculated value (i.e. rollups) whereas records with IDs are values explicitly stored in the cube. It is most useful when only one dimension exists in the rows of the data and one dimension exists in the columns of the data.

Data can be narrowed down to various slices by providing dimension paths for top level dimensions (Account, Scenario, etc). These slices can be specified in any of the following ways:

### Top Level Dimension Names With String Path Names Example

Parameter | Description
--------- | -----------
Account | Income Statement
Department | Consolidated Only
Time | 2019:Q1
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

`GET https://portal.cubesoftware.com/api/v1/cube`

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

`POST https://portal.cubesoftware.com/api/v1/cube/pivot`

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

`POST https://portal.cubesoftware.com/api/v1/cube/update`

## Drill Down Into A Value

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

`GET https://portal.cubesoftware.com/api/v1/cube/value`

### Top Level Dimension Names With String Path Names Example

Parameter | Description
--------- | -----------
Account | Income Statement:Revenue:Product Revenue
Department | Consolidated Only
Time | 2019:Q1:Jan-19
Scenario | Actuals


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
        "modified_at": "2019-09-24T17:28:56.739624+00:00",
        "created_by": { "id": "1" },
        "id": "5",
        "created_at": "2019-09-24T17:28:56.739514+00:00",
        "name": "Template #2",
        "reports": [
            {
                "id": "16",
                "sheet_range": "B1:C5",
                "rows": {
                    //Top-level dimension id: [Row dimenion IDs]
                    "10": [ "30", "31", "32", "29", "Can Also Be Text" ]
                },
                "columns": {
                    "6": [ "9", "8" ]
                },
                "filters": {
                    "4": [ "5" ],
                    "1": [ "65" ]
                },
                "values": [
                    [ "1","3" ],
                    [ "20", "50" ],
                    [ "100", "110" ],
                    [ "10", "20" ],
                    [ "22", "34" ]
                ]
            },
            {
                "id": "17",
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
                },
                "values": [
                    [ "12", "34", "99", "54" ],
                    [ "99", "43", "76", "65" ],
                    [ "12", "13", "23", "54" ],
                    [ "65", "56", "34", "32" ]
                ]
            }
        ]
    },
    {
        "modified_at": "2019-09-24T17:46:17.086755+00:00",
        "created_by": {
            "id": "1"
        },
        "id": "7",
        "created_at": "2019-09-24T17:46:17.086632+00:00",
        "name": "Yet Another",
        "reports": [
            {
                "id": "20",
                "sheet_range": "B1:C5",
                "rows": {
                    "10": [ "30", "31", "32", "29", "RANDOM TITLE" ]
                },
                "columns": {
                    "6": [ "9", "8" ]
                },
                "filters": {
                    "4": [ "5" ],
                    "1": [ "65" ]
                },
                "values": [
                    [ "1", "3" ],
                    [ "20", "50" ],
                    [ "100", "110" ],
                    [ "10", "20" ],
                    [ "22", "34" ]
                ]
            },
            {
                "id": "21",
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
                },
                "values": [
                    [ "12", "34", "99", "54" ],
                    [ "99", "43", "76", "65" ],
                    [ "12", "13", "23", "54" ],
                    [ "65", "56", "34", "32" ]
                ]
            }
        ]
    }
]
```

This endpoint fetches all the Report Templates available to the currently authenticated user. This includes all Report Templates anyone at the user's company has created.

Report Templates are made up of one or many reports, each with a defined `sheet_range` where the report should be placed on the sheet.

For rows, columns, and filters in a report, the response is a dictionary in the format `{top level dimension id: [list of individual rows/columns]}`

The individual rows/columns may be IDs of Dimensions in Cube, or they may be user-inputted strings (e.g. section headings, spacing columns/rows)

The `values` key in the report is a snapshot of the values in the report the last time the user saved it. It is NOT current data from the Cube.

### HTTP Request

`GET https://portal.cubesoftware.com/api/v1/templates`


## Create Report Template

```python
import requests

r = requests.post('<url>', data=data, headers={
    'Authorization': 'example-access-token'
})
r.json()
```

> The above request includes a `data` JSON file structured like this:

```json
{
	"name": "A New Template",
	"reports": [
		{
			"range": "B1:C5",
			"rows": {
				"Time": ["Jan-19", "Feb-19", "Mar-19", "Q1-19"]
			},
			"columns": {
				"Scenario": ["Forecast", "Budget"]
			},
			"filters": {
				"Department": ["Consolidated Only"],
				"Account": "Revenue"
			},
			"values": [
				["1", "3"],
				["20", "50"],
				["100", "110"],
				["10", "20"],
				["22", "34"]
			]
		},
		{
			"range": "B7:E10",
			"rows": {
				"Account": ["Revenue", "COGS", "Raw COGS", "Gross Margin"]
			},
			"columns": {
				"Time": ["Q1-19", "Q1-19", "Q2-19", "Q2-19"],
				"Scenario": ["Actuals", "Budget", "Actuals", "Budget"]
			},
			"filters": {
				"Department": "Consolidated Only"
			},
			"values": [
				["12", "34", "99", "54"],
				["99", "43", "76", "65"],
				["12", "13", "23", "54"],
				["65", "56", "34", "32"]
			]
		}
	]
}
```
> And returns a JSON with the same data as hitting the endpoint via GET, including the newly created Report Template
> See [Retrieve Report Templates](#retrieve-report-templates)

This endpoint allows the creation of new Report Templates in the database.

You can send row, column, and filters as dimension names or IDs. The API will convert dimension names into IDs. Row/column name strings that do not match the name of a dimension are saved to preserve user-inputted data (e.g. sheet subheadings, spacing rows)

Filters must only have one value. However, you can send that value as `{'top-level': 'value'}` or `{'top-level': ['value']}`. They are equivalent.


### HTTP Request

`POST https://portal.cubesoftware.com/api/v1/templates`


## Retrieve One Report Template

```python
import requests

r = requests.get('<url>', headers={
    'Authorization': 'example-access-token'
})
r.json()
```

> Response is a JSON object like this:

```json
{
    "modified_at": "2019-09-25T13:37:48.826114+00:00",
    "created_by": {
        "id": "1"
    },
    "id": "11",
    "created_at": "2019-09-25T13:37:48.826007+00:00",
    "name": "Retrieved Template",
    "reports": [
        {
            "id": "26",
            "sheet_range": "B1:C5",
            "rows": {
                "10": [ "30", "31", "32", "29" ]
            },
            "columns": {
                "6": [ "9", "8" ]
            },
            "filters": {
                "4": [ "5" ],
                "1": [ "65" ]
            },
            "values": [
                [ "1", "3" ],
                [ "20", "50" ],
                [ "100", "110" ],
                [ "10", "20" ],
                [ "22", "34" ]
            ]
        },
        {
            "id": "27",
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
            },
            "values": [
                [ "12", "34", "99", "54" ],
                [ "99", "43", "76", "65" ],
                [ "12", "13", "23", "54" ],
                [ "65", "56", "34", "32" ]
            ]
        }
    ]
}
```

This endpoint works much like [Retrieve Report Templates](#retrieve-report-templates), but it only fetches one template, given an ID specified in the URL.

See [Retrieve Report Templates](#retrieve-report-templates) for more information on format and expected values.

### HTTP Request

`GET https://portal.cubesoftware.com/api/v1/templates/<id>`


## Update Existing Report Template

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
	"name": "Update My Template",
	"reports": [
		{
			"range": "B1:C5",
			"rows": {
				"Time": ["Apr-19", "May-19", "Jun-19", "Q2-19"]
			},
			"columns": {
				"Scenario": ["Forecast", "Budget"]
			},
			"filters": {
				"Department": ["Consolidated Only"],
				"Account": "Revenue"
			},
			"values": [
				["1", "3"],
				["20", "50"],
				["100", "110"],
				["10", "20"],
				["22", "34"]
			]
		},
		{
			"range": "B7:E10",
			"rows": {
				"Account": ["Revenue", "COGS", "Raw COGS", "Gross Margin"]
			},
			"columns": {
				"Time": ["Q1-19", "Q1-19", "Q2-19", "Q2-19"],
				"Scenario": ["Actuals", "Budget", "Actuals", "Budget"]
			},
			"filters": {
				"Department": "Consolidated Only"
			},
			"values": [
				["12", "34", "99", "54"],
				["99", "43", "76", "65"],
				["12", "13", "23", "54"],
				["65", "56", "34", "32"]
			]
		}
	]
}
```
> And returns a JSON with the same data as hitting the endpoint via GET, including the newly updated Report Template data
> See [Retrieve One Report Template](#retrieve-one-report-template)

This endpoint allows you to update a specific Report Template. It uses much of the same logic as [Create Report Template](#create-report-template), but it updates an existing template rather than creating a new one.

See [Create Report Template](#create-report-template) for details on format and expected values.

### HTTP Request

`POST` or `PUT https://portal.cubesoftware.com/api/v1/templates/<id>` - equivalent
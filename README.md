# PrimeX API

This API service is the example codes for the PrimeXConnect Backend Developer API and coding challenge.


## Requirements
### 1. Create a new Laravel/Lumen Project.
Used `"laravel/framework": "^8.40"` and `"cloudcreativity/laravel-json-api": "^3.3"` instead.

### 2. Setup a database using the following diagram.
The SQL script file is in the folder of [/sql].

### 3. Create the following API routes (refer to [Resources](#resources) below)
- Add, Update, Delete product.
- Add a stock onHand for a product.
- Able to get products and product details.
- Able to pass optional stock parameter in get products and product details API
- get stock onHand summary.
- Able to sort products by stock onHand by both asc and desc order.
- Able to filter products by stock availability.

### 4, Bulk insert/update
- Able to bulk (5k +) insert/update products into database
- Able to bulk (20k +) insert stock into the database.
``` Note:
This part could be done by using an asynchronous messaging service that decouples services
that produce events from services that process events. E.g. (PubSub, RabbitMQ etc.)

Steps:
1, A publisher (front-end) application creates and sends messages to a topic.
2, Pub/Sub (Cloud) forwards messages from a topic to all of its subscriptions, individually.
3, A subscriber (back-end) deals with the messages via both ways of saving into DB directly
   or sending requests to the endpoints.
```


## Resources

### Products
#### Product create
```
> POST /primex-api/v1/products
```
- Request
```
{
    "data": {
        "type": "products",
        "attributes": {
            "code": "999001",
            "name": "B-ED 999 001",
            "description": "B-ED 999 001"
        }
    }
}
```

- Response with `201`
```
{
    "data": {
        "type": "products",
        "id": "3347",
        "attributes": {
            "code": "999001",
            "name": "B-ED 999 001",
            "on_hand": null,
            "description": "B-ED 999 001",
            "created_at": "2021-05-22 02:51:16",
            "updated_at": "2021-05-22 02:51:16",
            "deleted_at": ""
        },
        "relationships": {
            "stocks": {
                "data": []
            }
        }
    }
}
```

- Response with `422`
```
{
    "errors": [
        {
            "status": "422",
            "title": "Unprocessable Entity",
            "detail": "The code field is required.",
            "source": {
                "pointer": "/data"
            }
        }
    ]
}
```

#### Product get
```
> GET /primex-api/v1/products/3348
```

- Response with `200`
```
{
    "data": {
        "type": "products",
        "id": "3348",
        "attributes": {
            "code": "999001",
            "name": "B-ED 999 001",
            "on_hand": 22,
            "description": "B-ED 999 001",
            "created_at": "2021-05-22 03:00:07",
            "updated_at": "2021-05-22 03:00:30",
            "deleted_at": ""
        },
        "relationships": {
            "stocks": {
                "data": [
                    {
                        "type": "stocks",
                        "id": "7272"
                    },
                    {
                        "type": "stocks",
                        "id": "7273"
                    }
                ]
            }
        }
    }
}
```

#### Product get with including stocks
```
> GET /primex-api/v1/products/3348?include=stocks
```

- Response with `200`
```
{
    "data": {
        "type": "products",
        "id": "3348",
        "attributes": {
            "code": "999001",
            "name": "B-ED 999 001",
            "on_hand": 22,
            "description": "B-ED 999 001",
            "created_at": "2021-05-22 03:00:07",
            "updated_at": "2021-05-22 03:00:30",
            "deleted_at": ""
        },
        "relationships": {
            "stocks": {
                "data": [
                    {
                        "type": "stocks",
                        "id": "7272"
                    },
                    {
                        "type": "stocks",
                        "id": "7273"
                    }
                ]
            }
        }
    },
    "included": [
        {
            "type": "stocks",
            "id": "7272",
            "attributes": {
                "on_hand": "11",
                "taken": "0",
                "production_date": "2020-05-20",
                "created_at": "2021-05-22 03:00:26",
                "updated_at": "2021-05-22 03:00:26"
            },
            "relationships": {
                "products": {
                    "data": {
                        "type": "products",
                        "id": "3348"
                    }
                }
            }
        },
        {
            "type": "stocks",
            "id": "7273",
            "attributes": {
                "on_hand": "11",
                "taken": "0",
                "production_date": "2020-05-20",
                "created_at": "2021-05-22 03:00:30",
                "updated_at": "2021-05-22 03:00:30"
            },
            "relationships": {
                "products": {
                    "data": {
                        "type": "products",
                        "id": "3348"
                    }
                }
            }
        }
    ]
}
```

#### Product stocks get
```
> GET /primex-api/v1/products/3348/stocks
```

- Response with `200`
```
{
    "data": [
        {
            "type": "stocks",
            "id": "7272",
            "attributes": {
                "on_hand": "11",
                "taken": "0",
                "production_date": "2020-05-20",
                "created_at": "2021-05-22 03:00:26",
                "updated_at": "2021-05-22 03:00:26"
            },
            "relationships": {
                "products": {
                    "data": {
                        "type": "products",
                        "id": "3348"
                    }
                }
            }
        },
        {
            "type": "stocks",
            "id": "7273",
            "attributes": {
                "on_hand": "11",
                "taken": "0",
                "production_date": "2020-05-20",
                "created_at": "2021-05-22 03:00:30",
                "updated_at": "2021-05-22 03:00:30"
            },
            "relationships": {
                "products": {
                    "data": {
                        "type": "products",
                        "id": "3348"
                    }
                }
            }
        }
    ]
}
```

#### Products get without trashed records
```
> GET /primex-api/v1/products?page[number]=1&page[size]=5
```

- Response with `200`
```
{
    "meta": {
        "page": {
            "current-page": 1,
            "per-page": 5,
            "from": 1,
            "to": 5,
            "total": 3345,
            "last-page": 669
        }
    },
    "links": {
        "first": "http://service.primex-api.v1.docker/products?page%5Bnumber%5D=1&page%5Bsize%5D=5",
        "next": "http://service.primex-api.v1.docker/products?page%5Bnumber%5D=2&page%5Bsize%5D=5",
        "last": "http://service.primex-api.v1.docker/products?page%5Bnumber%5D=669&page%5Bsize%5D=5"
    },
    "data": [
        {
            "type": "products",
            "id": "2",
            "attributes": {
                "code": "40330",
                "name": "B-INS 100VL IW FZ",
                "on_hand": 0,
                "description": "B-INS 100VL IW FZ",
                "created_at": "2021-05-05 12:00:00",
                "updated_at": "2021-05-05 12:00:00",
                "deleted_at": ""
            },
            "relationships": {
                "stocks": {
                    "data": []
                }
            }
        },
        {
            "type": "products",
            "id": "3",
            "attributes": {
                "code": "40331",
                "name": "B-INS 100VL IW FZ",
                "on_hand": 0,
                "description": "B-INS 100VL IW FZ",
                "created_at": "2021-05-05 12:00:00",
                "updated_at": "2021-05-05 12:00:00",
                "deleted_at": ""
            },
            "relationships": {
                "stocks": {
                    "data": []
                }
            }
        },
        {
            "type": "products",
            "id": "4",
            "attributes": {
                "code": "40350",
                "name": "B-INS 100VL BP FZ",
                "on_hand": 0,
                "description": "B-INS 100VL BP FZ",
                "created_at": "2021-05-05 12:00:00",
                "updated_at": "2021-05-05 12:00:00",
                "deleted_at": ""
            },
            "relationships": {
                "stocks": {
                    "data": []
                }
            }
        },
        {
            "type": "products",
            "id": "5",
            "attributes": {
                "code": "40351",
                "name": "B-INS 100VL BP FZ",
                "on_hand": 0,
                "description": "B-INS 100VL BP FZ",
                "created_at": "2021-05-05 12:00:00",
                "updated_at": "2021-05-05 12:00:00",
                "deleted_at": ""
            },
            "relationships": {
                "stocks": {
                    "data": []
                }
            }
        },
        {
            "type": "products",
            "id": "6",
            "attributes": {
                "code": "40361",
                "name": "B-INS 100VL BP CH EU",
                "on_hand": 0,
                "description": "B-INS 100VL BP CH EU",
                "created_at": "2021-05-05 12:00:00",
                "updated_at": "2021-05-05 12:00:00",
                "deleted_at": ""
            },
            "relationships": {
                "stocks": {
                    "data": []
                }
            }
        }
    ]
}
```

#### Products get with trashed records
```
> GET /primex-api/v1/products?filter[withTrashed]=true&page[number]=1&page[size]=5
```

- Response with `200`
```
{
    "meta": {
        "page": {
            "current-page": 1,
            "per-page": 5,
            "from": 1,
            "to": 5,
            "total": 3346,
            "last-page": 670
        }
    },
    "links": {
        "first": "http://service.primex-api.v1.docker/products?filter%5BwithTrashed%5D=true&page%5Bnumber%5D=1&page%5Bsize%5D=5",
        "next": "http://service.primex-api.v1.docker/products?filter%5BwithTrashed%5D=true&page%5Bnumber%5D=2&page%5Bsize%5D=5",
        "last": "http://service.primex-api.v1.docker/products?filter%5BwithTrashed%5D=true&page%5Bnumber%5D=670&page%5Bsize%5D=5"
    },
    "data": [
        {
            "type": "products",
            "id": "1",
            "attributes": {
                "code": "229113",
                "name": "B-ED PIZZLES BP",
                "on_hand": 0,
                "description": "B-ED PIZZLES BP",
                "created_at": "2021-05-05 12:00:00",
                "updated_at": "2021-05-05 12:00:00",
                "deleted_at": "2021-05-05 13:00:00"
            },
            "relationships": {
                "stocks": {
                    "data": []
                }
            }
        },
        {
            "type": "products",
            "id": "2",
            "attributes": {
                "code": "40330",
                "name": "B-INS 100VL IW FZ",
                "on_hand": 0,
                "description": "B-INS 100VL IW FZ",
                "created_at": "2021-05-05 12:00:00",
                "updated_at": "2021-05-05 12:00:00",
                "deleted_at": ""
            },
            "relationships": {
                "stocks": {
                    "data": []
                }
            }
        },
        {
            "type": "products",
            "id": "3",
            "attributes": {
                "code": "40331",
                "name": "B-INS 100VL IW FZ",
                "on_hand": 0,
                "description": "B-INS 100VL IW FZ",
                "created_at": "2021-05-05 12:00:00",
                "updated_at": "2021-05-05 12:00:00",
                "deleted_at": ""
            },
            "relationships": {
                "stocks": {
                    "data": []
                }
            }
        },
        {
            "type": "products",
            "id": "4",
            "attributes": {
                "code": "40350",
                "name": "B-INS 100VL BP FZ",
                "on_hand": 0,
                "description": "B-INS 100VL BP FZ",
                "created_at": "2021-05-05 12:00:00",
                "updated_at": "2021-05-05 12:00:00",
                "deleted_at": ""
            },
            "relationships": {
                "stocks": {
                    "data": []
                }
            }
        },
        {
            "type": "products",
            "id": "5",
            "attributes": {
                "code": "40351",
                "name": "B-INS 100VL BP FZ",
                "on_hand": 0,
                "description": "B-INS 100VL BP FZ",
                "created_at": "2021-05-05 12:00:00",
                "updated_at": "2021-05-05 12:00:00",
                "deleted_at": ""
            },
            "relationships": {
                "stocks": {
                    "data": []
                }
            }
        }
    ]
}
```

#### Products get only trashed records
```
> GET /primex-api/v1/products?filter[onlyTrashed]=true&page[number]=1&page[size]=5
```

- Response with `200`
```
{
    "meta": {
        "page": {
            "current-page": 1,
            "per-page": 5,
            "from": 1,
            "to": 1,
            "total": 1,
            "last-page": 1
        }
    },
    "links": {
        "first": "http://service.primex-api.v1.docker/products?filter%5BonlyTrashed%5D=true&page%5Bnumber%5D=1&page%5Bsize%5D=5",
        "last": "http://service.primex-api.v1.docker/products?filter%5BonlyTrashed%5D=true&page%5Bnumber%5D=1&page%5Bsize%5D=5"
    },
    "data": [
        {
            "type": "products",
            "id": "1",
            "attributes": {
                "code": "229113",
                "name": "B-ED PIZZLES BP",
                "on_hand": 0,
                "description": "B-ED PIZZLES BP",
                "created_at": "2021-05-05 12:00:00",
                "updated_at": "2021-05-05 12:00:00",
                "deleted_at": "2021-05-05 13:00:00"
            },
            "relationships": {
                "stocks": {
                    "data": []
                }
            }
        }
    ]
}
```

#### Product update
```
> PATCH /primex-api/v1/products/3346
```
- Request
```
{
    "data": {
        "type": "products",
        "id": "3346",
        "attributes": {
            "code": "229113",
            "name": "B-ED PIZZLES BP1",
            "description": "B-ED PIZZLES BP1"
        }
    }
}
```

- Response with `200`
```
{
    "data": {
        "type": "products",
        "id": "3346",
        "attributes": {
            "code": "229113",
            "name": "B-ED PIZZLES BP1",
            "on_hand": 0,
            "description": "B-ED PIZZLES BP1",
            "created_at": "2021-05-22 02:07:13",
            "updated_at": "2021-05-22 02:09:20",
            "deleted_at": ""
        },
        "relationships": {
            "stocks": {
                "data": []
            }
        }
    }
}
```

#### Product update - soft-deleting
```
> PATCH /primex-api/v1/products/3345
```
- Request
```
{
    "data": {
        "type": "products",
        "id": "3345",
        "attributes": {
            "deleted_at": "2021-05-23 23:13:38"
        }
    }
}
```

- Response with `200`
```
{
    "data": {
        "type": "products",
        "id": "3345",
        "attributes": {
            "code": "999001",
            "name": "B-ED 999 001",
            "on_hand": 0,
            "description": "B-ED 999 001",
            "created_at": "2021-05-22 01:26:21",
            "updated_at": "2021-05-22 04:09:17",
            "deleted_at": "2021-05-23 23:13:38"
        },
        "relationships": {
            "stocks": {
                "data": []
            }
        }
    }
}
```

#### Product update - soft-deleting restore
```
> PATCH /primex-api/v1/products/3345
```
- Request
```
{
    "data": {
        "type": "products",
        "id": "3345",
        "attributes": {
            "deleted_at": null
        }
    }
}
```

- Response with `200`
```
{
    "data": {
        "type": "products",
        "id": "3345",
        "attributes": {
            "code": "999001",
            "name": "B-ED 999 001",
            "on_hand": 0,
            "description": "B-ED 999 001",
            "created_at": "2021-05-22 01:26:21",
            "updated_at": "2021-05-22 02:12:23",
            "deleted_at": ""
        },
        "relationships": {
            "stocks": {
                "data": []
            }
        }
    }
}
```

#### Product update - force-deleting
```
> PATCH /primex-api/v1/products/3345
```
- Request
```
```

- Response with `405`: Method Not Allowed
```
{
    "errors": [
        {
            "status": "405",
            "title": "Method Not Allowed",
            "detail": "The DELETE method is not supported for this route. Supported methods: GET, HEAD, PATCH."
        }
    ]
}
```

#### Products get - sort products by stock onHand by both ASC or DESC order
```
DESC:
> GET /primex-api/v1/products?sort=-on_hand&filter[withTrashed]=true&page[number]=1&page[size]=5

ASC:
> GET /primex-api/v1/products?sort=on_hand&filter[withTrashed]=true&page[number]=1&page[size]=5
```

#### Products get - filter products by stock availability
```
Where on_hand = 100:
> GET /primex-api/v1/products?filter[quantity]=100&filter[operator]==&page[number]=1&page[size]=5

Where on_hand > 100:
> GET /primex-api/v1/products?filter[quantity]=100&filter[operator]=>&page[number]=1&page[size]=5

Where on_hand >= 100:
> GET /primex-api/v1/products?filter[quantity]=100&filter[operator]=>=&page[number]=1&page[size]=5

Where on_hand < 100:
> GET /primex-api/v1/products?filter[quantity]=100&filter[operator]=<&page[number]=1&page[size]=5

Where on_hand <= 100:
> GET /primex-api/v1/products?filter[quantity]=100&filter[operator]=<=&page[number]=1&page[size]=5

Where on_hand <> 100:
> GET /primex-api/v1/products?filter[quantity]=100&filter[operator]=<>&page[number]=1&page[size]=5
```


### Stocks
#### Stock create
```
> POST /primex-api/v1/stocks
```
- Request
```
{
    "data": {
        "type": "stocks",
        "attributes": {
            "on_hand": "11",
            "production_date": "2020-05-20"
        },
        "relationships": {
        	"products": {
        		"data": {
        			"type": "products",
        			"id": "3348"
        		}
        	}
        }
    }
}
```

- Response with `201`
```
{
    "data": {
        "type": "stocks",
        "id": "7274",
        "attributes": {
            "on_hand": "11",
            "taken": "",
            "production_date": "2020-05-20",
            "created_at": "2021-05-22 04:28:37",
            "updated_at": "2021-05-22 04:28:37"
        },
        "relationships": {
            "products": {
                "data": {
                    "type": "products",
                    "id": "3348"
                }
            }
        }
    }
}
```

- Response with `422`
```
{
    "errors": [
        {
            "status": "422",
            "title": "Unprocessable Entity",
            "detail": "The on hand field is required.",
            "source": {
                "pointer": "/data"
            }
        },
        {
            "status": "422",
            "title": "Unprocessable Entity",
            "detail": "The production date does not match the format Y-m-d.",
            "source": {
                "pointer": "/data/attributes/production_date"
            }
        },
        {
            "status": "422",
            "title": "Unprocessable Entity",
            "detail": "The products.id field is required.",
            "source": {
                "pointer": "/data"
            }
        }
    ]
}
```


## Built With

Detail any key frameworks or libraries

- [laravel 8](https://laravel.com)
- [laravel JSON:API plugin](https://laravel-json-api.readthedocs.io/en/latest/)

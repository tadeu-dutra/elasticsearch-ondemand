# 1.1 Stack Introduction

In this lab, you will examine the eCommerce sample data that ships with Kibana by performing the following tasks:

    - Add a dataset in Kibana
    - Examine the indexed documents using Discover
    - View the pre-set visualizations in a sample dataset Dashboard
    - From the Console in Dev Tools, send HTTP requests to Elasticsearch and view the responses


## 1. BASIC HTTP REQUEST TO ELASTICSEARCH

### REQUEST:

```
GET /
```

### RESPONSE:

```
{
  "name": "node1",
  "cluster_name": "cluster1",
  "cluster_uuid": "aHOxbSEaRwa-mLEGEM7mTA",
  "version": {
    "number": "8.15.3",
    "build_flavor": "default",
    "build_type": "docker",
    "build_hash": "f97532e680b555c3a05e73a74c28afb666923018",
    "build_date": "2024-10-09T22:08:00.328917561Z",
    "build_snapshot": false,
    "lucene_version": "9.11.1",
    "minimum_wire_compatibility_version": "7.17.0",
    "minimum_index_compatibility_version": "7.0.0"
  },
  "tagline": "You Know, for Search"
}
```

## 2. The GET _cat/indices/*,-.*?v request displays the current non system indexes in the cluster. You should see the sample eCommerce data you added earlier.

### REQUEST:

```
GET _cat/indices/*,-.*?v
```

```
health status index                        uuid                   pri rep docs.count docs.deleted store.size pri.store.size dataset.size
green  open   replicated_blogs             9xoEUMSxT3CXWKnZgaWR4w   1   1          7            0    169.6kb         84.8kb       84.8kb
green  open   web_traffic                  R4beQxn5QAS1UeUGUsgnVg   1   1    1324128            0    620.1mb          310mb        310mb
green  open   blogs                        XONGA8UASaeiQHXjTV9M8A   1   2       3687            0       82mb         27.3mb       27.3mb
green  open   kibana_sample_data_ecommerce gx9TlJ3BQOynPMJHA71dhw   1   1       4675            0        9mb          4.5mb        4.5mb
```

## 3. SEARCH ON kibana_sample_data_ecommerce SAMPLE DATA
NOTE: Paging down through the results, you will see that the search returned the _source of 10 documents. In the following view, the hits have been collapsed using the arrows next to the line numbers.

```
GET kibana_sample_data_ecommerce/_search
```

```
{
  "took": 2,
  "timed_out": false,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": {
      "value": 4675,
      "relation": "eq"
    },
    "max_score": 1,
    "hits": [
      {
        "_index": "kibana_sample_data_ecommerce",
        "_id": "a_nAWpcBS5KuoUwimJsw",
        "_score": 1,
        "_source": {
          "category": [
            "Men's Clothing"
          ],
          "currency": "EUR",
          "customer_first_name": "Eddie",
          "customer_full_name": "Eddie Underwood",
          "customer_gender": "MALE",
          "customer_id": 38,
          "customer_last_name": "Underwood",
          "customer_phone": "",
          "day_of_week": "Monday",
          "day_of_week_i": 0,
          "email": "eddie@underwood-family.zzz",
          "manufacturer": [
            "Elitelligence",
            "Oceanavigations"
          ],
          "order_date": "2025-06-23T09:28:48+00:00",
          "order_id": 584677,
          "products": [
            {
              "base_price": 11.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Elitelligence",
              "tax_amount": 0,
              "product_id": 6283,
              "category": "Men's Clothing",
              "sku": "ZO0549605496",
              "taxless_price": 11.99,
              "unit_discount_amount": 0,
              "min_price": 6.35,
              "_id": "sold_product_584677_6283",
              "discount_amount": 0,
              "created_on": "2016-12-26T09:28:48+00:00",
              "product_name": "Basic T-shirt - dark blue/white",
              "price": 11.99,
              "taxful_price": 11.99,
              "base_unit_price": 11.99
            },
            {
              "base_price": 24.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Oceanavigations",
              "tax_amount": 0,
              "product_id": 19400,
              "category": "Men's Clothing",
              "sku": "ZO0299602996",
              "taxless_price": 24.99,
              "unit_discount_amount": 0,
              "min_price": 11.75,
              "_id": "sold_product_584677_19400",
              "discount_amount": 0,
              "created_on": "2016-12-26T09:28:48+00:00",
              "product_name": "Sweatshirt - grey multicolor",
              "price": 24.99,
              "taxful_price": 24.99,
              "base_unit_price": 24.99
            }
          ],
          "sku": [
            "ZO0549605496",
            "ZO0299602996"
          ],
          "taxful_total_price": 36.98,
          "taxless_total_price": 36.98,
          "total_quantity": 2,
          "total_unique_products": 2,
          "type": "order",
          "user": "eddie",
          "geoip": {
            "country_iso_code": "EG",
            "location": {
              "lon": 31.3,
              "lat": 30.1
            },
            "region_name": "Cairo Governorate",
            "continent_name": "Africa",
            "city_name": "Cairo"
          },
          "event": {
            "dataset": "sample_ecommerce"
          }
        }
      },
      {
        "_index": "kibana_sample_data_ecommerce",
        "_id": "bPnAWpcBS5KuoUwimJsw",
        "_score": 1,
        "_source": {
          "category": [
            "Women's Clothing"
          ],
          "currency": "EUR",
          "customer_first_name": "Mary",
          "customer_full_name": "Mary Bailey",
          "customer_gender": "FEMALE",
          "customer_id": 20,
          "customer_last_name": "Bailey",
          "customer_phone": "",
          "day_of_week": "Sunday",
          "day_of_week_i": 6,
          "email": "mary@bailey-family.zzz",
          "manufacturer": [
            "Champion Arts",
            "Pyramidustries"
          ],
          "order_date": "2025-06-22T21:59:02+00:00",
          "order_id": 584021,
          "products": [
            {
              "base_price": 24.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Champion Arts",
              "tax_amount": 0,
              "product_id": 11238,
              "category": "Women's Clothing",
              "sku": "ZO0489604896",
              "taxless_price": 24.99,
              "unit_discount_amount": 0,
              "min_price": 11.75,
              "_id": "sold_product_584021_11238",
              "discount_amount": 0,
              "created_on": "2016-12-25T21:59:02+00:00",
              "product_name": "Denim dress - black denim",
              "price": 24.99,
              "taxful_price": 24.99,
              "base_unit_price": 24.99
            },
            {
              "base_price": 28.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Pyramidustries",
              "tax_amount": 0,
              "product_id": 20149,
              "category": "Women's Clothing",
              "sku": "ZO0185501855",
              "taxless_price": 28.99,
              "unit_discount_amount": 0,
              "min_price": 15.65,
              "_id": "sold_product_584021_20149",
              "discount_amount": 0,
              "created_on": "2016-12-25T21:59:02+00:00",
              "product_name": "Shorts - black",
              "price": 28.99,
              "taxful_price": 28.99,
              "base_unit_price": 28.99
            }
          ],
          "sku": [
            "ZO0489604896",
            "ZO0185501855"
          ],
          "taxful_total_price": 53.98,
          "taxless_total_price": 53.98,
          "total_quantity": 2,
          "total_unique_products": 2,
          "type": "order",
          "user": "mary",
          "geoip": {
            "country_iso_code": "AE",
            "location": {
              "lon": 55.3,
              "lat": 25.3
            },
            "region_name": "Dubai",
            "continent_name": "Asia",
            "city_name": "Dubai"
          },
          "event": {
            "dataset": "sample_ecommerce"
          }
        }
      },
      {
        "_index": "kibana_sample_data_ecommerce",
        "_id": "bfnAWpcBS5KuoUwimJsw",
        "_score": 1,
        "_source": {
          "category": [
            "Women's Shoes",
            "Women's Clothing"
          ],
          "currency": "EUR",
          "customer_first_name": "Gwen",
          "customer_full_name": "Gwen Butler",
          "customer_gender": "FEMALE",
          "customer_id": 26,
          "customer_last_name": "Butler",
          "customer_phone": "",
          "day_of_week": "Sunday",
          "day_of_week_i": 6,
          "email": "gwen@butler-family.zzz",
          "manufacturer": [
            "Low Tide Media",
            "Oceanavigations"
          ],
          "order_date": "2025-06-22T22:32:10+00:00",
          "order_id": 584058,
          "products": [
            {
              "base_price": 99.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Low Tide Media",
              "tax_amount": 0,
              "product_id": 22794,
              "category": "Women's Shoes",
              "sku": "ZO0374603746",
              "taxless_price": 99.99,
              "unit_discount_amount": 0,
              "min_price": 46.01,
              "_id": "sold_product_584058_22794",
              "discount_amount": 0,
              "created_on": "2016-12-25T22:32:10+00:00",
              "product_name": "Boots - Midnight Blue",
              "price": 99.99,
              "taxful_price": 99.99,
              "base_unit_price": 99.99
            },
            {
              "base_price": 99.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Oceanavigations",
              "tax_amount": 0,
              "product_id": 23386,
              "category": "Women's Clothing",
              "sku": "ZO0272202722",
              "taxless_price": 99.99,
              "unit_discount_amount": 0,
              "min_price": 53.99,
              "_id": "sold_product_584058_23386",
              "discount_amount": 0,
              "created_on": "2016-12-25T22:32:10+00:00",
              "product_name": "Short coat - white/black",
              "price": 99.99,
              "taxful_price": 99.99,
              "base_unit_price": 99.99
            }
          ],
          "sku": [
            "ZO0374603746",
            "ZO0272202722"
          ],
          "taxful_total_price": 199.98,
          "taxless_total_price": 199.98,
          "total_quantity": 2,
          "total_unique_products": 2,
          "type": "order",
          "user": "gwen",
          "geoip": {
            "country_iso_code": "US",
            "location": {
              "lon": -118.2,
              "lat": 34.1
            },
            "region_name": "California",
            "continent_name": "North America",
            "city_name": "Los Angeles"
          },
          "event": {
            "dataset": "sample_ecommerce"
          }
        }
      },
      {
        "_index": "kibana_sample_data_ecommerce",
        "_id": "bvnAWpcBS5KuoUwimJsw",
        "_score": 1,
        "_source": {
          "category": [
            "Women's Shoes",
            "Women's Clothing"
          ],
          "currency": "EUR",
          "customer_first_name": "Diane",
          "customer_full_name": "Diane Chandler",
          "customer_gender": "FEMALE",
          "customer_id": 22,
          "customer_last_name": "Chandler",
          "customer_phone": "",
          "day_of_week": "Sunday",
          "day_of_week_i": 6,
          "email": "diane@chandler-family.zzz",
          "manufacturer": [
            "Primemaster",
            "Oceanavigations"
          ],
          "order_date": "2025-06-22T22:58:05+00:00",
          "order_id": 584093,
          "products": [
            {
              "base_price": 74.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Primemaster",
              "tax_amount": 0,
              "product_id": 12304,
              "category": "Women's Shoes",
              "sku": "ZO0360303603",
              "taxless_price": 74.99,
              "unit_discount_amount": 0,
              "min_price": 34.5,
              "_id": "sold_product_584093_12304",
              "discount_amount": 0,
              "created_on": "2016-12-25T22:58:05+00:00",
              "product_name": "High heeled sandals - argento",
              "price": 74.99,
              "taxful_price": 74.99,
              "base_unit_price": 74.99
            },
            {
              "base_price": 99.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Oceanavigations",
              "tax_amount": 0,
              "product_id": 19587,
              "category": "Women's Clothing",
              "sku": "ZO0272002720",
              "taxless_price": 99.99,
              "unit_discount_amount": 0,
              "min_price": 47.01,
              "_id": "sold_product_584093_19587",
              "discount_amount": 0,
              "created_on": "2016-12-25T22:58:05+00:00",
              "product_name": "Classic coat - black",
              "price": 99.99,
              "taxful_price": 99.99,
              "base_unit_price": 99.99
            }
          ],
          "sku": [
            "ZO0360303603",
            "ZO0272002720"
          ],
          "taxful_total_price": 174.98,
          "taxless_total_price": 174.98,
          "total_quantity": 2,
          "total_unique_products": 2,
          "type": "order",
          "user": "diane",
          "geoip": {
            "country_iso_code": "GB",
            "location": {
              "lon": -0.1,
              "lat": 51.5
            },
            "continent_name": "Europe"
          },
          "event": {
            "dataset": "sample_ecommerce"
          }
        }
      },
      {
        "_index": "kibana_sample_data_ecommerce",
        "_id": "b_nAWpcBS5KuoUwimJsw",
        "_score": 1,
        "_source": {
          "category": [
            "Men's Clothing",
            "Men's Accessories"
          ],
          "currency": "EUR",
          "customer_first_name": "Eddie",
          "customer_full_name": "Eddie Weber",
          "customer_gender": "MALE",
          "customer_id": 38,
          "customer_last_name": "Weber",
          "customer_phone": "",
          "day_of_week": "Monday",
          "day_of_week_i": 0,
          "email": "eddie@weber-family.zzz",
          "manufacturer": [
            "Elitelligence"
          ],
          "order_date": "2025-06-16T03:48:58+00:00",
          "order_id": 574916,
          "products": [
            {
              "base_price": 59.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Elitelligence",
              "tax_amount": 0,
              "product_id": 11262,
              "category": "Men's Clothing",
              "sku": "ZO0542505425",
              "taxless_price": 59.99,
              "unit_discount_amount": 0,
              "min_price": 28.2,
              "_id": "sold_product_574916_11262",
              "discount_amount": 0,
              "created_on": "2016-12-19T03:48:58+00:00",
              "product_name": "Winter jacket - black",
              "price": 59.99,
              "taxful_price": 59.99,
              "base_unit_price": 59.99
            },
            {
              "base_price": 20.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Elitelligence",
              "tax_amount": 0,
              "product_id": 15713,
              "category": "Men's Accessories",
              "sku": "ZO0601306013",
              "taxless_price": 20.99,
              "unit_discount_amount": 0,
              "min_price": 10.7,
              "_id": "sold_product_574916_15713",
              "discount_amount": 0,
              "created_on": "2016-12-19T03:48:58+00:00",
              "product_name": "Watch - green",
              "price": 20.99,
              "taxful_price": 20.99,
              "base_unit_price": 20.99
            }
          ],
          "sku": [
            "ZO0542505425",
            "ZO0601306013"
          ],
          "taxful_total_price": 80.98,
          "taxless_total_price": 80.98,
          "total_quantity": 2,
          "total_unique_products": 2,
          "type": "order",
          "user": "eddie",
          "geoip": {
            "country_iso_code": "EG",
            "location": {
              "lon": 31.3,
              "lat": 30.1
            },
            "region_name": "Cairo Governorate",
            "continent_name": "Africa",
            "city_name": "Cairo"
          },
          "event": {
            "dataset": "sample_ecommerce"
          }
        }
      },
      {
        "_index": "kibana_sample_data_ecommerce",
        "_id": "cPnAWpcBS5KuoUwimJsw",
        "_score": 1,
        "_source": {
          "category": [
            "Women's Shoes",
            "Women's Clothing"
          ],
          "currency": "EUR",
          "customer_first_name": "Diane",
          "customer_full_name": "Diane Goodwin",
          "customer_gender": "FEMALE",
          "customer_id": 22,
          "customer_last_name": "Goodwin",
          "customer_phone": "",
          "day_of_week": "Sunday",
          "day_of_week_i": 6,
          "email": "diane@goodwin-family.zzz",
          "manufacturer": [
            "Low Tide Media",
            "Pyramidustries"
          ],
          "order_date": "2025-06-15T21:44:38+00:00",
          "order_id": 574586,
          "products": [
            {
              "base_price": 59.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Low Tide Media",
              "tax_amount": 0,
              "product_id": 5419,
              "category": "Women's Shoes",
              "sku": "ZO0376303763",
              "taxless_price": 59.99,
              "unit_discount_amount": 0,
              "min_price": 31.79,
              "_id": "sold_product_574586_5419",
              "discount_amount": 0,
              "created_on": "2016-12-18T21:44:38+00:00",
              "product_name": "Winter boots - brown",
              "price": 59.99,
              "taxful_price": 59.99,
              "base_unit_price": 59.99
            },
            {
              "base_price": 11.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Pyramidustries",
              "tax_amount": 0,
              "product_id": 19325,
              "category": "Women's Clothing",
              "sku": "ZO0212402124",
              "taxless_price": 11.99,
              "unit_discount_amount": 0,
              "min_price": 6.47,
              "_id": "sold_product_574586_19325",
              "discount_amount": 0,
              "created_on": "2016-12-18T21:44:38+00:00",
              "product_name": "Shorts - dark blue/pink/dark green",
              "price": 11.99,
              "taxful_price": 11.99,
              "base_unit_price": 11.99
            }
          ],
          "sku": [
            "ZO0376303763",
            "ZO0212402124"
          ],
          "taxful_total_price": 71.98,
          "taxless_total_price": 71.98,
          "total_quantity": 2,
          "total_unique_products": 2,
          "type": "order",
          "user": "diane",
          "geoip": {
            "country_iso_code": "GB",
            "location": {
              "lon": -0.1,
              "lat": 51.5
            },
            "continent_name": "Europe"
          },
          "event": {
            "dataset": "sample_ecommerce"
          }
        }
      },
      {
        "_index": "kibana_sample_data_ecommerce",
        "_id": "cfnAWpcBS5KuoUwimJsw",
        "_score": 1,
        "_source": {
          "category": [
            "Men's Clothing"
          ],
          "currency": "EUR",
          "customer_first_name": "Oliver",
          "customer_full_name": "Oliver Rios",
          "customer_gender": "MALE",
          "customer_id": 7,
          "customer_last_name": "Rios",
          "customer_phone": "",
          "day_of_week": "Monday",
          "day_of_week_i": 0,
          "email": "oliver@rios-family.zzz",
          "manufacturer": [
            "Low Tide Media",
            "Elitelligence"
          ],
          "order_date": "2025-06-09T09:27:22+00:00",
          "order_id": 565855,
          "products": [
            {
              "base_price": 20.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Low Tide Media",
              "tax_amount": 0,
              "product_id": 19919,
              "category": "Men's Clothing",
              "sku": "ZO0417504175",
              "taxless_price": 20.99,
              "unit_discount_amount": 0,
              "min_price": 9.87,
              "_id": "sold_product_565855_19919",
              "discount_amount": 0,
              "created_on": "2016-12-12T09:27:22+00:00",
              "product_name": "Shirt - dark blue white",
              "price": 20.99,
              "taxful_price": 20.99,
              "base_unit_price": 20.99
            },
            {
              "base_price": 24.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Elitelligence",
              "tax_amount": 0,
              "product_id": 24502,
              "category": "Men's Clothing",
              "sku": "ZO0535205352",
              "taxless_price": 24.99,
              "unit_discount_amount": 0,
              "min_price": 12.49,
              "_id": "sold_product_565855_24502",
              "discount_amount": 0,
              "created_on": "2016-12-12T09:27:22+00:00",
              "product_name": "Slim fit jeans - raw blue",
              "price": 24.99,
              "taxful_price": 24.99,
              "base_unit_price": 24.99
            }
          ],
          "sku": [
            "ZO0417504175",
            "ZO0535205352"
          ],
          "taxful_total_price": 45.98,
          "taxless_total_price": 45.98,
          "total_quantity": 2,
          "total_unique_products": 2,
          "type": "order",
          "user": "oliver",
          "geoip": {
            "country_iso_code": "GB",
            "location": {
              "lon": -0.1,
              "lat": 51.5
            },
            "continent_name": "Europe"
          },
          "event": {
            "dataset": "sample_ecommerce"
          }
        }
      },
      {
        "_index": "kibana_sample_data_ecommerce",
        "_id": "cvnAWpcBS5KuoUwimJsw",
        "_score": 1,
        "_source": {
          "category": [
            "Men's Clothing",
            "Men's Accessories",
            "Men's Shoes"
          ],
          "currency": "EUR",
          "customer_first_name": "Abd",
          "customer_full_name": "Abd Sutton",
          "customer_gender": "MALE",
          "customer_id": 52,
          "customer_last_name": "Sutton",
          "customer_phone": "",
          "day_of_week": "Monday",
          "day_of_week_i": 0,
          "email": "abd@sutton-family.zzz",
          "manufacturer": [
            "Low Tide Media",
            "Oceanavigations",
            "Elitelligence"
          ],
          "order_date": "2025-06-23T02:19:41+00:00",
          "order_id": 723055,
          "products": [
            {
              "base_price": 28.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Low Tide Media",
              "tax_amount": 0,
              "product_id": 11751,
              "category": "Men's Clothing",
              "sku": "ZO0423104231",
              "taxless_price": 28.99,
              "unit_discount_amount": 0,
              "min_price": 14.78,
              "_id": "sold_product_723055_11751",
              "discount_amount": 0,
              "created_on": "2016-12-26T02:19:41+00:00",
              "product_name": "Shorts - beige",
              "price": 28.99,
              "taxful_price": 28.99,
              "base_unit_price": 28.99
            },
            {
              "base_price": 41.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Oceanavigations",
              "tax_amount": 0,
              "product_id": 13452,
              "category": "Men's Accessories",
              "sku": "ZO0314203142",
              "taxless_price": 41.99,
              "unit_discount_amount": 0,
              "min_price": 20.58,
              "_id": "sold_product_723055_13452",
              "discount_amount": 0,
              "created_on": "2016-12-26T02:19:41+00:00",
              "product_name": "Weekend bag - black",
              "price": 41.99,
              "taxful_price": 41.99,
              "base_unit_price": 41.99
            },
            {
              "base_price": 59.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Low Tide Media",
              "tax_amount": 0,
              "product_id": 22434,
              "category": "Men's Shoes",
              "sku": "ZO0394403944",
              "taxless_price": 59.99,
              "unit_discount_amount": 0,
              "min_price": 30.01,
              "_id": "sold_product_723055_22434",
              "discount_amount": 0,
              "created_on": "2016-12-26T02:19:41+00:00",
              "product_name": "Casual lace-ups - cognac",
              "price": 59.99,
              "taxful_price": 59.99,
              "base_unit_price": 59.99
            },
            {
              "base_price": 7.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Elitelligence",
              "tax_amount": 0,
              "product_id": 7059,
              "category": "Men's Clothing",
              "sku": "ZO0561805618",
              "taxless_price": 7.99,
              "unit_discount_amount": 0,
              "min_price": 3.76,
              "_id": "sold_product_723055_7059",
              "discount_amount": 0,
              "created_on": "2016-12-26T02:19:41+00:00",
              "product_name": "Print T-shirt - off white/green",
              "price": 7.99,
              "taxful_price": 7.99,
              "base_unit_price": 7.99
            }
          ],
          "sku": [
            "ZO0423104231",
            "ZO0314203142",
            "ZO0394403944",
            "ZO0561805618"
          ],
          "taxful_total_price": 138.96,
          "taxless_total_price": 138.96,
          "total_quantity": 4,
          "total_unique_products": 4,
          "type": "order",
          "user": "abd",
          "geoip": {
            "country_iso_code": "EG",
            "location": {
              "lon": 31.3,
              "lat": 30.1
            },
            "region_name": "Cairo Governorate",
            "continent_name": "Africa",
            "city_name": "Cairo"
          },
          "event": {
            "dataset": "sample_ecommerce"
          }
        }
      },
      {
        "_index": "kibana_sample_data_ecommerce",
        "_id": "c_nAWpcBS5KuoUwimJsw",
        "_score": 1,
        "_source": {
          "category": [
            "Women's Accessories",
            "Women's Clothing"
          ],
          "currency": "EUR",
          "customer_first_name": "Wilhemina St.",
          "customer_full_name": "Wilhemina St. Tran",
          "customer_gender": "FEMALE",
          "customer_id": 17,
          "customer_last_name": "Tran",
          "customer_phone": "",
          "day_of_week": "Sunday",
          "day_of_week_i": 6,
          "email": "wilhemina st.@tran-family.zzz",
          "manufacturer": [
            "Pyramidustries",
            "Tigress Enterprises",
            "Pyramidustries active",
            "Oceanavigations"
          ],
          "order_date": "2025-06-15T00:59:02+00:00",
          "order_id": 727462,
          "products": [
            {
              "base_price": 8.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Pyramidustries",
              "tax_amount": 0,
              "product_id": 12012,
              "category": "Women's Accessories",
              "sku": "ZO0186801868",
              "taxless_price": 8.99,
              "unit_discount_amount": 0,
              "min_price": 4.14,
              "_id": "sold_product_727462_12012",
              "discount_amount": 0,
              "created_on": "2016-12-18T00:59:02+00:00",
              "product_name": "Mittens - grey",
              "price": 8.99,
              "taxful_price": 8.99,
              "base_unit_price": 8.99
            },
            {
              "base_price": 20.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Tigress Enterprises",
              "tax_amount": 0,
              "product_id": 16471,
              "category": "Women's Clothing",
              "sku": "ZO0074100741",
              "taxless_price": 20.99,
              "unit_discount_amount": 0,
              "min_price": 9.66,
              "_id": "sold_product_727462_16471",
              "discount_amount": 0,
              "created_on": "2016-12-18T00:59:02+00:00",
              "product_name": "Vest - black/Chocolate",
              "price": 20.99,
              "taxful_price": 20.99,
              "base_unit_price": 20.99
            },
            {
              "base_price": 16.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Pyramidustries active",
              "tax_amount": 0,
              "product_id": 10554,
              "category": "Women's Clothing",
              "sku": "ZO0223202232",
              "taxless_price": 16.99,
              "unit_discount_amount": 0,
              "min_price": 7.82,
              "_id": "sold_product_727462_10554",
              "discount_amount": 0,
              "created_on": "2016-12-18T00:59:02+00:00",
              "product_name": "Tracksuit bottoms - Pale Violet Red",
              "price": 16.99,
              "taxful_price": 16.99,
              "base_unit_price": 16.99
            },
            {
              "base_price": 41.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Oceanavigations",
              "tax_amount": 0,
              "product_id": 22882,
              "category": "Women's Clothing",
              "sku": "ZO0267602676",
              "taxless_price": 41.99,
              "unit_discount_amount": 0,
              "min_price": 23.09,
              "_id": "sold_product_727462_22882",
              "discount_amount": 0,
              "created_on": "2016-12-18T00:59:02+00:00",
              "product_name": "Cardigan - black",
              "price": 41.99,
              "taxful_price": 41.99,
              "base_unit_price": 41.99
            }
          ],
          "sku": [
            "ZO0186801868",
            "ZO0074100741",
            "ZO0223202232",
            "ZO0267602676"
          ],
          "taxful_total_price": 88.96,
          "taxless_total_price": 88.96,
          "total_quantity": 4,
          "total_unique_products": 4,
          "type": "order",
          "user": "wilhemina",
          "geoip": {
            "country_iso_code": "MC",
            "location": {
              "lon": 7.4,
              "lat": 43.7
            },
            "continent_name": "Europe",
            "city_name": "Monte Carlo"
          },
          "event": {
            "dataset": "sample_ecommerce"
          }
        }
      },
      {
        "_index": "kibana_sample_data_ecommerce",
        "_id": "dPnAWpcBS5KuoUwimJsw",
        "_score": 1,
        "_source": {
          "category": [
            "Women's Shoes",
            "Women's Clothing"
          ],
          "currency": "EUR",
          "customer_first_name": "Rabbia Al",
          "customer_full_name": "Rabbia Al Baker",
          "customer_gender": "FEMALE",
          "customer_id": 5,
          "customer_last_name": "Baker",
          "customer_phone": "",
          "day_of_week": "Monday",
          "day_of_week_i": 0,
          "email": "rabbia al@baker-family.zzz",
          "manufacturer": [
            "Oceanavigations",
            "Tigress Enterprises",
            "Champion Arts",
            "Pyramidustries"
          ],
          "order_date": "2025-06-23T03:41:46+00:00",
          "order_id": 727269,
          "products": [
            {
              "base_price": 74.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Oceanavigations",
              "tax_amount": 0,
              "product_id": 22880,
              "category": "Women's Shoes",
              "sku": "ZO0249902499",
              "taxless_price": 74.99,
              "unit_discount_amount": 0,
              "min_price": 35.25,
              "_id": "sold_product_727269_22880",
              "discount_amount": 0,
              "created_on": "2016-12-26T03:41:46+00:00",
              "product_name": "Boots - black",
              "price": 74.99,
              "taxful_price": 74.99,
              "base_unit_price": 74.99
            },
            {
              "base_price": 32.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Tigress Enterprises",
              "tax_amount": 0,
              "product_id": 12484,
              "category": "Women's Clothing",
              "sku": "ZO0068400684",
              "taxless_price": 32.99,
              "unit_discount_amount": 0,
              "min_price": 17.48,
              "_id": "sold_product_727269_12484",
              "discount_amount": 0,
              "created_on": "2016-12-26T03:41:46+00:00",
              "product_name": "Cardigan - black/white",
              "price": 32.99,
              "taxful_price": 32.99,
              "base_unit_price": 32.99
            },
            {
              "base_price": 13.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Champion Arts",
              "tax_amount": 0,
              "product_id": 21362,
              "category": "Women's Clothing",
              "sku": "ZO0494704947",
              "taxless_price": 13.99,
              "unit_discount_amount": 0,
              "min_price": 6.58,
              "_id": "sold_product_727269_21362",
              "discount_amount": 0,
              "created_on": "2016-12-26T03:41:46+00:00",
              "product_name": "Long sleeved top - blue",
              "price": 13.99,
              "taxful_price": 13.99,
              "base_unit_price": 13.99
            },
            {
              "base_price": 49.99,
              "discount_percentage": 0,
              "quantity": 1,
              "manufacturer": "Pyramidustries",
              "tax_amount": 0,
              "product_id": 13854,
              "category": "Women's Shoes",
              "sku": "ZO0143401434",
              "taxless_price": 49.99,
              "unit_discount_amount": 0,
              "min_price": 25.49,
              "_id": "sold_product_727269_13854",
              "discount_amount": 0,
              "created_on": "2016-12-26T03:41:46+00:00",
              "product_name": "Lace-up boots - black",
              "price": 49.99,
              "taxful_price": 49.99,
              "base_unit_price": 49.99
            }
          ],
          "sku": [
            "ZO0249902499",
            "ZO0068400684",
            "ZO0494704947",
            "ZO0143401434"
          ],
          "taxful_total_price": 171.96,
          "taxless_total_price": 171.96,
          "total_quantity": 4,
          "total_unique_products": 4,
          "type": "order",
          "user": "rabbia",
          "geoip": {
            "country_iso_code": "AE",
            "location": {
              "lon": 55.3,
              "lat": 25.3
            },
            "region_name": "Dubai",
            "continent_name": "Asia",
            "city_name": "Dubai"
          },
          "event": {
            "dataset": "sample_ecommerce"
          }
        }
      }
    ]
  }
}
```

# Summary

In this lab, you examined the eCommerce sample data that ships with Kibana by performing the following tasks:

    Add a dataset in Kibana
    Examine the indexed documents using Discover
    View the pre-set visualizations in a sample dataset Dashboard
    From the Console in Dev Tools, send HTTP requests to Elasticsearch and view the responses

# Review

- Elasticsearch is the core of the Elastic Stack and is used to store, search, and analyze data.
- A node is a single instance of Elasticsearch.
- You can use Kibana Discover to query and filter your data.


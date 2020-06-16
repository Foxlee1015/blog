---
layout: post
title: Elasticsearch-aggregation
date: 2020-06-17 00:00:00 +0300
description: "Elasticsearch-aggregation"
tags: [elasticsearch]
img: elasticsearch.png
---

# Aggregation

## bucket

{% highlight ruby %}

* 필드(텍스트) 값으로 bucket 생성 
{
  "size": 0,
  "aggs": {
    "status_terms": {
      "terms": {
        "field": "status.keyword",    // status의 데이터를 기준으로 구분됨
        "size": 20,                    // default size = 10
        "min_doc_count": 0,
        "order": {
          "_key": "asc"               // 집계의 buckets 들의 이름 순서로 정렬
        }
      }
    }
  }
}

* 해당 필드(status)가 없거나 null 값을 가진 문서 집계 대상에 추가하기( key="N/A" 인 bucket 생성됨)
{
  "size": 0,
  "aggs": {
    "status_terms": {
      "terms": {
        "field": "status.keyword",
        "size": 20,
        "missing": "N/A"
      }
    }
  }
}

* 해당 필드(status)가 없거나 null 인 문서를 대상으로 집계
{
  "size": 0,
  "aggs": {
    "orders_without_status": {
      "missing": {
        "field": "status.keyword"
      },
      "aggs": {                                 # status 가 없거나 null 문서들의 total_amount 전체 합
        "missing_sum": {
          "sum": {
            "field": "total_amount"
          }
        }
      }
    }
  }
}

{% endhighlight %}

## Metric

{% highlight ruby %}

{
  "size": 0,
  "aggs": {
    "total_sales": {
      "sum": {
        "field": "total_amount"
      }
    },
    "avg_sale": {
      "avg": {
        "field": "total_amount"
      }
    },
    "min_sale": {
      "min": {
        "field": "total_amount"
      }
    },
    "max_sale": {
      "max": {
        "field": "total_amount"
      }
    }
  }
}

* Retrieving the number of distinct values
{
  "size": 0,
  "aggs": {
    "total_salesmen": {
      "cardinality": {                   
        "field": "salesman.id"
      }
    }
  }
}

* Retrieving the number of values
{
  "size": 0,
  "aggs": {
    "values_count": {
      "value_count": {
        "field": "total_amount"
      }
    }
  }
}

{% endhighlight %}

# Histograms - Interval

{% highlight ruby %}

{
  "size": 0,
  "aggs": {
    "amount_distribution": {
      "histogram": {
        "field": "total_amount",
        "interval": 25,                        // total_amount 25씩 bucket 생성 
        "min_doc_count": 0,                    // min_doc_count 이상 기준 (1이라면 doc_count가 0인 bucket은 집계에 나오지 않음)
        "extended_bounds": {                   // bucket 범위 설정. 0 ~ 500 까지 25씩 나눠서 생성함
            "min": 0,
            "max": 500
          }
      }
    }
  }
}

{
  "size": 0,
  "aggs": {
    "orders_over_time": {
      "date_histogram": {
        "field": "purchased_at",
        "interval": "month"
      }
    }
  }
}

{% endhighlight %}

## Range 활용하기

{% highlight ruby %}

{
  "size": 0,
  "query": {
    "range": {           // range로 대상을(조건) 줄인 후 
      "total_amount": {
        "gte": 100
      }
    }
  },
  "aggs": {
    "status_terms": {        
      "terms": {                 // status 필드의 데이터를 기준으로 bucket 생성후
        "field": "status"
      },
      "aggs": {
        "status_stats": {               // 각 bucket을 stats으로 집계
          "stats": {
            "field": "total_amount"
          }
        }
      }
    }
  }
}

{
  "size": 0,
  "aggs": {
    "low_value": {
      "filter": {
        "range": {
          "total_amount": {            // 50 이하를 필터하여
            "lt": 50
          }
        }
      },
      "aggs": {
        "avg_amount": {
          "avg": {
            "field": "total_amount"   // 50 이하로 필터된 거 문서들의 total_amount의 평균값
          }
        }
      }
    }
  }
}

{
  "size": 0,
  "aggs": {
    "amount_distribution": {
      "range": {
        "field": "total_amount",
        "ranges": [
          {
            "to": 50
          },
          {
            "from": 50,
            "to": 100
          },
          {
            "from": 100
          }
        ]
      }
    }
  }
}

{
  "size": 0,
  "aggs": {
    "purchased_ranges": {
      "date_range": {
        "field": "purchased_at",
        "format": "yyyy-MM-dd",   // 결과에 날짜 포맷 설정
        "keyed": true,            // 결과(buckets)에 배열이 아닌 객체로 생성되며 객체 키값으로 설정됨
        "ranges": [
          {
            "from": "2016-01-01",
            "to": "2016-01-01||+6M",
            "key": "first_half"             // bucket 키 값 설정 가능
          },
          {
            "from": "2016-01-01||+6M",
            "to": "2016-01-01||+1y",
            "key": "second_half"          // bucket 키 값 설정 가능
          }
        ]
      }
    }
  }
}

{% endhighlight %}

## 특정 기준으로 buckets 생성하기

{% highlight ruby %}

* pasta 와 spaghetti buckets 생성

{
  "size": 0,
  "aggs": {
    "my_filter": {
      "filters": {
        "filters": {
          "pasta": {
            "match": {
              "title": "pasta"
            }
          },
          "spaghetti": {
            "match": {
              "title": "spaghetti"
            }
          }
        }
      },
      "aggs": {
        "avg_rating": {
          "avg": {
            "field": "ratings"  // 생성된 2개의 buckets에 각각의 ratings 평균 집계
          }
        }
      }
    }
  }
}

{% endhighlight %}

## Nested objects

{% highlight ruby %}

{
  "size": 0,
  "aggs": {
    "employees": {
      "nested": {
        "path": "employees"   // 해당 객체의 위치 지정
      },
      "aggs": {
        "minimum_age": {
          "min": {
            "field": "employees.age"   // 결과 -> 해당 객체 문서의 수, 해당 객체에서 age 가장 낮은 수 
          }
        }
      }
    }
  }
}

* 아래는 잘못된 방식
{
 "aggs": {
    "minimum_age": {
      "min": {
        "field": "employees.age"
      },
    }
  }
}
{% endhighlight %}

## 쿼리가 추가 되었을때 쿼리와 상관 없이 전체를 대상으로 집계

{% highlight ruby %}

{
  "query": {
    "range": {
      "total_amount": {   // total_amoun 가 100 이상 
        "gte": 100
      }
    }
  },
  "size": 0,
  "aggs": {                   // 전체 대상으로 집계
    "all_orders": {
      "global": { },
      "aggs": {
        "stats_amount": {
          "stats": {
            "field": "total_amount"
          }
        }
      }
    },
    "stats_expensive": {           // 쿼리 대상으로 집계
      "stats": {                   // stats 는 min, max, avg, sum 을 다 집계함
        "field": "total_amount"
      }
    }
  }
}

{% endhighlight %}

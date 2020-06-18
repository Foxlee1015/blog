---
layout: post
title: Es example
date: 2020-06-16 01:00:00 +0300
description: "Es example"
tags: [elasticsearch]
img: elasticsearch.png
---


[ES 가이드북][ES 가이드북]

## Mapping
   
{% highlight ruby %}

PUT http://localhost:9200/news
{
    "mappings": {
        "properties": {
            "title": {
                "type": "text"
            },
            "content": {
                "type": "text"
            },
            "url": {
                "type": "text",
                "index": "false"
            },
            "site": {
                "type": "keyword",
                "index": "false"   // search 에서는 사용하지 못하지만 aggs 쓸 수 있음
            },
            "published_at": {
                "type": "date"
            },
            "crawled_at": {
                "type": "date"
            },
            "category": {
                "type": "text"
            }
        }
    }
}

* 결과
{
    "acknowledged": true,
    "shards_acknowledged": true,
    "index": "news"
}

* 뉴스 데이터
POST http://localhost:9200/news/_doc
{
    "title": "Trump Told China To 'Go Ahead' With Prison Camps, Bolton Alleges In New Book",
    "content": "President Trump told Chinese President Xi Jinping that he endorsed Beijing's now-infamous archipelago of prison camps for minority Uighurs, John Bolton writes in his new memoir, The Room Where It Happened.",
    "url": "https://www.npr.org/2020/06/17/875876905/trump-told-china-to-go-ahead-with-concentration-camps-bolton-alleges-in-new-book",
    "site": "npr",
    "published_at": "2020-06-17T18:11:32-04:00",
    "category": "Politics",
    "crawled_at": 1592462983
}

* 결과
{
    "_index": "news",
    "_type": "_doc",
    "_id": "TmEyxnIBb1Z-KxZa7Qnf",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 0,
    "_primary_term": 1
}

* 쿼리 term/match 비교하기
{
    "query": {
        "term": {
            "title": "trump"  // 찾음 -> 입력된 값을 색인된 값과 비교
        }
    }
}
{
    "query": {
        "term": {
            "title": "Trump"  // 못찾음   -> 입력된 값을 색인된 값과 비교
        }
    }
}
{
    "query": {
        "match": {
            "title": "trump"   // 찾음   -> 입력된 값을 색인하고, 색인하여 저장된 값과 비교해서 찾음
        }
    }
}
{
    "query": {
        "match": {
            "title": "trump"  // 찾음 -> 입력된 값을 색인하고, 색인하여 저장된 값과 비교해서 찾음
        }
    }
}

* range 로 찾기
{
    "query": {
        "range": {
            "crawled_at": {
                "gte": 1592462982,
                "lte": 1592462989
            }
        }
    }
}

* aggs 를 이용해 기간별 뉴스 건수
* 데이터 추가
* 위의 데이터로 날짜, site만 다르게 데이터 추가함(날짜 잘못 기입 시 에러뜸)

POST http://localhost:9200/news/_doc/_search
{
    "size": 0,
    "aggs": {
        "weekly_news": {
            "date_range": {
                "field": "published_at",
                "format": "yyyy-MM-dd",      // foramt 없으면 결과에 기존 데이터 값에 맞춰서 동일하게 보여줌
                "keyed": true,              // default = false => 객체(key 데이터를 key 값으로 가지지 않고, 배열 안에 key 값을 가짐
                "ranges": [
                    {
                        "from": "2020-06-08",
                        "to": "2020-06-15",
                        "key": "last-week"
                    },
                    {
                        "from": "2020-06-15",
                        "to": "2020-06-23",
                        "key": "this-week"
                    },
                    {
                        "from": "2020-06-23",
                        "to": "2020-06-30",
                        "key": "next-week"
                    }
                ]
            }
        }
    }
}

* 결과
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
            "value": 70,
            "relation": "eq"
        },
        "max_score": null,
        "hits": []
    },
    "aggregations": {
        "weekly_news": {
            "buckets": {
                "last-week": {
                    "from": 1.5915744E12,
                    "from_as_string": "2020-06-08",
                    "to": 1.5921792E12,
                    "to_as_string": "2020-06-15",
                    "doc_count": 19
                },
                "this-week": {
                    "from": 1.5921792E12,
                    "from_as_string": "2020-06-15",
                    "to": 1.5928704E12,
                    "to_as_string": "2020-06-23",
                    "doc_count": 20
                },
                "next-week": {
                    "from": 1.5928704E12,
                    "from_as_string": "2020-06-23",
                    "to": 1.5934752E12,
                    "to_as_string": "2020-06-30",
                    "doc_count": 14
                }
            }
        }
    }
}

* 사이트별 카운트
{
  "size: 0,
  "aggs": {
    "sites": {
      "terms": {
        "field": "site",
        "min_doc_count": 0,           //  이 숫자보다 작으면 bucket 만들지 않음
        "order": {
          "_key": "asc"               // 집계의 buckets 들의 이름 순서로 정렬   asc = 오름차순, desc =  내림차순
        }
      }
    }
  }
}

* 사이트별로 range 적용하기

{
    "size": 0,
    "aggs": {
        "sites": {
            "terms": {
                "field": "site",
                "min_doc_count": 20,
                "order": {
                    "_key": "asc"
                }
            },
            "aggs": {
                "weekly_news": {
                    "date_range": {
                        "field": "published_at",
                        "format": "yyyy-MM-dd",
                        "keyed": true,
                        "ranges": [
                            {
                                "from": "2020-06-08",
                                "to": "2020-06-15",
                                "key": "last-week"
                            },
                            {
                                "from": "2020-06-15",
                                "to": "2020-06-23",
                                "key": "this-week"
                            },
                            {
                                "from": "2020-06-23",
                                "to": "2020-06-30",
                                "key": "next-week"
                            }
                        ]
                    }
                }
            }
        }
    }
}

{% endhighlight %}

{% highlight ruby %}

## 분석기 적용하기

1. 이미 인덱스를 생성했기 때문에 close 후 가능

POST http://localhost:9200/news/_close
PUT http://localhost:9200/news/_settings
{
    "settings": {
        "analysis": {
            "analyzer": {
                "news_analyzer" : {
                    "type": "custom",
                    "char_filter" : ["html_strip"],
                    "tokenizer": "standard",
                    "filter": [
                        "lowercase",
                        "stop",
                        "asciifolding"
                    ]
                }
            }

        }
    }
}

POST http://localhost:9200/news/_open
GET http://localhost:9200/news/_settings -> Custom analyzer 추가된 거 확인 가능
POST http://localhost:9200/news/_update_by_query?conflicts=proceed -> 분석기 변경(적용) 전 인덱싱 된거를 새로운 분석기에 맞춰 수정

GET http://localhost:9200/news/_doc/TmEyxnIBb1Z-KxZa7Qnf/_termvectors
{
"fields": ["content"]
}

{% endhighlight %}

[ES 가이드북]: https://esbook.kimjmin.net/05-search/5.1-query-dsl
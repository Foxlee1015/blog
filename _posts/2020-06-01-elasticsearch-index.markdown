---
layout: post
title: Elasticsearch-index
date: 2020-06-01 00:00:00 +0300
description: "Elasticsearch-index"
tags: [elasticsearch]
img: elasticsearch.png
---

## Update mapping, reindex, aliases, mutil-field, index-template

## Update existing mappings

1. 예) product_id 숫자였으나 글자가 들어가서 필드 타입을 int 에서 text 나 keyword로 변경

    - product_id 는 검색 시 full-text search 를 사용하지 않고 입력된 값 그대로 검색할 것이므로 keyword 로 설정

2. 일반적으로는 맵핑 변경 불가능하지만, 일부 맵핑 파라미터는 변경될 수 있음.
    - 불가능 이유 1. 기존의 필드 맵핑에 맞춰 이미 인덱싱 되었기 때문에
    - 불가능 이유 2. 타입 변경으로 인해 전체 데이터 구조를 바꿔야 하는 것이 요구됨

{% highlight ruby %}

> 가능한 예
PUT /reviews/\_mapping
{
    "properties": {
        "author": {
            "properties": {
                "email": {
                    "type": "keyword",
                    "ignore_above": 256 // 256 글자 이상은 짜름
                }
            }
        }
    }
}

{% endhighlight %}

3. 필드 맵핑은 제거될 수 없으며 필요없는 필드의 경우 문서를 인덱싱할 때 무시하면 됨

4. 결론적으로 필드를 수정하기 위해서 reindex 가 필요함

## Reindex api

1. 기존 인덱스에서 맵핑 정보 가져오기

    > GET /reviews/\_mappings

2. 새로운 인덱스에 맵핑 적용

{% highlight ruby %}
PUT /reviews_new
{
    "mappings" : {
        "properties" : {
            "author" : {
                "properties" : {
                    "email" : {
                        "type" : "keyword",
                        "ignore_above" : 256
                    },
                    "first_name" : {
                        "type" : "text"
                    },
                    "last_name" : {
                        "type" : "text"
                    }
                }
            },
            "content" : {
                "type" : "text"
            },
            "created_at" : {
                "type" : "date"
            },
            "product_id" : {
                "type" : "keyword" // integer -> keyword 로 변경
            },
            "rating" : {
                "type" : "float"
            }
        }
    }
}

{% endhighlight %}

## Reindex

{% highlight ruby %}
POST /\_reindex
{
    "source": {
        "index": "reviews"
    },
    "dest": {
        "index": "reviews_new"
    }
}
{% endhighlight %}

## Reindex - 스크립트를 활용한 product_id 문자열로 변경

{% highlight ruby %}
POST /\_reindex
{
    "source": {
        "index": "reviews"
    },
    "dest": {
        "index": "reviews_new"
    },
    "script": {
        "source": 
        """
            if (ctx.\_source.product_id != null) {
                ctx.\_source.product_id = ctx.\_source.product_id.toString();
            }
        """
    }
}

{% endhighlight %}

## Reindex - 특정 데이터만 reindex에 포함하기

{% highlight ruby %}

POST /\_reindex
{
    "source": {
        "index": "reviews",
        "query": {
        "match_all": { }
        }
    },
    "dest": {
        "index": "reviews_new"
    }
}

POST /\_reindex // 특정 조건에 충족하는 것만
{
    "source": {
        "index": "reviews",
        "query": {
            "range": {
                "rating": {
                    "gte": 4.0
                }
            }
        }
    },
    "dest": {
        "index": "reviews_new"
    }
}

POST /\_reindex // 특정 필드만 가져오기
{
    "source": {
        "index": "reviews",
        "\_source": ["content", "created_at", "rating"]
    },
    "dest": {
        "index": "reviews_new"
    }
}

POST /\_reindex // 필드명 변경
{
    "source": {
        "index": "reviews"
    },
    "dest": {
        "index": "reviews_new"
    },
    "script": {
        "source": """ # Rename "content" field to "comment"
        ctx.\_source.comment = ctx.\_source.remove("content");
        """
    }
}

POST /\_reindex // 특정 조건에 만족하는 건 무시하기
{
    "source": {
        "index": "reviews"
    },
    "dest": {
        "index": "reviews_new"
    },
    "script": {
        "source": """
        if (ctx.\_source.rating < 4.0) {
        ctx.op = "noop"; # Can also be set to "delete"
        }
        """
    }
}

{% endhighlight %}

## reindex 후 제거하기

{% highlight ruby %}
POST /reviews_new/\_delete_by_query
{
    "query": {
        "match_all": {}
    }
}
{% endhighlight %}

## field aliases 기존 필드에서 필드 추가하기.

1. field aliases는 인덱싱에 영향을 주지 않음
2. 쿼리 레벨에서 생성됨(즉, 쿼리시 필드명만 변경되어 대상 필드로부터 데이터를 참조하여 추가된 필드명으로 반환)

{% highlight ruby %}

PUT /reviews/\_mapping
{
    "properties": {
        "comment": { // 대상 필드
            "type": "alias",
            "path": "content" // 새로운 맵핑
        }
    }
}
GET /reviews/\_search
{
    "query": {
        "match": {
            "comment": "outstanding"
        }
    }
}

/ alias 추가한 후
GET /reviews/\_search
{
    "query": {
        "match": {
            "content": "outstanding"
        }
    }
}
{% endhighlight %}

## Multi-field mapping

-   text 타입인 필드에 키워드 필드 추가하기

{% highlight ruby %}
PUT /multi_field_test
{
    "mappings": {
        "properties": {
            "description": {
                "type": "text"
            },
            "ingredients": {
                "type": "text",
                "fields": {
                    "keyword": {
                        "type": "keyword"
                    }
                }
            }
        }
    }
}

POST /multi_field_test/\_doc
{
    "description": "To make this spaghetti carbonara, you first need to...",
    "ingredients": ["Spaghetti", "Bacon", "Eggs"] -> spaghetti, bacon, eggs 로 인덱싱됨
}

{% endhighlight %}

-   text 맵핑, keyword 맵핑 차이

{% highlight ruby %}

GET /multi_field_test/\_search
{
    "query": {
        "match": {
            "ingredients": "Spaghetti" -> spaghetti 를 찾음
        }
    }
}

GET /multi_field_test/\_search
{
    "query": {
        "term": {
            "ingredients.keyword": "Spaghetti" -> Spaghetti 를 찾음
        }
    }
}

{% endhighlight %}

## Index templates

{% highlight ruby %}
PUT /\_template/access-logs
{
    "index_patterns": ["access-logs-*"], // whildcard
    "settings": {
        "number_of_shards": 2,
        "index.mapping.coerce": false
    },
    "mappings": {
        "properties": {
            "@timestamp": {
                "type": "date"
            },
            "url.original": {
                "type": "keyword"
            },
            "http.request.referrer": {
                "type": "keyword"
            },
            "http.response.status_code": {
                "type": "long"
            }
        }
    }
}

PUT /access-logs-2020-01-01 - index_patterns 에 맞춰서 인덱스 생성
GET /access-logs-2020-01-01 - 생성된 인덱스의 맵핑 확인

{% endhighlight %}

{% highlight ruby %}

> 업데이트 방법

PUT /\_template/access-logs
{ # Full config
}

> 삭제

DELETE /\_template/access-logs

{% endhighlight %}

-   인덱스를 생설할때 셋팅에 추가적으로 입력하면 오버라이딩 된다.

{% highlight ruby %}
PUT /access-logs-2020-01-01
{
    "settings": {
    "number_of_shards": 3, // 템플릿에는 2이나 3으로 셋팅해주면 오버라이딩된 값인 3으로 설정됨
    }
}

{% endhighlight %}

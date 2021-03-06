---
layout: post
title: Elasticsearch-query
date: 2020-06-04 00:00:00 +0300
description: "Elasticsearch-query"
tags: [elasticsearch]
img: elasticsearch.png
---

## Query

* match, term 쿼리의 차이점
* term 은 입력된 값 자체를 색인된 데이터로부터 찾는다. 따라서 기본값으로 인덱싱된 데이터의 경우 소문자화되어 저장되기 때문에 대문자를 입력하면 찾을 수 없음
* match 는 입력된 값을 분석한 값으로 찾음
* _source // 검색 결과 데이터 일부만 가져오기 또는 결과만 가져오기
* Url 파라미터로 검색하기
* Wildcard, prefix, regxp

## 필터 검색

{% highlight ruby %}

{
    "query": {
        "bool": {
            "filter": [
            	{
	                "range": {
	                    "age": {
	                        "gte": 10
	                    }
	                }
            	},
            	{
            		"match": {
            			"gender": "F"
            		}
            	}
            ]
        }
    }
}


{
    "query": {
        "bool": {
            "filter": [
            	{
	                "range": {
	                    "age": {
	                        "gte": 10
	                    }
	                }
            	},
            	{
            		"term": {
            			"gender": "F"      // 결과 없음
            		}
            	}
            ]
        }
    }
}
{% endhighlight %}


## _source // 검색 결과 데이터 일부만 가져오기 또는 결과만 가져오기

{% highlight ruby %}

GET /news/_search
{
  "_source": "url", 
  "query": {
    "match_all": {}
  }
}

GET /news/_search
{
  "_source": ["url", "title"], 
  "query": {
    "match_all": {}
  }
}


GET /news/_search
{
  "_source": {
    "excludes": "text"
  }, 
  "query": {
    "match_all": {}
  }
}

GET /news/_search
{
  "_source": false,
  "size": 10,
  "query": {
    "match_all": {}
  }
}

{% endhighlight %}


## 특정 문서의 검색 점수 정보

{% highlight ruby %}

GET /news/_doc/world-europe-52914089/_explain
{
  "query": {
    "term" : {
      "category": "coronavirus"
    }
  }
}

{% endhighlight %}

## Url 파라미터로 검색하기 - match_all

{% highlight ruby %}

GET /news/_search?q=*

GET /news/_search?q=category:coronavirus

GET /news/_search?q=category:coronavirus AND published_at:1591226181

{% endhighlight %}

## Wildcard, prefix, regxp

{% highlight ruby %}


GET /news/_search
{
  "query": {
    "wildcard": {
      "category": "corona*"
    }
  }
}

GET /news/_search
{
  "query": {
    "wildcard": {
      "category": "coronaviru?"
    }
  }
}

# bbc
GET /news/_search
{
  "query": {
    "prefix": {
      "site": "bb"   
    }
  }
}


# 정규식   coronavirus  corona[a-zA-Z]+ , corona[a-zA-Z]+  irus / rus / us / s
GET /news/_search
{
  "query": {
    "regexp": {
      "category.keyword": "corona[a-zA-Z]+"
    }
  }
}


{% endhighlight %}


## 날짜, 기간 관련 쿼리

{% highlight ruby %}


GET /news/_search
{
  "query": {
    "range": {
      "published_at": {
        "gte": "2010/01/01",
        "lte": "2020/12/31"
      }
    }
  }
}

GET /news/_search
{
  "query": {
    "range": {
      "published_at": {
        "gte": "01-01-2010",
        "lte": "31-12-2030",
        "format": "dd-MM-yyyy"
      }
    }
  }
}



GET /news/_search
{
  "query": {
    "range": {
      "published_at": {
        "gte": "01-01-2010",
        "lte": "06-06-2020",
        "format": "dd-MM-yyyy"
      }
    }
  }
}


GET /news/_search
{
  "query": {
    "range": {
      "published_at": {
        "lte": "now"
      }
    }
  }
}


GET /product/_search
{
  "query": {
    "range": {
      "created": {
        "gte": "2010/01/01||-1y-1d"
      }
    }
  }
}

{% endhighlight %}

### 필드 존재 여부, 복수개의 id 정보로 검색, term 검색

{% highlight ruby %}


GET /news/_search
{
  "query": {
    "term" : {
      "category": "coronavirus"
    }
  }
}

### or query
GET /news/_search
{
  "from": 2,
  "query": {
    "terms": {
      "category.keyword": [ "coronavirus", "health" ]
    }
  }
}


{% endhighlight %}



### 필드 삭제하기 
* [Script][Script] 문서 참고

{% highlight ruby %}
POST http://host:port/index/_update_by_query?conflicts=proceed

{
  "script": "ctx._source.remove('filed_name')",
  "query": {
    "bool": {
      "must": [
        {
          "exists": {
            "field": "filed_name"
          }
        }
      ]
    }
  }
}
{% endhighlight %}

### 필드 추가하기 

{% highlight ruby %}
POST http://host:port/index/_update_by_query?conflicts=proceed

{
  "script": "ctx._source.fieldName2='field_value'",  // ='string' or =1
  "query": {
    "bool": {
      "must": [
        {
          "exists": {
            "field": "fieldName2"
          }
        }
      ]
    }
  }
}
{% endhighlight %}

### Random_score
* [참고][참고]
    
{% highlight ruby %}

{
  "query": {
    "function_score": {
      "query": {
        "bool": {
          "must_not": [
            {
              "exists": {
                "field": "field_name"
              }
            }
          ]
        }
      },
    "random_score": {}
    }  }
}
{% endhighlight %}



[Script]: https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-update.html 
[참고]:http://richardhallett.com/posts/random-sampling-elasticsearch/

---
layout: post
title: Elasticsearch-query
date: 2020-06-04 00:00:00 +0300
description: "Elasticsearch-query"
tags: [elasticsearch]
img: elasticsearch.png
---

## 요약

-   match, term 쿼리의 차이점
-   term 은 입력된 값 자체를 색인된 데이터로부터 찾는다. 따라서 기본값으로 인덱싱된 데이터의 경우 소문자화되어 저장되기 때문에 대문자를 입력하면 찾을 수 없음
-   match 는 입력된 값을 분석한 값으로 찾음

## 예

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

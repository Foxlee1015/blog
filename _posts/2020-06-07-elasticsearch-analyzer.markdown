---
layout: post
title: Elasticsearch-analyzer
date: 2020-06-07 00:00:00 +0300
description: "Elasticsearch-analyzer"
tags: [elasticsearch]
img: elasticsearch.png
---

## 요약
* 분석기, custom 분석기
* tokenizer(standard, keyword)
* filter



## 분석기 예제 및 custom 분석기 만들기

{% highlight ruby %}

1. html tag 삭제하는 분석기
POST /_analyze          
{
  "char_filter": ["html_strip"],
  "text": "I&apos;m in a <em>good</em> mood&nbsp;-&nbsp;and I <strong>love</strong> açaí!"
}
응답
{
  "tokens" : [
    {
      "token" : "I'm in a good mood - and I love açaí!",
      "start_offset" : 0,
      "end_offset" : 78,
      "type" : "word",
      "position" : 0
    }
  ]
}

2. 분석기에 토크나이저 추가
POST /_analyze
{
  "char_filter": ["html_strip"],
  "tokenizer": "standard",
  "text": "I&apos;m in a <em>good</em> mood&nbsp;-&nbsp;and I <strong>love</strong> açaí!"
}
응답
{
  "tokens" : [
    {
      "token" : "I'm",
      "start_offset" : 0,
      "end_offset" : 8,
      "type" : "<ALPHANUM>",
      "position" : 0
    },
    {
      "token" : "in",
      "start_offset" : 9,
      "end_offset" : 11,
      "type" : "<ALPHANUM>",
      "position" : 1
    },
    {
      "token" : "a",
      "start_offset" : 12,
      "end_offset" : 13,
      "type" : "<ALPHANUM>",
      "position" : 2
    },
    {
      "token" : "good",
      "start_offset" : 18,
      "end_offset" : 27,
      "type" : "<ALPHANUM>",
      "position" : 3
    },
    {
      "token" : "mood",
      "start_offset" : 28,
      "end_offset" : 32,
      "type" : "<ALPHANUM>",
      "position" : 4
    },
    {
      "token" : "and",
      "start_offset" : 45,
      "end_offset" : 48,
      "type" : "<ALPHANUM>",
      "position" : 5
    },
    {
      "token" : "I",
      "start_offset" : 49,
      "end_offset" : 50,
      "type" : "<ALPHANUM>",
      "position" : 6
    },
    {
      "token" : "love",
      "start_offset" : 59,
      "end_offset" : 72,
      "type" : "<ALPHANUM>",
      "position" : 7
    },
    {
      "token" : "açaí",
      "start_offset" : 73,
      "end_offset" : 77,
      "type" : "<ALPHANUM>",
      "position" : 8
    }
  ]
}
3. lowercase 토큰 필터 추가

POST /_analyze
{
  "char_filter": ["html_strip"],
  "tokenizer": "standard",
  "filter": [
    "lowercase"
  ],
  "text": "I&apos;m in a <em>good</em> mood&nbsp;-&nbsp;and I <strong>love</strong> açaí!"
}
응답
{
  "tokens" : [
    {
      "token" : "i'm",
      "start_offset" : 0,
      "end_offset" : 8,
      "type" : "<ALPHANUM>",
      "position" : 0
    },
    {
      "token" : "in",
      "start_offset" : 9,
      "end_offset" : 11,
      "type" : "<ALPHANUM>",
      "position" : 1
    },
    {
      "token" : "a",
      "start_offset" : 12,
      "end_offset" : 13,
      "type" : "<ALPHANUM>",
      "position" : 2
    },
    {
      "token" : "good",
      "start_offset" : 18,
      "end_offset" : 27,
      "type" : "<ALPHANUM>",
      "position" : 3
    },
    {
      "token" : "mood",
      "start_offset" : 28,
      "end_offset" : 32,
      "type" : "<ALPHANUM>",
      "position" : 4
    },
    {
      "token" : "and",
      "start_offset" : 45,
      "end_offset" : 48,
      "type" : "<ALPHANUM>",
      "position" : 5
    },
    {
      "token" : "i",
      "start_offset" : 49,
      "end_offset" : 50,
      "type" : "<ALPHANUM>",
      "position" : 6
    },
    {
      "token" : "love",
      "start_offset" : 59,
      "end_offset" : 72,
      "type" : "<ALPHANUM>",
      "position" : 7
    },
    {
      "token" : "açaí",
      "start_offset" : 73,
      "end_offset" : 77,
      "type" : "<ALPHANUM>",
      "position" : 8
    }
  ]
}
4. stop 토큰 필터 -> 영어 stop words 제거
POST /_analyze
{
  "char_filter": ["html_strip"],
  "tokenizer": "standard",
  "filter": [
    "lowercase",
    "stop"
  ],
  "text": "I&apos;m in a <em>good</em> mood&nbsp;-&nbsp;and I <strong>love</strong> açaí!"
}
응답
{
  "tokens" : [
    {
      "token" : "i'm",
      "start_offset" : 0,
      "end_offset" : 8,
      "type" : "<ALPHANUM>",
      "position" : 0
    },
    {
      "token" : "good",
      "start_offset" : 18,
      "end_offset" : 27,
      "type" : "<ALPHANUM>",
      "position" : 3
    },
    {
      "token" : "mood",
      "start_offset" : 28,
      "end_offset" : 32,
      "type" : "<ALPHANUM>",
      "position" : 4
    },
    {
      "token" : "i",
      "start_offset" : 49,
      "end_offset" : 50,
      "type" : "<ALPHANUM>",
      "position" : 6
    },
    {
      "token" : "love",
      "start_offset" : 59,
      "end_offset" : 72,
      "type" : "<ALPHANUM>",
      "position" : 7
    },
    {
      "token" : "açaí",
      "start_offset" : 73,
      "end_offset" : 77,
      "type" : "<ALPHANUM>",
      "position" : 8
    }
  ]
}

5. keyword tokenizer
POST /_analyze
{
  "text": "2 guys walk into   a bar, but the third... DUCKS! :-)",
  "analyzer": "keyword"
}
{
  "tokens" : [
    {
      "token" : "2 guys walk into   a bar, but the third... DUCKS! :-)",
      "start_offset" : 0,
      "end_offset" : 53,
      "type" : "word",
      "position" : 0
    }
  ]
}

{% endhighlight %}


## 존재하는 인덱스에 분석기 추가하기

{% highlight ruby %}
* 인덱스에 설정을 변경하기 위해서 먼저 인덱스를 닫아야 함

POST /news/_close   

GET /_cat/indices?v  -> 해당 인덱스의 상태가 닫힌걸 확인할 수 있음

* 분석기 추가

PUT /news/_settings
{
  "analysis": {
    "analyzer": {
      "my_second_analyzer": {
        "type": "custom",
        "tokenizer": "standard",
        "char_filter": ["html_strip"],
        "filter": [
          "lowercase",
          "stop",
          "asciifolding"
        ]
      }
    }
  }
}

* 분석기 열기
  
POST /news/_open

* 설정 반영 결과 확인

GET /news/_settings
{
  "news" : {
    "settings" : {
      "index" : {
        "number_of_shards" : "1",
        "provided_name" : "news",
        "creation_date" : "1591449773246",
        "analysis" : {
          "analyzer" : {
            "my_second_analyzer" : {
              "filter" : [
                "lowercase",
                "stop",
                "asciifolding"
              ],
              "char_filter" : [
                "html_strip"
              ],
              "type" : "custom",
              "tokenizer" : "standard"
            }
          }
        },
        "number_of_replicas" : "1",
        "uuid" : "szUSKu4XSUy_pcxFKz-a5Q",
        "version" : {
          "created" : "7070199"
        }
      }
    }
  }
}

* 변경된 분석기로 reindex
  
POST /news/_update_by_query?conflicts=proceed



{% endhighlight %}

## Es 의 배열 분석 : 텍스트들이 하나로 연결됨

{% highlight ruby %}


POST /_analyze
{
  "text": ["Strings are simply", "merged together."],
  "analyzer": "standard"
}
응답
{
  "tokens" : [
    {
      "token" : "strings",
      "start_offset" : 0,
      "end_offset" : 7,
      "type" : "<ALPHANUM>",
      "position" : 0
    },
    {
      "token" : "are",
      "start_offset" : 8,
      "end_offset" : 11,
      "type" : "<ALPHANUM>",
      "position" : 1
    },
    {
      "token" : "simply",
      "start_offset" : 12,
      "end_offset" : 18,
      "type" : "<ALPHANUM>",
      "position" : 2
    },
    {
      "token" : "merged",
      "start_offset" : 19,
      "end_offset" : 25,
      "type" : "<ALPHANUM>",
      "position" : 3
    },
    {
      "token" : "together",
      "start_offset" : 26,
      "end_offset" : 34,
      "type" : "<ALPHANUM>",
      "position" : 4
    }
  ]
}

{% endhighlight %}



## ES 자동(강제) 변환 - coercion


{% highlight ruby %}


PUT /coercion_test/_doc/1
{
  "price": 7.4        -> 숫자로 저장됨 -> 동적 매핑으로 float 으로 설정됨
}


PUT /coercion_test/_doc/2
{
  "price": "7.4"   -> 문자열로 저장됨
}

PUT /coercion_test/_doc/3
{
  "price": "7.4m"   ->  에러
}
에러 : "failed to parse field [price] of type [float] in document with id '3'. Preview of field's value: '7.4m'"


GET /coercion_test/_doc/2 
  "_source" : {
    "price" : "7.4"
  }   -> 사용자가 보는 건 입력된 정보이지만 숫자 7.4로 저장되어 있으며

GET /coercion_test/_search
{
  "query": {
    "match" : {
      "price": 7.4
    }
  }
}
응답으로 1, 2번 모두 검색됨

{% endhighlight %}


## standard 분석기 api

{% highlight ruby %}

POST /_analyze
{
  "text": "2 guys walk into   a bar, but the third... DUCKS! :-)",
  "analyzer": "standard"
}

POST /_analyze
{
  "text": "2 guys walk into   a bar, but the third... DUCKS! :-)",
  "char_filter": [],
  "tokenizer": "standard",
  "filter": ["lowercase"]
}

위 text 의 분석 결과는 동일함

{% endhighlight %}

---
layout: post
title: Elasticsearch-analyzer
date: 2020-05-30 12:00:00 +0300
description: "Elasticsearch-analyzer"
tags: [web, dns]
img: elasticsearch.png
---

## stemming, stop words

1. stemming : 단어의 root form 으로 변경
    * loved -> love
    * I loved drinking bottles of whine on last year's vacation. -> I love drink botle of whine on last year vacat

2. stop words
    * a, the, at, of, on 등은 텍스트 분석시 필터처리되며, 스코어에 계산되지 않음


    * "I loved drinking bottles of whine on last year's vacation." -> ["i", "love", "drink", "botle", "of", "whine", "on", "last", "year", "vacat"]
    * 검색 시에도 동일하게 적용되기 때문에 검색시 찾을 수 있음
    * 예) {"query":"match":{"description" : "drinking"}} -> drink 을 찾음


## Built-in analyzers
[분석기][분석기]

1. Standard analyzer
    * 단어 분리, punctuation 제거 = 토큰화
    * 소문자로 변경
    * stop token filter

2. simple analyzer
    * letter 가 아니면 무조건 분리시킴(토큰화)
    * 소문자
    * "Is that Peter's cute-looking dog?" -> ["is", "that", "peter", "s", "cute", looking", "dog"]

3. whitespace analyzer
    * 띄어쓰기로만 분리 토큰화, 소문자로 변경하지 않음

4. keyword analyzer
    * input 문장 그대로 토큰 하나가 됨

5. pattern anaylzer
    * 정규식을 활용해 매치시켜 토큰화

6. language anaylzer

{% highlight ruby %}
PUT /english_example
{
    "settings": {
        "analysis": {
        "filter": {
            "english_stop": {
            "type":       "stop",
            "stopwords":  "_english_" 
            },
            "english_keywords": {
            "type":       "keyword_marker",
            "keywords":   ["example"] 
            },
            "english_stemmer": {
            "type":       "stemmer",
            "language":   "english"
            },
            "english_possessive_stemmer": {
            "type":       "stemmer",
            "language":   "possessive_english"
            }
        },
        "analyzer": {
            "rebuilt_english": {
                "tokenizer":  "standard",
                "filter": [
                    "english_possessive_stemmer",   // 소유격
                    "lowercase",                    // 소문자
                    "english_stop",
                    "english_keywords",
                    "english_stemmer"
                    ]
                }
            }
        }
    }
}
    
{% endhighlight %}


{% highlight ruby %}
> standard 분석기로 html 태그 분석 결과 확인(토큰화 안함)
post /_analyze
{
    "analyzer" : "standard",
    "text": "<h1>this is a test text</h1>"
}

{
    "tokens": [
        {
            "token": "h1",
            "start_offset": 1,
            "end_offset": 3,
            "type": "<ALPHANUM>",
            "position": 0
        },
        {
            "token": "this",
            "start_offset": 4,
            "end_offset": 8,
            "type": "<ALPHANUM>",
            "position": 1
        },
        {
            "token": "is",
            "start_offset": 9,
            "end_offset": 11,
            "type": "<ALPHANUM>",
            "position": 2
        },
        {
            "token": "a",
            "start_offset": 12,
            "end_offset": 13,
            "type": "<ALPHANUM>",
            "position": 3
        },
        {
            "token": "test",
            "start_offset": 14,
            "end_offset": 18,
            "type": "<ALPHANUM>",
            "position": 4
        },
        {
            "token": "text",
            "start_offset": 19,
            "end_offset": 23,
            "type": "<ALPHANUM>",
            "position": 5
        },
        {
            "token": "h1",
            "start_offset": 25,
            "end_offset": 27,
            "type": "<ALPHANUM>",
            "position": 6
        }
    ]
}
> standdard 분석기 대신 char_filter 적용
post _analyze
{
    "char_filter": ["html_strip"],
    "text": "<h1>this is a test text</h1>"
}

{
    "tokens": [
        {
            "token": "\nthis is a test text\n",
            "start_offset": 0,
            "end_offset": 28,
            "type": "word",
            "position": 0
        }
    ]
}  
{% endhighlight %}



{% highlight ruby %}
> custom analyzer  인덱스에 적용

put /analyzer_test
{
    "settings": {
        "analysis": {
            "analyzer": {
                "my_custom_analyzer" :{   // custom 분석기 이름
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
{
    "acknowledged": true,
    "shards_acknowledged": true,
    "index": "analyzer_test"
}

> 적용 후 테스트

/get /analyzer_test/_analyze
{
	"analyzer" : "my_custom_analyzer",
    "text": "<h1>this is a test text</h1>"
}

{
    "tokens": [
        {
            "token": "test",
            "start_offset": 14,
            "end_offset": 18,
            "type": "<ALPHANUM>",
            "position": 3
        },
        {
            "token": "text",
            "start_offset": 19,
            "end_offset": 23,
            "type": "<ALPHANUM>",
            "position": 4
        }
    ]
}
{% endhighlight %}



{% highlight ruby %}
> custom filter 
put /analyzer_test
{
    "settings": {
        "analysis": {
            "filter" : {
                "korean_stop": {
                    "type": "stop",
                    "stopwords": "_korean_"
                }
            },
            "char_filter": {},   // korean_stop 처럼 만들어서 밑에 적용하면 됨
            "tokenizer": {},
            "analyzer": {
                "my_custom_analyzer" :{   // custom 분석기 이름
                    "type": "custom",
                    "char_filter" : ["html_strip"],
                    "tokenizer": "standard",
                    "filter": [
                        "lowercase",
                        "korean_stop",   // 위 필터 적용 
                        "asciifolding"
                    ]
                }
            }

        }
    }
}
{% endhighlight %}



{% highlight ruby %}
> 인덱스 생성 후에 분석기 수정하기
put /analyzer_test/_settings  --> 에러 발생함 아래 static settings 확인
{
    "analysis": {
        "analyzer": {
            "my_new_custom_analyzer" :{   // custom 분석기 이름
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

{% endhighlight %}


* Dynamic and static settings
1. Dynamic settings
    * 인덱스 closing 하지 않아도 환경 설정 변경 가능

2. static settings
    * 인덱스 closing 하지 않으면 환경 설정 변경 불가능
    * 분석기는 static setting 이므로 먼저 닫은 후 가능
  


{% highlight ruby %}
  
> 위에서 분석기 변경하려면 먼저 인덱스 닫은 후

/post /analyzer_test/_close

/put /analyzer_test/_settings
{
    "analysis": {
        "analyzer": {
            "my_new_custom_analyzer" :{
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
/post /analyzer_test/_open

/get /analyzer_test/_settings 에서 확인 가능
{% endhighlight %}

* 위와 같이 분석기를 변경하는 것은 서비스 환경에서 바르지 못함. reindex하는 방법으로 먼저 새로운 인덱스를 만들어 분석기를 설정하고 index alias 를 통해 transition 한다


* 분석기를 중간에 변경하면 기존 문서들은 변경 전의 분석한 상태로 되어 있기  때문에 새로 변경된 것으로 맞춰줘야함
    * /post /analyzer_test/_update_by_query?conflicts=proceed
* 분석기 변경 예) 문장에 the 를 찾고 싶은데 디폴트 분석기의 경우 이를 생략하고 토큰화하고 인덱싱 하기 때문에 검색 쿼리를 통해 inverted index 에서 해당 단어 및 문서를 찾을 수 없다. 다른 분석기로 변경 후 the이  inverted index에 들어가게 하고 분석기 변경 전 후의 분석 결과를 맞춰야함


[분석기]: https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-analyzers.html
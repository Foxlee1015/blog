---
layout: post
title: Elasticsearch-field
date: 2020-06-07 00:00:00 +0300
description: "Elasticsearch-field"
tags: [elasticsearch]
img: elasticsearch.png
---

## 요약
* 복수 타입을 가지는 필드 매핑
* 필드 추가, 필드 alias 추가
* 매핑 가져오기(인덱스, 필드)
* 일반적으로 필드 매핑은 업데이트는 불가(추가만 가능) 하지만  ignore_above 등은 가능함
* 객체의 dot notation 

## 복수 타입을 가지는 필드 매핑

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

POST /multi_field_test/_doc
{
  "description": "To make this spaghetti carbonara, you first need to...",
  "ingredients": ["Spaghetti", "Bacon", "Eggs"]
}

GET /multi_field_test/_search
{
  "query": {
    "match": {
      "ingredients": "Spaghetti"
    }
  }
}

GET /multi_field_test/_search
{
  "query": {
    "term": {
      "ingredients.keyword": "Spaghetti"   -> o
    }
  }
}


GET /multi_field_test/_search
{
  "query": {
    "term": {
      "ingredients": "Spaghetti"   -> x
    }
  }
}

{% endhighlight %}

## 필드 추가하기

{% highlight ruby %}

* 인덱스 생성 전
  
PUT /articles
{
  "mappings": {
    "properties": {
      "rating": { "type": "float" },
      "content": { "type": "text" },
      "article_id": { "type": "integer" },
      "author": {
        "properties": {
          "first_name": { "type": "text" },
          "last_name": { "type": "text" },
          "email": { "type": "keyword" }
        }
      }
    }
  }
}
* 이미 존재하는 인덱스(존재하지 않을  시 에러 뜸)
PUT /articles/_mapping
{
  "properties": {
    "created_at": {
      "type": "date"
    }
  }
}

{% endhighlight %}

## 필드 aliases 추가하기

{% highlight ruby %}

* 뉴스 인덱스의 본문인 text 필드에 content alias 추가하기

PUT /news/_mapping
{
  "properties": {
    "content": {
      "type": "alias",
      "path": "text"
    }
  }
}

* 저장시 content 필드로 지정하여 저장하면 text에 변경되어 text 에 저장되며
* 검색시 content 필드는 text로 변경하여 text에서 검색함 

* 아래 두개의 쿼리는 같은 결과가 나옴
GET /news/_search
{
  "query": {
    "match": {
      "text": "tech"
    }
  }
}

GET /news/_search
{
  "query": {
    "match": {
      "content": "tech"
    }
  }
}

{% endhighlight %}

## 매핑 가져오기

{% highlight ruby %}

GET /news/_mapping
GET /news/_mapping/field/title
GET /news/_mapping/field/author.email

{% endhighlight %}

## 객체의 dot notation

{% highlight ruby %}

PUT /reviews_dot_notation
{
  "mappings": {
    "properties": {
      "rating": { "type": "float" },
      "content": { "type": "text" },
      "product_id": { "type": "integer" },
      "author.first_name": { "type": "text" },
      "author.last_name": { "type": "text" },
      "author.email": { "type": "keyword" }
    }
  }
}

{% endhighlight %}
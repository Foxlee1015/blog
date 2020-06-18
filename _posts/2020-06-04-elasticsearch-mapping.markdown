---
layout: post
title: Elasticsearch-mapping
date: 2020-06-04 00:00:00 +0300
description: "Elasticsearch-mapping"
tags: [elasticsearch]
img: elasticsearch.png
---

## 요약

-   엘라스틱에서 제공하는 더미 데이터를 올려서 매핑, 매핑 템플릿 적용
-   shakespeare(shakespeare), account(bank), logs(logstash-2015.05.18, logstash-2015.05.19, logstash-2015.05.20) 데이터 받아서 저장 ()안은 인덱스 이름

## 데이터 다운로드

-   [링크][링크]

## 인덱스 맵핑

-   인덱스 맵핑을 하지 않고 바로 데이터를 올릴 수 있지만(다이나믹 맵핑) 인덱스 문서를 여러 논리적 그룹으로 나누거 필드의 특성, 검색 가능성 또는 토큰화 여부(단어 분리) 등을 지정할 수 있음

{% highlight ruby %}
버전 5.\*
PUT http://localhost:9200/shakespeare
{
    "mappings": {
        "_default_": {
            "properties": {
                "speaker": {
                    "type": "string",
                    "index": "not_analyzed"
                },
                "play_name": {
                    "type": "string",
                    "index": "not_analyzed"
                },
                "line_id": {
                    "type": "integer"
                },
                "speech_number": {
                    "type": "integer"
                }
            }
        }
    }
}

버전 7.\*
PUT http://localhost:9200/shakespeare/

{
    "mappings": {
        "properties": {
            "speaker": {
                "type": "text",  // 버전 7에서는 string 으로 하니 에러 떠서 text 로 변경
                "index": "false"  // 분석되지 않을 문자열. 여러 단어라도 하나의 단위로 처리
            },
            "play_name": {
                "type": "text",
                "index": "false"
            },
            "line_id": {
                "type": "integer"
            },
            "speech_number": {
                "type": "integer"
            }
        }
    }
}

get /http://localhost:9200/shakespeare/_mapping 매핑 결과

{
    "shakespeare": {
        "mappings": {
            "properties": {
                "line_id": {
                    "type": "integer"
                },
                "play_name": {
                    "type": "text",
                    "index": false
                },
                "speaker": {
                    "type": "text",
                    "index": false
                },
                "speech_number": {
                    "type": "integer"
                }
            }
        }
    }
}

> 데이터 로드 ( git bash)
> curl -H 'Content-Type: application/x-ndjson' -XPOST 'localhost:9200/\_bulk?pretty' --data-binary @shakespeare.json
> 엘라스틱 버전 6 이상부터는 복수개의 \_type 을 지원하지 않아서 더미 데이터의 \_type 을 지워야함 ( 디폴트로 \_type 은 \_doc 로 됨)

{% endhighlight %}

{% highlight ruby %}

1. 매핑 설정
PUT http://localhost:9200/logstash-2015.05.18 logstash-2015.05.19 logstash-2015.05.20
{
    "mappings": {
        "properties": {
            "geo": {
                "properties": {
                    "coordinates": {
                        "type": "geo_point"
                    }
                }
            }
        }
    }
}

2. 매핑 결과
GET http://localhost:9200/logstash-2015.05.18/_mapping logstash-2015.05.19 logstash-2015.05.20
{
    "logstash-2015.05.18": { // logstash-2015.05.19, logstash-2015.05.20
        "mappings": {
            "properties": {
                "geo": {
                    "properties": {
                        "coordinates": {
                            "type": "geo_point"
                        }
                    }
                }
            }
        }
    }
}

3. 데이터 올리기
   curl -H 'Content-Type: application/x-ndjson' -XPOST 'localhost:9200/\_bulk?pretty' --data-binary @logs.jsonl

4)  로그 매핑 템플릿 설정하기
PUT http://localhost:9200/_template/logstash
{
    "index_patterns": [
        "logstash-*"
    ],
    "settings": {
        "number_of_shards": 2,
        "index.mapping.coerce": false
    },
    "mappings": {
        "properties": {
            "geo": {
                "properties": {
                    "coordinates": {
                        "type": "geo_point"
                    }
                }
            }
        }
    }
}


GET http://localhost:9200/_template/logstash
{
    "logstash": {
        "order": 0,
        "index_patterns": [
            "logstash-*"
        ],
        "settings": {
            "index": {
                "number_of_shards": "2",
                "mapping": {
                    "coerce": "false"
                }
            }
        },
        "mappings": {
            "properties": {
                "geo": {
                    "properties": {
                        "coordinates": {
                            "type": "geo_point"
                        }
                    }
                }
            }
        },
        "aliases": {}
    }
}
* 추가 인덱스에 따로 매핑 설정하지 않고 더미 데이터 올린 후 매핑 정보 확인하기
1) 더미 데이터 추가

POST http://localhost:9200/logstash-2015.05.21/_doc
{
    "@timestamp": "2015-05-18T09:03:25.877Z",
    "ip": "185.124.182.126",
    "extension": "gif",
    "response": "404",
    "geo": {
        "coordinates": {
            "lat": 36.518375,
            "lon": -86.05828083
        },
        "src": "PH",
        "dest": "MM",
        "srcdest": "PH:MM"
    },
    "@tags": [
        "success",
        "info"
    ],
    "utc_time": "2015-05-18T09:03:25.877Z"
}

GET http://localhost:9200/logstash-2015.05.21/_mapping

{
    "logstash-2015.05.21": {
        "mappings": {
            "properties": {
                "@tags": {
                    "type": "text",
                    "fields": {
                        "keyword": {
                            "type": "keyword",
                            "ignore_above": 256
                        }
                    }
                },
                "@timestamp": {
                    "type": "date"
                },
                "extension": {
                    "type": "text",
                    "fields": {
                        "keyword": {
                            "type": "keyword",
                            "ignore_above": 256
                        }
                    }
                },
                "geo": {
                    "properties": {
                        "coordinates": {
                            "type": "geo_point"   # 템플릿에 맞춰 들어감
                        },
                        "dest": {
                            "type": "text",
                            "fields": {
                                "keyword": {
                                    "type": "keyword",
                                    "ignore_above": 256
                                }
                            }
                        },
                        "src": {
                            "type": "text",
                            "fields": {
                                "keyword": {
                                    "type": "keyword",
                                    "ignore_above": 256
                                }
                            }
                        },
                        "srcdest": {
                            "type": "text",
                            "fields": {
                                "keyword": {
                                    "type": "keyword",
                                    "ignore_above": 256
                                }
                            }
                        }
                    }
                },
                "ip": {
                    "type": "text",
                    "fields": {
                        "keyword": {
                            "type": "keyword",
                            "ignore_above": 256
                        }
                    }
                },
                "response": {
                    "type": "text",
                    "fields": {
                        "keyword": {
                            "type": "keyword",
                            "ignore_above": 256
                        }
                    }
                },
                "utc_time": {
                    "type": "date"
                }
            }
        }
    }
}


GET http://localhost:9200/logstash-2015.05.21/_settings
{
    "logstash-2015.05.21": {
        "settings": {
            "index": {
                "mapping": {
                    "coerce": "false"      ## 템플릿에 맞춰서 들어감
                },
                "number_of_shards": "2",   ## 템플릿에 맞춰서 들어감
                "provided_name": "logstash-2015.05.21",
                "creation_date": "1591261405441",
                "number_of_replicas": "1",
                "uuid": "NKEMV35JRVqMjWNtQZ2Fhw",
                "version": {
                    "created": "7070099"
                }
            }
        }
    }
}


인덱스 이름을 패턴에 맞추지 않고 동일한 데이터 넣어봄

POST http://localhost:9200/log_test/_doc
{
    "@timestamp": "2015-05-18T09:03:25.877Z",
    "ip": "185.124.182.126",
    "extension": "gif",
    "response": "404",
    "geo": {
        "coordinates": {
            "lat": 36.518375,
            "lon": -86.05828083
        },
        "src": "PH",
        "dest": "MM",
        "srcdest": "PH:MM"
    },
    "@tags": [
        "success",
        "info"
    ],
    "utc_time": "2015-05-18T09:03:25.877Z"
}


GET http://localhost:9200/log_test/_mapping
{
    "log_test": {
        "mappings": {
            "properties": {
                ~~~
                "geo": {
                    "properties": {
                        "coordinates": {    # 적용 안됨
                            "properties": {
                                "lat": {
                                    "type": "float"
                                },
                                "lon": {
                                    "type": "float"
                                }
                            }
                        },
                        "dest": {
                            "type": "text",
                            "fields": {
                                "keyword": {
                                    "type": "keyword",
                                    "ignore_above": 256
                                }
                            }
                        },
                        "src": {
                            "type": "text",
                            "fields": {
                                "keyword": {
                                    "type": "keyword",
                                    "ignore_above": 256
                                }
                            }
                        },
                        "srcdest": {
                            "type": "text",
                            "fields": {
                                "keyword": {
                                    "type": "keyword",
                                    "ignore_above": 256
                                }
                            }
                        }
                    }
                }
            }
        }
    }
}


GET http://localhost:9200/log_test/_settings  # 템플릿 적용 안됨
{
    "log_test": {
        "settings": {
            "index": {
                "creation_date": "1591261636558",
                "number_of_shards": "1",
                "number_of_replicas": "1",
                "uuid": "6sy_ROZ2RjuyMiNPY3vZEA",
                "version": {
                    "created": "7070099"
                },
                "provided_name": "log_test"
            }
        }
    }
}





{% endhighlight %}

{% highlight ruby %}

{% endhighlight %}

[링크]: https://www.elastic.co/guide/kr/kibana/current/tutorial-load-dataset.html

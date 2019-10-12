### 1. 服务日志监控
    监控所有错误日志，当前时间到80s前。

```js
{
  "search": {
    "request": {
      "index": [
        "logstash-*"
      ],
      "body": {
        "query": {
          "bool": {
            "must": [
              {
                "match": {
                  "level": "ERROR"
                }
              },
              {
                "range": {
                  "@timestamp": {
                    "gte": "now-80s",
                    "lte": "now",
                    "format": "epoch_millis"
                  }
                }
              }
            ],
            "must_not": []
          }
        }
      }
    }
  }
}
```
    webhook输出参数：
```js
{{#payload.hits.hits}} {{_source.tags}}|{{_source.message}} %7c{{/payload.hits.hits}}
```
### 2. zk健康状况监控
    监控zk健康状况。

```js
{
  "search": {
    "request": {
      "index": [
        "metricbeat-*"
      ],
      "body": {
        "query": {
          "bool": {
            "must": [
              {
                "term": {
                  "metricset.module.keyword": "zookeeper"
                }
              },
              {
                "match_phrase": {
                  "error.message": "mntr command failed"
                }
              },
              {
                "range": {
                  "@timestamp": {
                    "gte": "now-80s",
                    "lte": "now",
                    "format": "epoch_millis"
                  }
                }
              }
            ]
          }
        }
      }
    }
  }
}
```
    webhook输出参数：
```js
zk注册中心预警|{{payload.hits.total}}|zookeeper注册注册中心异常,{{payload.hits.hits.0._source.error.message}}
```
### 3. dubbo异常服务数监控
    监控dubbo服务是否宕机

```js
{
  "search": {
    "request": {
      "index": [
        "metricbeat-*"
      ],
      "body": {
        "query": {
          "bool": {
            "must": [
              {
                "term": {
                  "tags.keyword": "nlp-log-monitor"
                }
              },
              {
                "range": {
                  "zootemAbnMsg": {
                    "gte": 1
                  }
                }
              },
              {
                "range": {
                  "@timestamp": {
                    "gte": "now-80s",
                    "lte": "now",
                    "format": "epoch_millis"
                  }
                }
              }
            ],
            "must_not": []
          }
        }
      }
    }
  }
}
```
    webhook输出参数：
```js
dubbo服务数预警|{{payload.hits.total}}|dubbo服务数异常,宕机应用数:{{payload.hits.hits.0._source.zootemAbnMsg}},宕机应用：{{payload.hits.hits.0._source.zootemAbnMsgParam}}
```
### 4. eureka异常服务数监控
    监控eureka服务是否宕机

```js
{
  "search": {
    "request": {
      "index": [
        "metricbeat-*"
      ],
      "body": {
        "query": {
          "bool": {
            "must": [
              {
                "term": {
                  "tags.keyword": "nlp-log-monitor"
                }
              },
              {
                "range": {
                  "eurekaAbnormalCount": {
                    "gte": 1
                  }
                }
              },
              {
                "range": {
                  "@timestamp": {
                    "gte": "now-80s",
                    "lte": "now",
                    "format": "epoch_millis"
                  }
                }
              }
            ],
            "must_not": []
          }
        }
      }
    }
  }
}
```
    webhook输出参数：
```js
eureka服务数预警|{{payload.hits.total}}|eureka服务数异常,宕机应用数:{{payload.hits.hits.0._source.eurekaAbnormalCount}},宕机应用：{{payload.hits.hits.0._source.eurekaAbnormalAppliction}}
```
# Graph-bed

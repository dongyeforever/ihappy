##### 创建 Mapping

建议 es 服务启动后，先执行初始化 `createIndex`，类似于 MySQL 中创建数据库和表。

```java
public void createIndex(String indexName) {
		···
			CreateIndexRequest request = new CreateIndexRequest(indexName);
      // 增加一个方法
			buildIndexMapping(request);
			CreateIndexResponse response = client.admin().indices().
					create(request)
					.actionGet();
			···
		}
	}
```



```java
public void buildIndexMapping(CreateIndexRequest request) {
		// 方式一：Map 构造 mapping
		Map<String, String> fieldData = new HashMap<>();
        fieldData.put("fielddata", "true");
		Map<String, Object> jsonMap = new HashMap<>();
        Map<String, Object> taskStatus = new HashMap<>();
        taskStatus.put("type", "text");
        Map<String, Object> timeStamp = new HashMap<>();
        timeStamp.put("type", "text" );
        Map<String, Object> beginTime = new HashMap<>();
        beginTime.put("type", "text" );
        beginTime.put("fields", fieldData);
      
        // properties
        Map<String, Object> properties = new HashMap<>();
        properties.put("task_status", taskStatus);
        properties.put("time_stamp", timeStamp);
        properties.put("beginTime", beginTime);
        
        Map<String, Object> voiceType = new HashMap<>();
        voiceType.put("properties", properties);
        jsonMap.put("voices", voiceType);
        request.mapping(Constants.ES_VR_TYPE, jsonMap);
	}

public void buildIndexMapping(CreateIndexRequest request) {
     // 方式二：jsonBuilder 构造 mapping
		try {
			XContentBuilder mappingBuilder = XContentFactory.jsonBuilder()
					.startObject()
						.startObject(Constants.ES_VR_TYPE)
							.startObject("properties")
								.startObject("task_status").field("type", "text").endObject()
								.startObject("time_stamp").field("type", "text").endObject()
								.startObject("beginTime").field("type", "text").field("fielddata", "true").endObject()
							.endObject()
						.endObject()
					.endObject();
			request.mapping(Constants.ES_VR_TYPE, mappingBuilder);
		} catch (IOException e) {
			e.printStackTrace();
		}
}
```

> 注意构造出来的 mapping 格式是否正确。尤其注意检查层级。

mapping 参考：

```json
{
    "mappings":{
        "voice_recognition_type":{
            "properties":{
                "task_status":{
                    "type":"text",
                    "fields":{
                        "keyword":{
                            "ignore_above":256,
                            "type":"keyword"
                        }
                    }
                },
                "time_stamp":{
                    "type":"text",
                    "fields":{
                        "keyword":{
                            "ignore_above":256,
                            "type":"keyword"
                        }
                    }
                },
                "is_end":{
                    "type":"text",
                    "fields":{
                        "keyword":{
                            "ignore_above":256,
                            "type":"keyword"
                        }
                    }
                },
                "channelNum":{
                    "type":"text",
                    "fields":{
                        "keyword":{
                            "ignore_above":256,
                            "type":"keyword"
                        }
                    }
                },
                "task_id":{
                    "type":"text",
                    "fields":{
                        "keyword":{
                            "ignore_above":256,
                            "type":"keyword"
                        }
                    }
                },
                "url_id":{
                    "type":"text",
                    "fields":{
                        "keyword":{
                            "ignore_above":256,
                            "type":"keyword"
                        }
                    }
                },
                "result":{
                    "type":"text",
                    "fields":{
                        "keyword":{
                            "ignore_above":256,
                            "type":"keyword"
                        }
                    }
                },
                "sequence":{
                    "type":"text",
                    "fields":{
                        "keyword":{
                            "ignore_above":256,
                            "type":"keyword"
                        }
                    }
                },
                "role_id":{
                    "type":"text",
                    "fields":{
                        "keyword":{
                            "ignore_above":256,
                            "type":"keyword"
                        }
                    }
                },
                "appid":{
                    "type":"text",
                    "fields":{
                        "keyword":{
                            "ignore_above":256,
                            "type":"keyword"
                        }
                    }
                },
                "result_flag":{
                    "type":"text",
                    "fields":{
                        "keyword":{
                            "ignore_above":256,
                            "type":"keyword"
                        }
                    }
                },
                "beginTime":{
                    "type":"text",
                    "fields":{
                        "keyword":{
                            "ignore_above":256,
                            "type":"keyword"
                        }
                    }
                },
                "sign_token":{
                    "type":"text",
                    "fields":{
                        "keyword":{
                            "ignore_above":256,
                            "type":"keyword"
                        }
                    }
                }
            }
        }
    }
}
```



#### 如果 ES 已经上线

- 复制数据

```json
POST _reindex
{
    "source": {
        "index": "dynamic_data_v2"
    },
    "dest": {
        "index": "dynamic_data_v5"
    }
}
```



- 修改别名关联

```json
POST /_aliases
{
    "actions": [
        { "remove": { "index": "dynamic_data_v2", "alias": "dynamic_data" }},
        { "add":    { "index": "dynamic_data_v5", "alias": "dynamic_data" }}
    ]
}
```



- 删除旧索引

```api
DELETE dynamic_data_v2
```


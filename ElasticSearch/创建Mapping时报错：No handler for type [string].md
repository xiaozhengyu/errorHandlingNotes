ES设置mapping时报错：

```json
{
    "error": {
        "root_cause": [
            {
                "type": "mapper_parsing_exception",
                "reason": "No handler for type [string] declared on field [city_name]"
            }
        ],
        "type": "mapper_parsing_exception",
        "reason": "Failed to parse mapping [_doc]: No handler for type [string] declared on field [city_name]",
        "caused_by": {
            "type": "mapper_parsing_exception",
            "reason": "No handler for type [string] declared on field [city_name]"
        }
    },
    "status": 400
}
```



原因：ES在5.X以上版本取消了String类型，换成了text和keyword类型
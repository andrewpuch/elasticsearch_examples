```
# Create the user index.
curl -XPUT http://localhost:9200/user -d '
{
    "settings" : {
        "index" : {
            "number_of_shards" : 5,
            "number_of_replicas" : 1
        }
    }
}
'
```

```
# Creating the mapping for the index user and type profile. 
curl -XPUT http://localhost:9200/user/_mapping/profile -d '
{
    "profile" : {
        "_timestamp" : {
            "enabled" : true,
            "path" : "created_on"
        }
        "properties" : {
            "full_name" : { "type" : "string", "store" : true },
            "bio" : { "type" : "string", "store" : true },
            "age" : { "type" : "integer" },
            "location" : { "type" : "geo_point" },
            "enjoys_coffee" : { "type" : "boolean" },
            "created_on" : {
                "type" : "date", 
                "format" : "date_time" 
            }
        }
    }
}
'
```

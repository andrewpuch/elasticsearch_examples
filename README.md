```
# Create the user index. Since we are dealing with one single node in my example the replicas are set to 0
# if you had more than 1 node in the cluster the default for number of replicas is 1 and will actually put an exact
# replica of your data on the second node. The shards by default is 5 and this basically how elasticsearch can 
# quickly find your data by sharding it out to multiple nodes. 
curl -XPUT http://localhost:9200/user -d '
{
    "settings" : {
        "index" : {
            "number_of_shards" : 5,
            "number_of_replicas" : 0
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

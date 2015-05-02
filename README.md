Creating the user index.
---
```
# Since we are dealing with one single node in my example the replicas are set to 0
# if you had more than 1 node in the cluster the default for number of replicas is 1 
# and will actually put an exact replica of your data on the second node. The shards 
# by default is 5 and this basically how elasticsearch can quickly find your data by 
# sharding it out to multiple nodes. 

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

Create the mapping for the user index and type of profile.
---
```
# Here it is important to note that some fields we are choosing to store. Why would 
# we store some and not others? Well the reason is because assuming your _source is 
# not disabled you can retrieve all of your data no matter what. Elasticsearch will 
# by default put your field in the _source. But if you enforce storing to true it will pull the
# field from the index itself rather than the _source. This is only used for large 
# data sets where the cost of parsing the source is high. I'm only using this as an 
# example for you to let you know it's possible.

curl -XPUT http://localhost:9200/user/_mapping/profile -d '
{
    "profile" : {
        "_timestamp" : {
            "enabled" : true,
            "path" : "created_on"
        },
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

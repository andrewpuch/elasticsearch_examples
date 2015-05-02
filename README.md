```
# Create the user index.
curl -XPUT http://ELASTIC_URL:9200/user
```

```
# Creating the mapping for the index user and type profile. 
curl -XPUT http://ELASTIC_URL:9200/user/_mapping/profile -d '
{
    "profile" : {
        "properties" : {
            "full_name" : {"type" : "string", "store" : true },
            "bio" : {"type" : "string", "store" : true },
            "age" : {"type" : "integer" },
            "location" : {"type" : "geo_point" },
            "enjoys_coffee" : {"type": "boolean" }
        }
    }
}
'
```

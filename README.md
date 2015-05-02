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
Now let's insert some data!
---
```
# We are going to insert three users just to show you how searching works. Here I am 
# actually telling elasticsearch what I want the ID of the document to be. You'll 
# see an ID of 1, 2 and 3. If you do not specify this elasticsearch will create an ID 
# for you by default. Sometimes this is fine because if you are dealing with more big 
# data that has no relation the ID isn't too big of a worry. However in applications 
# where you want to constantly update the data in elasticsearch such as a social network 
# you will want to add an ID.

curl -XPOST http://localhost:9200/user/profile/1 -d '
{
    "full_name" : "Andrew Puch",
    "bio" : "My name is Andrew. I am an agile DevOps Engineer who is passionate about working with Software as a Service based applications, REST APIs, and various web application frameworks.",
    "age" : 27,
    "location" : "41.1246110,-73.4232880",
    "enjoys_coffee" : true,
    "created_on" : "2015-05-02T14:45:10.000-04:00"
}
'

curl -XPOST http://localhost:9200/user/profile/2 -d '
{
    "full_name" : "Elon Musk",
    "bio" : "Elon Reeve Musk is a Canadian-American entrepreneur, engineer, inventor and investor. He is the CEO and CTO of SpaceX, CEO and product architect of Tesla Motors, and chairman of SolarCity.",
    "age" : 43,
    "location" : "37.7749290,-122.4194160",
    "enjoys_coffee" : false,
    "created_on" : "2015-05-02T15:45:10.000-04:00"
}
'

curl -XPOST http://localhost:9200/user/profile/3 -d '
{
    "full_name" : "Some Hacker",
    "bio" : "I am a haxor user who you should end up deleting.",
    "age" : 1000,
    "location" : "37.7749290,-122.4194160",
    "enjoys_coffee" : true,
    "created_on" : "2015-05-02T16:45:10.000-04:00"
}
'
```

Now time to update a record.
---
```
# Pretending we are running a real social networking application. And let's say I 
# just relocated to a new City and State! We are going to want to update our record. 
# So let's do that. It's important here that we specify "doc" and _update because if 
# you don't you will wipe out your record ;)

curl -XPOST http://localhost:9200/user/profile/1/_update -d '
{
    "doc" : {
        "location" : "40.7127840,-74.0059410"
    }
}
'
```

We noticed a bad user. Let's delete them.
---
```
# So not only will you need to update and insert records but if users are moving in and
# out of your system docs will need to be deleted. In our case for this demo we notice
# that there is a bad user. Lets remove them.

curl -XDELETE http://localhost:9200/user/profile/3
```

Now let's query that data!
---
```
# Let's search for the word "engineer" you'll notice we get both our users back because 
# engineer is in both of their profiles.

curl -XGET http://localhost:9200/user/profile/_search?pretty=true -d '
{ 
    "query" : {
        "query_string" : {
            "query" : "engineer"
        }
    }
}
'

# Now let's just search for users who have "investor" in their profile. You will notice
# we only get back Elon's profile.

curl -XGET http://localhost:9200/user/profile/_search?pretty=true -d '
{ 
    "query" : {
        "query_string" : {
            "query" : "investor"
        }
    }
}
'

# Okay, okay lets do something fun. Let's try to find ONLY the users who enjoy drinking coffee.

curl -XGET http://localhost:9200/user/profile/_search?pretty=true -d '
{ 
    "query" : {
        "bool" : {
            "must" : [
                {
                    "term" : {
                        "enjoys_coffee" : true
                    }
                }
            ]
        }
    }
}
'

```


#Links

[Assignment 12](https://github.com/datsoftlyngby/soft2019spring-databases/blob/master/assignments/assignment12.md)

```
LOAD CSV FROM "file:///some2016UKgeotweets.csv" AS row 
    FIELDTERMINATOR ";"
CREATE (:Tweet { tweetId: row[0], date: row[1], hour: row[2], username: row[3], nickname: row[4], tweetContent: row[6], `latitude`: row[9], `longitude`: row[10], place:row[12]})
```


Extract mentions for each tweet:
```
match (a:Tweet)
with extract( m in 
                filter(m in split(a.tweetContent," ") where m starts with "@" and size(m) > 1) 
                | right(m,size(m)-1))
                as mentions,a
set a.mentions = mentions
```


# Exercise 2


## Create Tweeters


```
match (t:Tweet) 
with distinct(t.username) as username
create (tw: Tweeter { username: username})
```

## Create relationship between tweeters and tweets


**Extremely slow, never finished**

```
match (tweeter:Tweeter), (tweet:Tweet)
where tweeter.username in tweet.mentions
create (tweet)-[r:MENTIONS]->(tweeter)
```

```
match (tweet:Tweet)
unwind tweet.mentions as mentions
match (tweeter:Tweeter {username: mentions})
create (tweet)-[:MENTIONS]->(tweeter)
```

## Create relationship between tweeters and  their tweets

```
match (tweet:Tweet), (tweeter: Tweeter)
where tweet.username = tweeter.username
create (tweeter)-[:TWEETED]->(tweet)
```

# Exercise 3



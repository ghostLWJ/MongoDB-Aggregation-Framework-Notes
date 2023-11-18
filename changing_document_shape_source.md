# Lab: Changing Document Shape

## For this lab, you'll be using expressions to change document shape and perform an analysis 

### Question

Using the Aggregation Framework, find a count of the number of movies that have
a title composed of one word. To clarify, "Cinderella" and "3-25" should count,
where as "Cast Away" would not.

Don't forget to append the following `counting` variable to your pipeline!

### answer

```
db.movies.aggregate(
    [
        {
            "$project": {
                "_id": 0,
                "one_word_titles": {
                    $size: {
                        "$split": [{ $toString: "$title" }, " "]
                    }
                },
            }
        },
        {
            "$match": {
                "one_word_titles": {
                    $eq: 1
                }
            }
        },
        {
            "$count": "one_word_titles"
        }
    ]
)
```

***result***
```
{
  one_word_titles: 8068
}
```
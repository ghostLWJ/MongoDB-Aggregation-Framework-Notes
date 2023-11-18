# Lab: Expression  Composition

## For this lab, you'll be composing expressions together 

### Question

Let's find how many movies in our **movies** collection are a "labor of love",
where the same person appears in ``cast``, ``directors``, and ``writers``


How many movies are "labors of love"?

To get a count, ensure you add the following to the end of your pipeline list.

### answer

```
db.movies.aggregate(
    [
        {
            "$match": {
                "writers": {
                    "$elemMatch": {
                        "$exists": true
                    }
                },
                "directors": {
                    "$elemMatch": {
                        "$exists": true
                    }
                },
                "cast": {
                    "$elemMatch": {
                        "$exists": true
                    }
                }
            }
        },
        {
            "$project": {
                "_id": 0,
                "cast": 1,
                "directors": 1,
                "writers": {
                    "$map": {
                        "input": "$writers",
                        "as": "writer",
                        "in": {
                            "$arrayElemAt": [
                                { "$split": ["$$writer", " ("] },
                                0
                            ]
                        }
                    }
                }
            }
        },
        {
            "$project": {
                "labor_of_love": {
                    "$gt": [
                        { "$size": { "$setIntersection": ["$cast", "$directors", "$writers"] } },
                        0
                    ]
                }
            }
        },
        {
            "$match": {
                "labor_of_love": true
            }
        },
        {
            "$count": "labors_of_love"
        }    
    ]
)
```

***result***
```
{
  "labors_of_love": 1597
}
```
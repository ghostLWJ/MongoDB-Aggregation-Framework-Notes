# Lab: Using Cursor-like aggregation stages

## For this lab, you'll have to use cursor-like aggregation stages to find the answer for the following scenario.

### Movie Night

Your organization has a movie night scheduled, and you've again been tasked with coming up with a selection.

HR has polled employees and assembled the following list of preferred actresses and actors.

For movies released in the **USA** with a ``tomatoes.viewer.rating`` greater
than or equal to **3**, calculate a new field called num_favs that represets how
many **favorites** appear in the ``cast`` field of the movie.

Sort your results by ``num_favs``, ``tomatoes.viewer.rating``, and ``title``,
all in descending order.

What is the ``title`` of the **25th** film in the aggregation result?

**Hint**: MongoDB has a great expression for quickly determining whether there are common elements in lists, ``$setIntersection``

### answer

```
db.movies.aggregate(
    [
        {
            "$match": {
                "tomatoes.viewer.rating": {
                    "$gte": 3
                },
                "countries": {  
                    "$in": ["USA"]
                },
                "cast": {
                    "$exists" : true 
                }
            }
        },
        {
            "$project": {
                "_id": 0,
                "tomatoes": 1,
                "title": 1,
                "num_favs": {
                "$size": {
                        "$setIntersection": [
                            "$cast",  [
                                    "Sandra Bullock",
                                    "Tom Hanks",
                                    "Julia Roberts",
                                    "Kevin Spacey",
                                    "George Clooney"
                                ]
                        ]
                    }
                }
            }
        },
        {
            "$sort": {
                "num_favs": -1,
                "tomatoes.viewer.rating": -1,
                "title": -1
            }
        },
        {
            "$skip": 24
        },
        {
            "$limit": 1
        }
    ]
)
```

***result***
```
{
  "title": "The Heat",
  "tomatoes": {
    "website": "http://www.theheatmovie.com/#/trailer",
    "viewer": {
      "rating": 3.8,
      "numReviews": 165664,
      "meter": 71
    },
    "dvd": {
      "$date": "2013-10-15T00:00:00.000Z"
    },
    "rotten": 57,
    "boxOffice": "$159.6M",
    "consensus": "The Heat is predictable, but Melissa McCarthy is reliably funny and Sandra Bullock proves a capable foil.",
    "critic": {
      "rating": 6.2,
      "numReviews": 165,
      "meter": 65
    },
    "production": "20th Century Fox",
    "lastUpdated": {
      "$date": "2015-09-12T17:47:24.000Z"
    },
    "fresh": 108
  },
  "num_favs": 1
}
```
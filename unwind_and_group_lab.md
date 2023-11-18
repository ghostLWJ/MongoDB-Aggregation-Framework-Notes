# Lab: Using ``$unwind`` and ``$group``

## For this lab, you'll be using both the ``$unwind`` and ``$group`` stages.

### Question

Let's use our increasing understanding of the Aggregation Framework to explore our
movies collection in more detail. We'd like to calculate how many movies every
**cast** member has been in, and get an average ``imdb.rating`` for each
``cast`` member.

Which cast member has the been in the most movies with **English** as an available language?

To verify that you've successfully completed this exercise please submit your answer as the sum of the number of films and average rating for this cast member.

For example, if the cast member was output like so:

    { "_id": "James Dean", "numFilms": 11, "average": 7.1 }
Then the answer would be 18.1.

### answer
```
db.movies.aggregate(
    [
        {
            $match: {
                    // languages: { $in: ["English"] }
                languages: "English"
                }
        },
        {
            $project: {
                _id: 0,
            cast: 1,
            "imdb.rating": 1
            }
        },
        {
            $unwind: "$cast"
        },
        {
            $group: {
            _id: "$cast",
            numFilms: { $sum: 1 },
            average: { $avg: "$imdb.rating" }
            }
        },
        {
            $project: {
            numFilms: 1,
            average: {
                    $divide: [
                    { $trunc: { $multiply: ["$average", 10] } },
                    10
                ]   
            }
            }
        },
        {
            $sort: { numFilms: -1 }
        },
        {
            $limit: 1
        }
    ]
)
```

***result***
```
{
  _id: 'John Wayne',
  numFilms: 107,
  average: 6.4
}
```
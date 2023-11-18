# Lab: Group Accumulators

## For this lab, you'll be using group accumulators.

### Question

In this lab, you will need to capture the highest `imdb.rating`, lowest `imdb.rating`, average, and **sample** standard deviation for all films that won an Oscar.

You may find documentation on [group accumulators](https://docs.mongodb.com/manual/reference/operator/aggregation-group/#group-accumulator-operators) helpful!

The matching stage to find films with Oscar wins is provided below.

### answer

```
db.movies.aggregate(
  [
    {
      "$match": {
          "awards": { "$regex": "Won \\d{1,2} Oscars?"}
      }
    },
    {
      $group: {
        _id: null,
        highest_rating: { $max: "$imdb.rating" },
        lowest_rating: { $min: "$imdb.rating" },
        average_rating: { $avg: "$imdb.rating" },
        deviation: { $stdDevSamp: "$imdb.rating" }
      }
    }
  ]
)
```

***result***
```
{
  "_id": null,
  "highest_rating": 9.2,
  "lowest_rating": 4.5,
  "average_rating": 7.527024070021882,
  "deviation": 0.5988145513344504
}
```
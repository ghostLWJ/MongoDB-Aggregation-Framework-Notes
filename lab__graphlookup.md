## Lab : $graphLookup

For this lab, you'll be calculating the [degrees of separation](https://en.wikipedia.org/wiki/Six_degrees_of_separation) of directors to "Steven Spielberg".

This is a bit like calculating a ["Kevin Bacon" number](https://en.wikipedia.org/wiki/Six_Degrees_of_Kevin_Bacon), but instead of all connections you will only consider connections through the `directors` graph nodes.

Complete the the `$graphLookup` and `$project` stages by correctly constructing the `graph_lookup` and `project_cast` variables below. 

To optimize the execution of `$graphLookup` stage, use a `maxDepth` of 6.

For the solution, only provide the numeric portion of the returned output to the validator.

**HINT**: `$reduce` is a powerful expression!

### answer
```
db.movies.aggregate(
    [
        {
            $match: {
                directors: "Steven Spielberg",
            },
        },
        {
            $project: {
                directors: 1,
            },
        },
        {
            $graphLookup: {
                from: "movies",
                startWith: "$directors",
                connectFromField: "directors",
                connectToField: "directors",
                as: "network",
                maxDepth: 6,
                depthField: "network_level",
            },
        },
        {
            $unwind: "$network",
        },
        {
            $project: {
                cast: "$network.cast",
                level: "$network.network_level",
            },
        },
        {
            $group: {
                _id: "$level",
                cast: { $addToSet: "$cast" },
            },
        },
        {
            $project: {
                cast: {
                    $reduce: {
                        input: "$cast",
                        initialValue: [],
                        in: {
                            $concatArrays: ["$$value", "$$this"],
                        },
                    },
                },
            },
        },
        {
            $match: {
                cast: "Woody Harrelson",
            },
        },
        {
            $sort: {
                _id: 1,
            },
        },
        {
            $project: {
                _id: 0,
                answer: "$_id",
            },
        },
        {
            $limit: 1,
        },
    ]
)
```

***result***
```
{ answer: 2 }
```
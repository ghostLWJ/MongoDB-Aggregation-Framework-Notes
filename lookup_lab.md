# Lab: Using ``$lookup``

## For this lab, you'll be using the ``$lookup``.

### Question

Which alliance from ``air_alliances`` flies the most **routes** with either a
Boeing 747 or an Airbus A380 (abbreviated 747 and 380 in ``air_routes``)?

**Note**: Begin from the ``air_routes`` collection!

### answer
```
db.air_routes.aggregate(
    [
        {
            "$match": {
                "airplane": {"$regex": "747|380"}
            }
        },
        {
            "$lookup": {
                from: "air_alliances",
                localField: "airline.name",
                foreignField: "airlines",
                as: "result"
            }
        },
        {
            $unwind: "$result"
        },
        {
            $group: {
                _id: "$result.name",
                count: { $sum: 1 }
            }
        },
        {
            $sort: {
                count: -1
            }
        }
    ]
)
```

***result***

```
{
  _id: 'SkyTeam',
  count: 8
},
{
  _id: 'Star Alliance',
  count: 6
},
{
  _id: 'OneWorld',
  count: 5
}
```
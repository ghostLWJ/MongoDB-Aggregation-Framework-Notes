### Question

Replace this with a match stage that will return documents that match on
first_name OR last_name OR birthday OR email.

Replace this with a stage that will add a field called 'matchScore', where
matchScore is the number of fields (first_name, last_name, birthday, email)
that match the source document.


### answer
```
db.people_master.aggregate(
[
  {
    $lookup: {
      from: "people_import",
      let: {
        first_name: "$first_name",
        last_name: "$last_name",
        email: "$email",
        birthday: "$birthday",
      },
      pipeline: [
        {
          $addFields: {
            matchScore: {
              $sum: [
                {
                  $cond: [
                    {
                      $eq: [
                        "$first_name",
                        "$$first_name",
                      ],
                    },
                    1,
                    0,
                  ],
                },
                {
                  $cond: [
                    {
                      $eq: [
                        "$last_name",
                        "$$last_name",
                      ],
                    },
                    1,
                    0,
                  ],
                },
                {
                  $cond: [
                    {
                      $eq: ["$email", "$$email"],
                    },
                    1,
                    0,
                  ],
                },
                {
                  $cond: [
                    {
                      $eq: [
                        "$birthday",
                        "$$birthday",
                      ],
                    },
                    1,
                    0,
                  ],
                },
              ],
            },
          },
        },
        {
          $addFields: {
            matchScore: {
              $sum: [
                {
                  $cond: [
                    {
                      $eq: [
                        "$first_name",
                        "$$first_name",
                      ],
                    },
                    1,
                    0,
                  ],
                },
                {
                  $cond: [
                    {
                      $eq: [
                        "$last_name",
                        "$$last_name",
                      ],
                    },
                    1,
                    0,
                  ],
                },
                {
                  $cond: [
                    {
                      $eq: ["$email", "$$email"],
                    },
                    1,
                    0,
                  ],
                },
                {
                  $cond: [
                    {
                      $eq: [
                        "$birthday",
                        "$$birthday",
                      ],
                    },
                    1,
                    0,
                  ],
                },
              ],
            },
          },
        },
        {
          $match: {
            matchScore: { $gte: 3 },
          },
        },
        {
          $sort: { matchScore: -1 },
        },
        {
          $limit: 5,
        },
      ],
      as: "matches",
    },
  },
  {
    $match: {
      "matches.matchScore": 3,
    },
  },
  {
    $count: "len"
  }
])
```

***result***
```
{
  "len": 19
}
```
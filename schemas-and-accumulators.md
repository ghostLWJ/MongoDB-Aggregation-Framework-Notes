### Question

The documents in this collection represent orders made to an online retail
store. The purchases field is an array containing each item order with its
associated quantity and unit price.

Your goal for this exercise is to add the following fields to each document
using the aggregation framework:

order_total - This field represents the total for the order. i.e. The sum of each item's
unit_price multiplied by its quantity.

order_quantity - This field represents the total number of products ordered. i.e. The sum of
each item's quantity.

mean_order_unit_price - This field represents the mean unit price of the products ordered. i.e. The
average of each item's unit_price.

mean_order_quantity - This field represents the mean quantity of the products ordered. i.e. The
average of each item's quantity.

Hint: You don't need to use $unwind to make this work.

To verify that you've successfully completed this exercise, what is the
order_total of the largest single order?

### answer

```
db.orders.aggregate([
  {
    $addFields: {
      order_total: {
        $reduce: {
          input: "$purchases",
          initialValue: 0,
          in: { 
            $sum: [
              "$$value",
              { $multiply: [ "$$this.quantity", "$$this.unit_price" ] }
            ]
          }
        }
      }
    }
  },
  {
    $addFields: {
      order_quantity: {
        $sum: "$purchases.quantity"
      }
    }
  },
  {
    $addFields: {
      mean_order_unit_price: {
        $divide: [
          "$order_total",
          "$order_quantity"
        ]
      }
    }
  },
  {
    $addFields: {
      mean_order_quantity: {
        $divide: [
          "$order_quantity",
          { $size: "$purchases" },
        ]
        
      }
    }
  },
  {
    $sort: {
      order_total: -1
    }
  },
  {
    $limit: 1
  }
])
```

***result***

```
{
  _id: 581483,
  date: 2011-12-09T09:15:00.000Z,
  customer_id: 16446,
  country: 'United Kingdom',
  purchases: [
    {
      description: 'PAPER CRAFT , LITTLE BIRDIE',
      quantity: 80995,
      stock_code: '23843',
      unit_price: 2.08
    }
  ],
  order_total: 168469.6,
  order_quantity: 80995,
  mean_order_unit_price: 2.08,
  mean_order_quantity: 80995
}
```
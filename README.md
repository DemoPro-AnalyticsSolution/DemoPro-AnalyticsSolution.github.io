DemoPro-AnalyticsSolution.github.io
===================================


Analytics reporting system:
Problem: Reporting systems usually have very complex queries to render reports.  Instead of having the complexity of the query to render, the complexity is placed on storing the data in another location before the analytics is applied.

In order to apply the Report there needs to be a number of items in place.

1: The report needs to know which aggregations to apply to the pre-calculated data
2:  There needs to be a way to place summarized data into a separate location.

Workflow:  User creates a summarized dataset with metrics.  These metrics may be as simple as this example.
Imagine we have a store. We want to do metrics on our users buying behavior.  We want to know how well we did per product and we have sales data and meta-data about our products.

Let us say that we have an Items table:
Item ID | Item Name | Item Description | Purchased Amount | Inventory Count | Repurchase Quantity | Lower Limit

Purchases Table could look like this:
Purchase ID, Customer ID, Created Date, Invoiced Amount, Total (As calculated from Sales) 

Let us say we have customers:
Customer ID, First Name, Last Name, Address

The following table represents sales of items:
Purchase Item ID, Purchase ID, Item ID, Sold Price, Tax

As you can see, just 4 relations get pretty complex.  In order to do analytics it may be helpful to see data like this.
Customer Name, Item Name, Sold Price, Created Date

In order to produce this type of relation, the following needs to be done in SQL.
```sql
Select Last Name + ‘, ’First Name, Item Name, Sold Price, Created Date
From Purchases 
join Item on Purchase.Item ID = Item.Item ID
Join  Customers on Purchases. Customer ID = Customers.Customer ID
Join Sales Items on Puchases.Purchase ID = Sales Items.Purchase ID
```

We could provide a simple filter, but this relationship could get REALLY big.  Imagine you are doing 100,000 transactions a second.

We would like to aggregate some of this data so we can make decisions later.
Let us say today I want to group the items on my dashboard by the top Items Sold yesterday.  This is easy enough because the initial data source was aggregated.  This means we would do the following simple query.
```sql
Select top 10 Item Name, Count(*) 
From NewItemAggregate
Where Created Date = Yesterday
Group by Item Name
Order by count(*) desc
```
I would like this system to do two things.  #1: Allow the user to create the aggregate table base on the complex join.  Then, on a regular basis aggregate the numbers and store in a new location such as NewItemAggregate.  The system should be smart enough to follow foreign keys and create a dataset view based on columns from the join.  There may be simple metrics at this level, but nothing detailed.

The second item would be a drag / drop interface to select the Column from the aggregate table, then perform a metric within that selection such as Count(*).  As you would imagine, group by would become a powerful tool to perform additional calculations.  The first iteration would allow something as simple as above.  Then, we could work on more advanced calculations such as Variance, standard deviation and Regression analysis including Error, Bias, and P values.

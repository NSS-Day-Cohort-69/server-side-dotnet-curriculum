# :convenience_store: EF Corner Store
Ernie Fairchild has hired us to build a new point-of-sale system for his convenience store. 

## Setup
1. Request the Github Classroom link for the assignment from your instructor.
1. Clone your new repo that Github classroom created to your local computer and open it in VS Code.
1. For the rest of these instructions, the files will be referring to files inside the `CornerStore` folder. You can look at the `CornerStore.Tests` folder if you wish, but you do not need to modify any code there.
1. The project code is already configured to use EF Core and Npgsql. Running `dotnet restore` in the project directory will resolve all missing dependencies.
1. Initialize user secrets for this project, and add a secret called `CornerStoreDbConnectionString` with the connection string for a local database called `CornerStore`. Be sure to include you username and password for your local Postgresql `postgres` user. 

> IMPORTANT: There will be many compiler errors in the Tests project at the outset, because many of the entities that the tests rely on in the API will not exist yet (and need to be completed by you!). You will know that you can try `dotnet test` when those compiler errors are gone. This of course does not mean that all the tests will pass, but you have at least properly created all of the entities and the DbContext so that you can start writing and testing the endpoints. 

## Entities
Add the following classes with their properties to the `Models` folder. Use Data Annotations to ensure that properties that should not be nullable or ignored in the database are configured correctly.

1. Cashier
    - Id (primary key)
    - FirstName (not nullable)
    - LastName (not nullable)
    - FullName (computed)
1. Product
    - Id (primary key)
    - ProductName (not nullable)
    - Price (not nullable)
    - Brand (not nullable)
    - CategoryId (not nullable, foreign key)
1. Category
    - Id (primary key)
    - CategoryName (not nullable)
1. Order 
    - Id (primary key)
    - CashierId (not nullable, foreign key)
    - Total (computed - add the logic to sum the product prices times the quantity in the `OrderProduct`s)
    - PaidOnDate (nullable DateTime to record when the transaction was completed)
1. OrderProduct
    - ProductId (not nullable, foreign key)
    - OrderId (not nullable, foreign key)
    - Quantity (not nullable)

In addition to the properties above that correspond to database columns, add other properties that reflect these relationships that will allow related data to be nested:
1. A cashier can have many orders, an order is only handled by one cashier
1. A product belongs in one category. A category can be associated with many products
1. An order may have many associated  order products, but an order product will be associated with one product.
1. Make an ERD for this database

## Seeding the database
1. Use the `OnModelCreating` method in the `CornerStoreDbContext` class to add cashiers, products, categories, and orders. Also ensure that orders have order products associated with them. 
1. Use the migration tool to create your database

## Endpoints
Implement the following endpoints in the API (return the correct HTTP status codes and response bodies for each endpoint!):

### `/cashiers`
1. Add a cashier
1. Get a cashier (include their orders, and the orders' products).

### `/products`
1. Get all products with categories. If the `search` query string param is present, return only products whose names or category names include the `search` value (ignore case).
1. Add a product
1. Update a product
### `/orders`
1. Get an order details, including the cashier, order products, and products on the order with their category.
1. Get all orders. Check for a query string param `orderDate` that only returns orders from a particular day. If it is not present, return all orders. 
1. Delete an order
1. Create an Order (with products!)


When you have completed all of the tasks, push the result to the main branch (you can push more often, just make sure that the final working result is pushed as well). 

## Extra Challenge
This feature is not required for the tests to pass, but is a fun challenge if you would like to try it:

`/products/popular` - Get the most popular products, determined by which products have been ordered the most times (HINT: this requires using `GroupBy` to group the OrderProducts by ProductId, then using `Sum` to add up all the Quantities of the OrderProducts in each group). Check for a query string param called `amount` that says how many products to return. Return five by default.


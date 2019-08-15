


# Metrilo custom integration tutorial

**This guide is from developers for developers. If your are not a developer, please find one and come back later :)**

:warning: **We strongly recommend doing this on Staging/Beta environment first to make sure the integration is smooth and nothing breaks down.**


To do your custom integration with Metrilo and send data to our servers you have to use the **Metrilo API**. There is a documentation about it written according to the [OpenAPI Specification](https://swagger.io/docs/specification/about/). Please copy the content of this [file](metrilo_open_api_specification.yml) to the [swagger editor](https://editor.swagger.io/).

There are 3 things you have to do to make Metrilo work on your website. These are:
* [Import of resources](#import)
* [Frontend integration](#frontend-integration)
* [Backend integration](#backend-integration)

## Import
Before doing any [Frontend](#frontend-integration) and [Backend](#backend-integration) integrations, you have to sync(import) your data with Metrilo. These is the **categories**, **products**, **customers** and **orders** you have on your website.
:warning: Import of categories and products is **crucial** because the [Frontend integration](#frontend-integration) depends on it. Import of customers and orders is necessary only if you want to sync any historical data with Metrilo.
:warning: The order in which the import must be done is categories -> products -> customers -> orders. This is because the orders are dependent on the customers and the products, and the products on the categories.


### Implementation
To ease your life, we have made four endpoints which you could use. You could use them to import a multiple records at once. Just keep the size of a single request **less than 5 MB**.

#### Import endpoints
* /product/batch
* /category/batch
* /customer/batch
* /order/batch


## Frontend integration
When a customer of yours do some actions on your website you have to tell Metrilo about it. Such actions could be view a page, add a product to the cart, view a product, make a search, etc. To notify Metrilo about it you have to send the required data by using a few Frontend endpoints. Depending on the customer action there is a different endpoint you have to call. For example, when a customer **views a product**, you should call **/product/view**. This will create a view product event in the customer's current session.

### Implementation
For your convenience we have made a javascript library that will simplify calling the [Frontend endpoints](#frontend-endpoints). You could load it on your website and use the provided functions with it. [Here](frontend_integration_library.md) you could check how to use the library.

#### Frontend endpoints
* /article/view
* /category/view
* /product/view
* /cart/add
* /cart/remove
* /checkout
* /custom
* /page/land
* /page/view
* /search
* /visitor/apply-tags
* /visitor/identify

### Examples of when to use this endpoints
Customer actions and what you have to do with the loaded library(the params are not included):
* visits the Homepage -> call metrilo.viewPage
* visits an article page -> call metrilo.viewArticle
* visits a category page -> call metrilo.viewCategory
* visits a product page -> call metrilo.viewProduct
* visits another page (e.g. /thank-you) -> call metrilo.viewPage
* types something in the search bar -> call metrilo.search
* adds a product to his cart -> call metrilo.addToCart
* removes a product to his cart -> call metrilo.removeFromCart
* checks the content of his cart -> call metrilo.viewPage
* goes to the checkout page -> call metrilo.checkout
* enters his email in some html form -> call metrilo.identify


## Backend integration
When you create or update **categories**, **products**, **customers** and **orders** you have to tell Metrilo about it. These are the objects (resources) around which most of the customer actions are made and their IDs are the ones used for the [Frontend endpoints](#frontend-endpoints).

### Implementation
The Backend integration has to be done from your backend. Therefore, we could not provide any pre-made library about it as the implementation is bound to your backend logic and programming language.
However, a simple rule is any time you create or update a **category**, **product**, **customer** or **order**, call the relevant [Backend endpoint](#backend-endpoints) with the required data. Keep in mind that if you try to call any [Frontend endpoint](#frontend-endpoints) for not existing resource in Metrilo, your request won't be processed.

**Important** to know is that the data you send will override any existing data. For example, if you make a call about product *A* with categories *C1* and *C2* and then you make another call only with category *C3*, then the first two categories will be removed from the product.

#### Backend endpoints

* /category
* /product
* /customer
* /order

### Example of when to use these endpoints:
* a customer signs up for first time -> call /customer to create it
* a customer profile is updated -> call /customer to update it
* a new category is added to a product -> call /product to update its categories
* a new product is created -> call /product to create it
* a new order is made -> call /order to create it
* an order details are changed -> call /order to update it
* a customer doesn't sign up, but makes an order just by filling in his email(guest customer) -> call /customer and then /order to create both resources

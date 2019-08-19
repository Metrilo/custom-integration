
# Metrilo custom integration tutorial

**This guide is from developers for developers. If your are not a developer, please find one and come back later :)**

:warning: **We strongly recommend doing this on Staging/Beta environment first to make sure the integration is smooth and nothing breaks down.**


To do your custom integration with Metrilo and send data to our servers you have to use the **Metrilo API**. Its documentation is written according to the [OpenAPI Specification](https://swagger.io/docs/specification/about/). Please copy the content of this [file](metrilo_open_api_specification.yml) to the [swagger editor](https://editor.swagger.io/) for a better look. In the documentation you will find what endpoints the Metrilo API serves and what data they require.

In general we can distinguish 3 types of endpoints according to their purpose. Also, this is what you have to do to complete your integration. These are endpoints for:
* [Import of resources](#import-of-resources)
* [Frontend events](#frontend-events)
* [Backend events](#backend-events)

<br/><br/>
## Import of resources
  Before doing any [Frontend](#frontend-events) and [Backend](#backend-events) events, you have to sync (import) your data with Metrilo. These are the **categories**, **products**, **customers** and **orders** you have in your database. Metrilo needs information about them, because these are the objects (resources) related to your customers actions.

:warning: Keep in mind that:
  * import of categories and products is **crucial** because the [Frontend events](#frontend-events) depends on it.
  * import of customers is also **crucial** because new order events for old customers of yours depend on them([Backend events](#backend-events)).
  * import of orders is necessary only if you want to sync any historical data with Metrilo.

### Implementation
:warning: There are a few **very important** rules you must follow when doing the import.
* The order in which the import must be done is **categories -> products -> customers -> orders**. This is because the orders are dependent on the customers and the products, and the products are dependent on the categories.
* Orders will not be imported if Metrilo has never seen the products.
* Just keep the size of a single request **less than 5 MB**.

### Import endpoints
* /category/batch
* /product/batch
* /customer/batch
* /order/batch

All of the endpoints can import multiple records at once. More detailed information about what data they require you can find in the OpenAPI documentation mentioned above.

### Examples of when to use these endpoints
| Lets say you have                                          | you have to call ... with the required data about each resource |
| :--------------------------------------------------------- | :-------------------------------------------------------------- |
| categories shirts, jeans and hats                          | /category/batch                                                 |
| products T-shirt, short sleeve shirt and Long sleeve shirt | /product/batch                                                  |
| customers Marry, Molly and Jane                            | /customer/batch                                                 |
| orders #100, #101 and #102                                 | /order/batch                                                    |

<br/><br/>
## Frontend events
When a customer of yours do some actions on your website you have to tell Metrilo about it. Such actions could be view a page, add a product to the cart, view a product, make a search, etc. These are the actions that will be saved in the customer's current session and on the base of which Metrilo will do its magic.

### Implementation
To notify Metrilo about the customer actions you have to send the required data by using the [Frontend endpoints](#frontend-endpoints). Depending on the customer action there is a different endpoint you have to call. Fortunately and for your convenience we have made a **javascript library** that will simplify calling these endpoints. You could load it on your website and use the provided functions with it. [Here](frontend_integration_library.md) you could check how to load and use the library.

:warning: Keep in mind that:
* if you try to call any [Frontend endpoint](#frontend-endpoints) for not existing resource in Metrilo, your request won't be processed.
* if you have any caching, make sure not to cache any frontend events

### Frontend endpoints
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

Since you will most probably use the **javascript library** there is no need to call directly the endpoints.

### Examples of when to use the javascript library
| customers                            | you have to call ... with the required params |
| :----------------------------------- | :-------------------------------------------- |
| visit the Homepage                   | metrilo.viewPage                              |
| visit an article page                | metrilo.viewArticle                           |
| visit a category page                | metrilo.viewCategory                          |
| visit a product page                 | metrilo.viewProduct                           |
| visit another page (e.g. /thank-you) | metrilo.viewPage                              |
| type something in the search bar     | metrilo.search                                |
| add a product to their cart          | metrilo.addToCart                             |
| remove a product from their cart     | metrilo.removeFromCart                        |
| check the content of their cart      | metrilo.viewPage                              |
| visit the checkout page              | metrilo.checkout                              |
| enter their email in some html form  | metrilo.identify                              |


<br/><br/>
## Backend events
When you create or update **categories**, **products**, **customers** or **orders** you have to notify Metrilo about it. These are the same objects (resources) you did import for, however here you can manage them one by one. To do this you can make a request to the relevant [Backend endpoint](#backend-endpoints) for the resource that was created/updated.

### Implementation
The Backend events has to be done from your backend. Therefore, we could not provide any pre-made library about it as the implementation is bound to your backend logic and programming language.
However, a simple rule is any time you create or update a **category**, **product**, **customer** or **order**, call the relevant [Backend endpoint](#backend-endpoints) with the required data.

:warning: Keep in mind that:
* if you try to make any [Frontend event](#frontend-events) for not existing resource in Metrilo, your request won't be processed.
* most of the times the data you send will override any existing data. For example, if you make a call about product *A* with categories *C1* and *C2* and then you make another call only with category *C3*, then the first two categories will be removed from the product. However, sometimes the data will be merged(e.g. product's options). If the data is merged, it will be explicitly mentioned in the OpenAPI documentation.

### Backend endpoints

* /category
* /product
* /customer
* /order

More detailed information about what data they require you can find in the OpenAPI documentation mentioned above.

### Example of when to use these endpoints:
| when                                                                                        | you have to call ... with the required data        |
| :------------------------------------------------------------------------------------------ | :------------------------------------------------- |
| a customer signs up for first time                                                          | /customer to create it                             |
| a customer profile is updated                                                               | /customer to update it                             |
| a new category is added to a product                                                        | /product to update its categories                  |
| a new product is created                                                                    | /product to create it                              |
| a new order is made                                                                         | /order to create it                                |
| an order's status or details are changed                                                    | /order to update it                                |
| a customer doesn't sign up, but makes an order just by filling in his email(guest customer) | /customer and then /order to create both resources |


<br/><br/>
## Responses from the endpoints
The following responses are valid for all of the endpoints.

| Code | Message |  
| --- | --- |  
| 204 | Success response. |  
| 400 | Bad request from client. |  
| 401 | Project token is invalid. |  
| 402 | Project requires payment. |  
| 403 | The IP that the request came from is ignored. |  
| 500 | Error from server while processing request. |  
| 502 | Error from server accepting request. |  

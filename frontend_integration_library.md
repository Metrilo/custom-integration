# Metrilo's JS tracking library

:warning: **We strongly recommend doing this on Staging/Beta environment first to make sure the integration is smooth and nothing breaks down.**

Metrilo provides a javascript library for integration with your custom e-commerce solution. It helps you track the events that the people visiting your shop do, such as viewing specific pages or products, adding products to the cart, making orders, etc. The provided data is transformed into different reports and analytics metrics you could benefit from.
For more information about Metrilo and its features please visit [the official website](https://www.metrilo.com/).

## Integration
### Installing the JS library
Insert the following script tag in the `<head>` tag of the page you want to track.
```
<script type="text/javascript" src="https://trk.mtrl.me/tracking.js?token=<YOUR_PROJECT_TOKEN>"></script>
```
Consider the `<YOUR_PROJECT_TOKEN>` parameter - here you should put your project's API token, which you could find on the Settings page of your project in Metrilo.

:warning: **You should add the script tag on every page you want to track!**

## Tracking
In order to start tracking visitor activity you have to call the functions defined in the `window.metrilo` object. See the sections below for more information.
* [Identify a user](#identify-a-user)
* [Tracking events](#tracking-events)

### Tracking responses
The following responses are valid for all of the events that the tracking library provides.

| Code | Message |  
| --- | --- |  
| 204 | Success response. |  
| 400 | Bad request from client. |  
| 401 | Project token is invalid. |  
| 402 | Project requires payment. |  
| 403 | The IP that the request came from is ignored. |  
| 500 | Error from server while processing request. |  
| 502 | Error from server accepting request. |  

### Identify a user
When a customer comes to the store and he is not logged in Metrilo treats him as an anonymous visitor. Metrilo adds a unique identifier to his session, so it could attribute his actions to this identifier. Once he provides his email (e.g. logs in, signs up, fills in a subscription form, makes an order, etc.) you have to call ```metrilo.identify(customerEmail)```. This will associate the unique identifier with the email. Moreover, all events that the visitor has while being an anonymous one will be merged with the events he has as identified (*if any). It is enough to call the identify only once, the first time the visitor enters his email. If you are not sure that you have already called it, just check for the existence of email in the cookie named **cbuid**. If there is an email, no need to call it. This cookie contains either the unique identifier or the email and part of the identify function is to change the content  from the unique identifier to the email.

**\*** sometimes customers might log out, use different devices, browsers or delete their cookies. Every time one of this happens Metrilo will create a new anonymous visitor with a new unique identifier. However, once the visitor identifies himself (enters his email and you call metrilo.identify) Metrilo will check for the existence of a customer with the entered email and if there is any, it will merge him with the anonymous visitor.

```javascript
  var visitorEmail = 'johnnybravo@gmail.com'; // Visitor's email address.
  metrilo.identify(visitorEmail);
```

### Tracking events
The tracking events which the tracking library supports are:
* [search](#search)
* [viewPage](#viewPage)
* [viewArticle](#viewArticle)
* [viewCategory](#viewCategory)
* [viewProduct](#viewProduct)
* [addToCart](#addToCart)
* [removeFromCart](#removeFromCart)
* [checkout](#checkout)
* [applyTags](#applyTags)
* [customEvent](#customEvent)

Some of the event functions accept `params`, which is a hash with *optional* parameters. The other parameters (besides `params`) are **required**.

#### search ####

Visitor has searched in the shop (this supports only a plain query string without further filtering). This event was not available in the old tracking library.
```javascript
  var query = 'a plant'; // Search query string that the visitor entered in the search form.
  var url = 'http://myshop.com/search/?s=a+plant'; // The URL that the visitor landed on when submitting the search form.

  metrilo.search(query, url);
```

#### viewPage ####
Visitor has visited a shop page.

```javascript
  var url = 'http://myshop.com/amazing-page' // Absolute URL linking the the visited page.
  var params = {
    name: 'My Amazing page' // The name of the page in the shop.
  };

  metrilo.viewPage(url, params);
```

#### viewArticle ####
Visitor has visited an article page.

```javascript
  var articleId = '42'; // Unique ID of article resource in the shop's database.
  var params = {
    name: 'How to increase sales' // Article display name in the shop.
    url: 'http://myshop.com/how-to-increase-sales' // Landing page for the article resource.
  };

  metrilo.viewArticle(articleId, params);
```

#### viewCategory ####
Visitor has visited a category page.

```javascript
  var categoryId = '9912'; // Unique ID of category resource in the shop's database.
  metrilo.viewCategory(categoryId);
```

#### viewProduct ####
Visitor has visited a product page.

```javascript
  var productId = '312'; // The ID of the product in the shop's database.
  metrilo.viewProduct(productId);
```

#### addToCart ####
Visitor has added a product to the cart.

```javascript
  var productId = '312'; // The ID of the added product
  var quantity = 2; // The quantity of added items

  metrilo.addToCart(productId, quantity);
```

#### removeFromCart ####
Visitor has removed a product from the cart.

```javascript
  var productId = '312'; // The ID of the removed product
  var quantity = 1; // The quantity of removed items

  metrilo.removeFromCart(productId, quantity);
```

#### checkout ####
Visitor has started the checkout process.

```javascript
  metrilo.checkout();
```

#### applyTags ####
Adds tags to a visitor.  
*Note*: the case of the tags doesn't matter.

```javascript
  var tags = ['regular', 'big_spender'] // Tag labels that are assigned to users in the Metrilo application.
  metrilo.applyTags(tags);
```

#### customEvent ####
Sends a custom event (anything you want to track).

```javascript
  var customEventName = 'paypal_payment' // The name of the custom event that the shop sends to Metrilo.
  metrilo.customEvent(customEventName);
```

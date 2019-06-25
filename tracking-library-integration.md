# Metrilo's JS tracking library

:warning: :warning: :warning: **We strongly recommend doing this on Staging/Beta environment first to make sure the integration is smooth and nothing breaks down.**

Metrilo provides a javascript library for integration with your custom e-commerce solution. It helps you track the events that the people visiting your shop do, such as viewing specific pages or products, adding products to the cart, making orders, etc. The provided data is transformed into different reports and analytics metrics you could benefit from.
For more information about Metrilo and its features please visit [the official website](https://www.metrilo.com/).

## Integration
### Installing the JS library
Insert the following script tag in the `<head>` tag of the page you want to track.
```
<script type="text/javascript" src="https://tracking.metrilo.com/tracking.js?token=<YOUR_PROJECT_TOKEN>"></script>
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
Identifies current visitor explicitly by email. The common use cases where identification could be done are:

* After order, because each order contains customer details
* After subscription for some resource on your web-site
* After login

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
Visitor has landed on an article page.

```javascript
  var articleId = '42'; // Unique ID of article resource in the shop's database.
  var params = {
    name: 'How to increase sales' // Article display name in the shop.
    url: 'http://myshop.com/how-to-increase-sales' // Landing page for the article resource.
  };

  metrilo.viewArticle(articleId, params);
```

#### viewCategory ####
Visitor has opened a category page.

```javascript
  var categoryId = '9912'; // Unique ID of category resource in the shop's database.
  metrilo.viewCategory(categoryId);
```

#### viewProduct ####
Visitor has opened a product page.

```javascript
  var productId = '312'; // The ID of the product in the shop's database.
  metrilo.viewProduct(productId);
```

#### addToCart ####
Visitor adds a product to the cart.

```javascript
  var productId = '312'; // The ID of the added product
  var quantity = 2; // The quantity of added items

  metrilo.addToCart(productId, quantity);
```

#### removeFromCart ####
Visitor removes a product from the cart.

```javascript
  var productId = '312'; // The ID of the removed product
  var quantity = 1; // The quantity of removed items

  metrilo.removeFromCart(productId, quantity);
```

#### checkout ####
Visitor started the checkout process.

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

### Differences between and old and the new tracking library

The new tracking library uses a separate function for each tracking event, unlike the old tracking library, which uses a single function for every call (`event()`). The tracking functions in the new tracking accept the mandatory parameters as function arguments and the optional ones - as hash as the last parameter:
```
metrilo.event('<event_type>', eventParams); // OLD TRACKING
metrilo.<event_function>(mandatoryParams, optionalParams); // NEW TRACKING
```

See the following example:
```javascript
  // OLD TRACKING
  var pageviewParams = { url: 'http://myshop.com/amazing-page', page_title: 'My amazing page' }
  metrilo.event('pageview', pageviewParams) // Using the `event` function here, which accepts the name of the event as the first argument and all the others as hash

  // NEW TRACKING
  var url = 'http://myshop.com/amazing-page'
  var params = { name: 'My amazing page' };
  metrilo.viewPage(url, params); // A separate function, which accepts the mandatory URL as the first parameter and the optional name of the page as part of the optional parameters hash
```

Check the following examples in order to update your code easily.

*Identify a user*
:warning: In the previous version of the tracking library it was possible to provide additional parameters (first name, last name, company, tags, etc.) to the visitor in the identify call, but __not__ in this version. The new tracking accepts only the email of the visitor. If you want to apply tags, use the [applyTags](#applytags) event.

Example:
```javascript
  // OLD TRACKING
  var identifyParams = {
    first_name: 'Johny',
    last_name: 'Bravo',
    email: 'johnybravo@gmail.com',
    tags: ['from-sale', 'early_subscriber'],
    company: 'Metrilo',
  };

  metrilo.identify('johnybravo@gmail.com', identifyParams);

  // NEW TRACKING
  metrilo.identify('johnnybravo@gmail.com'); // Just the email of the user, no other parameters!
  metrilo.applyTags(['from-sale', 'early_subscriber']) // A separate function for applying tags!
```

*viewPage*
```javascript
  // OLD TRACKING
  var pageViewed = { uri: 'http://myshop.com/amazing-page', page_title: 'My amazing page' };
  metrilo.event('pageview', pageViewed);


  // NEW TRACKING
  var url = 'http://myshop.com/amazing-page' // Absolute URL linking the the visited page.
  var params = {
    name: 'My Amazing page' // The name of the page in the shop.
  };

  metrilo.viewPage(url, params);
```

*viewArticle*
```javascript
  // OLD TRACKING
  var articleParams = {
    id: '42',
    name: 'How to increase sales',
    url: 'http://myshop.com/how-to-increase-sales'
  };
  metrilo.event('view_article', articleParams);


  // NEW TRACKING
  var articleId = '42'; // Unique ID of article resource in the shop's database.
  var params = {
    name: 'How to increase sales' // Article display name in the shop.
    url: 'http://myshop.com/how-to-increase-sales' // Landing page for the article resource.
  };

  metrilo.viewArticle(articleId, params);
```

*viewCategory*
```javascript
  // OLD TRACKING
  var categoryParams = {
    id: '9912',
    name: 'Accessories'
  };
  metrilo.event('view_category', categoryParams);

  // NEW TRACKING
  var categoryId = '9912'; // Unique ID of category resource in the shop's database.
  metrilo.viewCategory(categoryId);
```

*viewProduct*
Visitor has opened a product page.

Unlike the old `view_product` call, the `viewProduct` one in the new tracking accepts **only** the ID of the product as a parameter.
```javascript
  // OLD TRACKING
  var productParams = {
    id: '312',
    price: 134.50,
    name: 'Cool sunglasses',
    url: 'http://fanstore-johnybravo.com/product/coolsunglasses',
    image_url: 'http://fanstore-johnybravo.com/product/coolsunglasses/coolsunglasses.jpg',
    categories: [
      {
        id: '9912',
        name: 'Accessories'
      },
      {
        id: '8009',
        name: 'Summer'
      }
    ]
  }

  metrilo.event('view_product', productParams);

  // NEW TRACKING
  var productId = '312'; // The ID of the product in the shop's database.
  metrilo.viewProduct(productId);
```

*addToCart*
```javascript
  // OLD TRACKING
  var productParams = {
    id: '312',
    price: 134.50,
    name: 'Cool sunglasses',
    url: 'http://fanstore-johnybravo.com/product/coolsunglasses',
    image_url: 'http://fanstore-johnybravo.com/product/coolsunglasses/coolsunglasses.jpg'
  };

  metrilo.event('add_to_cart', productParams);

  // NEW TRACKING
  var productId = '312'; // The ID of the added product
  var quantity = 2; // The quantity of the added items

  metrilo.addToCart(productId, quantity);
```

*removeFromCart*
```javascript
  // OLD TRACKING
  var productParams = { id: '312' }
  metrilo.event('remove_from_cart', productParams);

  // NEW TRACKING
  var productId = '312'; // The ID of the removed product
  var quantity = 1; // The quantity of the removed items

  metrilo.removeFromCart(productId, quantity);
```

*checkout*
```javascript
  // OLD TRACKING
  metrilo.event('checkout_start');
  
  
  // NEW TRACKING
  metrilo.checkout();
```

*viewCart*
The `view_cart` has been removed. If you still want to track it, you can use *customEvent*.

*applyTags*
```javascript
  // OLD TRACKING
  metrilo.event('apply_tags', { tags: ['regular', 'big_spender'] });

  // NEW TRACKING
  var tags = ['regular', 'big_spender'] // Tag labels that are assigned to users in the Metrilo application.
  metrilo.applyTags(tags);
```

*customEvent*
```javascript
  // OLD TRACKING
  metrilo.event('paypal_payment');

  // NEW TRACKING
  var customEventName = 'paypal_payment' // The name of the custom event that the shop sends to Metrilo.
  metrilo.customEvent(customEventName);
```

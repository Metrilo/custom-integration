# Differences between and old and the new tracking library

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
:warning: In the previous version of the tracking library it was possible to provide additional parameters (first name, last name, company, tags, etc.) to the visitor in the identify call, but __not__ in this version. The new tracking accepts only the email of the visitor. If you want to apply tags, use the [applyTags](../blob/master/frontend_integration_library.md#applytags) event.

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

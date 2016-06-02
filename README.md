## Metrilo custom integration tutorial ##

_This guide is from developers for developers. If your are not a developer, please find one and come back later :)_



**:warning: :warning: :warning:
We strongly recommend doing this on Staging/Beta environment first to make sure the integration is smooth and nothing breaks down.**

Metrilo provides a javascript library for integration with your custom e-commerce solution.
The same library is used for our Magento and Woocommerce plugins so they can be a valuable source when integrating Metrilo with your website. You can find them on:
- [Magento Plugin](https://github.com/Metrilo/Magento-Plugin)
- [Woocommerce Plugin](https://github.com/Metrilo/WooCommerce-Plugin)

This guide consists of 2 parts:

- [Front-end Metrilo integration](#front-end-metrilo-integration)
- [Back-end Metrilo integration](#back-end-metrilo-integration)


## Front-end Metrilo integration ##

_You should add the Metrilo javascript snippet on every page you want to track!_

#### Installing the javascript library ####

Let's start by including the javascript snippet (bellow) right before closing the `head` tag.

```
<script type="text/javascript">
window.metrilo||(window.metrilo=[]),window.metrilo.queue=[],window.metrilo.methods=["identify","track","event","pageview","purchase","debug","atr"],
window.metrilo.skelet=function(e){return function(){a=Array.prototype.slice.call(arguments);a.unshift(e);window.metrilo.queue.push(a)}};
for(var i=0;window.metrilo.methods.length>i;i++){var mthd=window.metrilo.methods[i];window.metrilo[mthd]=window.metrilo.skelet(mthd)}
window.metrilo.load=function(e){var t=document,n=t.getElementsByTagName("script")[0],r=t.createElement("script");
r.type="text/javascript";r.async=true;r.src="//t.metrilo.com/j/"+e+".js";n.parentNode.insertBefore(r,n)};
metrilo.load("<YOUR_PROJECT_TOKEN>");
</script>
```

Consider the `metrilo.load("<YOUR_PROJECT_TOKEN>")` line. Here you should put your project token. You can find it on the Settings page in the Metrilo web-site.

#### Identify user ####

Metrilo tracks anonymous user history and it is nice to notify Metrilo when you have customer details. The common use cases where identification could be done are:
* After order, because each order contains customer details
* After subscription for some resource on your web-site
* After login

The following example shows how identification works:
```javascript
var identifyParams = { name: 'Johny Bravo', first_name: 'Johny', last_name: 'Bravo', email: 'johnybravo@gmail.com' };

metrilo.identify('johnybravo@gmail.com', identifyParams);
```
_First name and last name are not required_

### Tracking events ###

The main events that are supported by the Metrilo library are: order, view product, view category, add to cart, remove from cart, view cart, page view, checkout start.
Optional events you can use are: checkout delivery, checkout payment, checkout confirm.

You can track an event using the call
```javascript
metrilo.event('<event_type>', event_parameters);
```
You can pass multiple events on the same page.

We will describe the parameters allowed and use cases for each event.

##### Order #####

When a user creates an order on your web-site you can send the following event from your javascript code.

```javascript
// Prepare the order parameters
var orderParams = {
  order_id: '12345678',
  order_status: 'pending',
  amount: 234.99,
  shippint_amount: 0,
  tax_amount: 23.50,
  items: [
     {
        id: '312',
        price: 134.50,
        // For optional products you can use instead of price
        // option_id: '4556',
        // option_price: 134.50
        name: 'Cool sunglasses',
        url: 'http:\/\/fanstore-johnybravo.com\/product\/coolsunglasses',
        quantity: 1
     },
     {
        id: '148',
        price: 100.49,
        // For optional products you can use option price intead of price
        // option_id: '4556',
        // option_price: 134.50
        name: 'Black T-shirt',
        url: 'http:\/\/fanstore-johnybravo.com\/product\/blacktshirt',
        quantity: 1
     }
  ],
  coupons: ['EASTER15OFF'],
  shipping_method: 'Free Shipping - Free',
  payment_method: 'Cash On Delivery',
  billing_phone: '+47 888 999 000',
  billing_country: 'US',
  billing_region: 'Alaska',
  billing_postcode: '12345',
  billing_address: 'Sesam Street 1',
  billing_company: 'Johny&Friends'
};
// Identify customer so we know who is doing the order
metrilo.identify('johnybravo@gmail.com', { name: 'Johny Bravo', email: 'johnybravo@gmail.com' });
// Then call Metrilo API using the order parameters.
metrilo.event('order', orderParams);
```
_Consider escaping the url strings_

The coupons, billing, payment and shipping related parameters are not required.

##### View product #####

When the user lands on a product page you can track it as follows:

```javascript
var productParams = {
  id: '312',
  price: 134.50,
  name: 'Cool sunglasses',
  url: 'http:\/\/fanstore-johnybravo.com\/product\/coolsunglasses',
  image_url: 'http:\/\/fanstore-johnybravo.com\/product\/coolsunglasses\/coolsunglasses.jpg',
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
```
_Consider escaping the url strings_

##### View category #####
When a user lands on a category or a multiproduct listing page you can track view category event as follows:

```javascript
  var categoryParams = {
    id: '9912',
    name: 'Accessories'
  };

  metrilo.event('view_category', categoryParams);
```
##### Add to cart #####

When a product is added to cart you can track "add to cart" event:

```javascript
  var productParams = {
    id: '312',
    price: 134.50,
    // For optional products you can use option price intead of price or allongside with it.
    // option_id: '4556',
    // option_price: 134.50
    name: 'Cool sunglasses',
    url: 'http:\/\/fanstore-johnybravo.com\/product\/coolsunglasses',
    image_url: 'http:\/\/fanstore-johnybravo.com\/product\/coolsunglasses\/coolsunglasses.jpg',
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
  };

  metrilo.event('add_to_cart', productParams);
```
_Consider escaping the url strings_

##### Remove from cart #####

You can track "remove from cart" this way:
```javascript
var productParams = { id: '312' };

metrilo.event('remove_from_cart', productParams);
```

##### View cart #####

When a user views his cart you can track view cart event:

```javascript
metrilo.event('view_cart');
```

##### Checkout start #####

When a user goes to the checkout page, you should track "checkout start" event as follows:

```javascript
metrilo.event('checkout_start');
```

##### Page view #####

Static page views this way:
```javascript
metrilo.event('pageview', 'FAQ');
```

3.8. Checkout delivery, Checkout payment, Checkout confirm (_Not required_)

If those events bring value to your business you can track them as follows.

* Checkout delivery
```javascript
metrilo.event('checkout_delivery');
```

* Checkout payment
```javascript
metrilo.event('checkout_payment');
```

* Checkout confirm
```javascript
metrilo.event('checkout_confirm');
```

## Back-end Metrilo integration ##

You should integrate your back-end with Metrilo for two main reasons:

- Importing orders from the database
- Syncing order data. This means doing API call on creating and updating of the order.

Back-end synchronization is important part of the implementation, since it gives opportunity to track changes to the order statuses.

Make a back-end call when creating order is done on the client as well. It's ensuring that if an order sent from client fails for a reason we can have it from your back-end.

1. The order JSON structure is similar to the front-end one. We will present it using PHP named arrays.

So we have an `orderParams` array, containing 2 `items` (products, subscriptions, etc.)

```javascript
var orderParams = {
  order_id: '12345678',
  order_status: 'pending',
  amount: 234.99,
  shippint_amount: 0,
  tax_amount: 23.50,
  items: [
     {
        id: '312',
        price: 134.50,
        // For optional products you can use instead of price
        // option_id: '4556',
        // option_price: 134.50
        name: 'Cool sunglasses',
        url: 'http:\/\/fanstore-johnybravo.com\/product\/coolsunglasses',
        quantity: 1
     },
     {
        id: '148',
        price: 100.49,
        // For optional products you can use option price intead of price
        // option_id: '4556',
        // option_price: 134.50
        name: 'Black T-shirt',
        url: 'http:\/\/fanstore-johnybravo.com\/product\/blacktshirt',
        quantity: 1
     }
  ],
  coupons: ['EASTER15OFF'],
  shipping_method: 'Free Shipping - Free',
  payment_method: 'Cash On Delivery',
  billing_phone: '+47 888 999 000',
  billing_country: 'US',
  billing_region: 'Alaska',
  billing_postcode: '12345',
  billing_address: 'Sesam Street 1',
  billing_company: 'Johny&Friends'
}
```

You can build events JSON using the orders from above.

```javascript
var eventsJson = {
  token: '<YOUR_PROJECT_TOKEN>',
  // !!! Consider events is array. This way you can pass multiple orders when importing !!!
  events: [
    {
       event_type: 'order',
       // !!! Pass identify to know who is doing the order !!!
       identity: {
         email: 'johnybravo@gmail.com',
         name: 'Johny Bravo',
         first_name: 'Johny',
         last_name: 'Bravo'
       },
       // !!! orderParams from the order strucure above !!!
       params: orderParams,
       time: 1463500152 // !!! The order created timestamp (UNIX) !!!
       uid: 'johnybravo@gmail.com' // !!! Again identification for user !!!
       use_ip: '127.0.0.1' // Or the user ip address if you have it
    }
  ]
}
```
**For order imports passing multiple events it is useful to pass chunks of orders rather than one by one. We recommend using 25 orders per chunk. Consider setting correct UNIX time for the call, because we can match order creation from there.**

We are almost ready. Last step is securing the back-end calls a bit.

For securing the back-end calls to Metrilo API there are some encryption stages when building the actual request.

We will choose PHP for the examples bellow, but those are applicable for almost all popular languages.

```php
// eventJson is from the example above and is an named array with nested order arrays etc.
$events = array(
  'token' => '<YOUR_TOKEN_HERE>',
  'events' => array(
    // And events as arrays, as you can imagine :)
  )
);
// Sort events by keys.
ksort($events);
// Encode the array
$eventsJson = json_encode($events);
// Encode using Base64
$basedCall = base64_encode($events);
// Finally build MD5 signature with the $basedCall concatenated with <YOUR_API_SECRET_HERE>
$signature = md5($basedCall."<YOUR_API_SECRET_HERE>");
// And with $signature and $basedCall build the final request
$requestBody = array(
  's' => $signature,
  'hs' => $basedCall
);
```

So the `$requestBody` is ready to be send as POST request body to Metrilo API.

Our endpoint for back-end integration is `http://p.metrilo.com/bt`.

You can choose suitable client for your environment to POST it (CURL, some rest client, etc.).

**We suggest making those calls fire-and-forget so they don't block the normal back-end flows on error or timeout.**

We have PHP implementation of a [lightweight async client](https://github.com/Metrilo/Magento-Plugin/blob/master/app/code/community/Metrilo/Analytics/Helper/AsyncHttpClient.php) we use in our Magento plugin based on [fsockopen](http://php.net/manual/en/function.fsockopen.php)

It is framework independent (instead of the `extend Mage_Core_Helper_Abstract` which can be removed without breaking the code).

You can check it here - [AsyncHttpClient](https://github.com/Metrilo/Magento-Plugin/blob/master/app/code/community/Metrilo/Analytics/Helper/AsyncHttpClient.php)

---
_For questions, ideas and critics you can open an issue here or write us to support@metrilo.com_

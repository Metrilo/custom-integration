## Tracking API ##

### 1. Authentication ###
'X-Metracker-Digest' header must be provided within each request, built as shown below.

```ruby
hmac_digest = OpenSSL::HMAC.digest(OpenSSL::Digest.new('sha256'), api_secret, params.to_json)

x_metracker_digest = Base64.encode64(hmac_digest)
```

### 2. API calls ###
```ruby
# Metrilo project token
projectToken = 'a3bg1d006693314h'

# unique session identifier, that associates the call to a specific visitor session.
uid = 'c509c095715448cde218bf70186158b828befcbf21'

# type of the call, if sent from the frontend or from the backend
callType = 'frontend', 'backend'
```

- Identify call

```ruby
identifyParams = {}

# or with some of the params
identifyParams = {
  email:      'mr.bean@mail.com',
  first_name: 'Rowan',
  last_name:  'Atkinson',
  name:       'Rowan Atkinson',
  country:    'UK',
  company:    'Fun Ltd',
  subscribed: false,
  tags:       ['Spender', 'Funny']
}
```

- Pageview event

```ruby
eventType = 'pageview'
eventParams = {
  event: 'Homepage',
  uri: 'https://metrilo.com'
}
identifyParams = {}
```

- Search event

```ruby
eventType = 'search'
eventParams = {
  query: 'Black Box',
  uri:   'https://teststore.com/black-box'
}
identifyParams = {}
```

- Subscribed event

```ruby
eventType = 'subscribed'
eventParams = {}
identifyParams = { email: 'mr.bean@mail.com' } # if we have it
```

- View Article event

```ruby
eventType = 'view_article'
eventParams = {
  id:   3377,
  name: 'Metrilo Blog',
  url:  'https://metrilo/com/blog',
  uri:  'https://metrilo/com/blog'
}
identifyParams = {}
```

- View Category event

```ruby
eventType = 'view_category',
eventParams = {
  id:   8844,
  name: 'Plans',
  uri:  'https://metrilo.com/plans',
},
identifyParams = {},
```

- View Product event

```ruby
eventType = 'view_product'
eventParams = {
  id:         66445511,
  name:       'Automator',
  image_url:  '',
  uri:        '',
  url:        '',
  price:      89.90,
  categories: [{ id: 1221, name: 'Features' }]
}
identifyParams = {},
```

- View Cart event

```ruby
eventType = 'view_cart'
eventParams = {}
identifyParams = {}
```

- Add To Cart event

```ruby
eventType = 'add_to_cart'
eventParams = {
  id:        1778821,
  name:      'Sun Glasses',
  url:       '',
  uri:       '',
  image_url: '',
  price:     45.90,
  quantity:  1
}
identifyParams = {}
```

- Remove From Cart event

```ruby
eventType = 'remove_from_cart'
eventParams = {
  id:        1778821,
  name:      'Sun Glasses',
  url:       '',
  uri:       '',
  image_url: '',
  price:     45.90,
  quantity:  1
}
identifyParams = {}
```

- Checkout event

```ruby
eventType = 'checkout_start' # 'checkout_payment', 'checkout_delivery', 'checkout_confirm'
eventParams = {}
identifyParams = {}
```

- Order event

By sending the same order with different order status, the status gets updated.

```ruby
eventType = 'order'
eventParams = {
  order_id:               221199,
  order_status:           'paid',
  amount:                 110.90,
  shipping_amount:        4.96,
  tax_amount:             1.23,
  shipping_method:        'Standart',
  payment_method:         'PayPal',
  billing_phone:          '4158167332',
  billing_city:           'London',
  billing_region:         'Center',
  billing_postcode:       '112233',
  billing_country:        'UK',
  billing_address_line_1: 'Main str',
  billing_address_line_2: '13',
  billing_company:        'Fun Ltd',
  items: [
    {
      id:        '3377',
      name:      'Tea',
      url:       '',
      image_url: '',
      price:     63.90,
      quantity:  2
    },
    {
      id:        '5524',
      name:      'Body Oil',
      url:       '',
      image_url: '',
      price:     78.80,
      quantity:  1
    }
  ],
  categories: [{ id: '7667', name: 'Body Fit' }],
  coupons: ['10%OFF', 'FREE2DAYSHIPPING']
}
```

### 3. Endpoints ###
#### Identify endpoint, for sending single identify calls. ####
When we have an email that uniquely identifies the visitor.

```ruby
POST /tracking/identify

params = {
  # required
  token:           projectToken,                           
  identify_params: identifyParams
  uid:             uid,
  call_type:       'frontend', # 'backend'
  time:            1494920940,
  # optional
  use_ip: '89.110.223.56',
  meta: { referrer: '', href: '', qs: '' },
  smeta: { referrer: '', href: '', qs: '' }
}
```

#### Event endpoint, for sending single event calls. ####
```ruby
POST /tracking/event

params = {
  # required
  token:        projectToken,
  event_type:   eventType,
  event_params: eventParams,
  uid:          uid,
  call_type:    'backend', # 'frontend'
  time:         1494920940,
}
```

#### Batch endpoint, for sending batch of mixed identify/event calls. ####
```ruby
POST /tracking/batch

params = {
  batch: [{
    token:           projectToken,
    identify_params: identifyParams,
    uid:             uid,
    call_type:       'frontend',
    time:            1494920940
  },
  {
    token:           projectToken,
    event_type:      eventType,
    event_params:    eventParams,
    identify_params: {},
    uid:             uid,
    call_type:       'backend',
    time:            1494920940
  }]
}
```

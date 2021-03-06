# Storefront JS API

The JavaScript API we described is available for all Ecwid users. The API is intended for better integrating Ecwid with the surrounding website. The API is based on two concepts: objects and events. Objects are simple containers for methods, while events are containers for the user-supplied callbacks (or extensions).

> Check out [Customize Storefront](#customize-storefront) section for general details on changing Ecwid's storefront.
> 
> Access Page object to find the current page type

```javascript
Ecwid.OnPageLoad.add(function(page) {
        alert("My page load handler: " + page.type);
});
```
> 
> See also: [App working in storefront source code example](https://github.com/Ecwid/custom-thank-you-page-app)

Extensions are added to the events using the `add()` method, see example code on the right.

This sample adds a function that is called every time a new page is loading in the product browser. Callbacks may be objects with multiple functions instead of just one function as shown in the example above, which may return useful values. The form of the extensions, their parameters, and the need of a return value depends on the event that occurs.

Note that the `add()` method is located in the script.js file of the standard Ecwid integration code. It is important to include script.js before you use the API methods. Moreover, because of the staged loading of Ecwid, only few API methods are available during the page load. Most of the Ecwid JavaScript API is available after Ecwid is loaded completely. All JavaScript API methods will be available after you hook into the `Ecwid.OnAPILoaded` event.

<aside class="notice">
Ecwid Storefront JavaScript API is available on any Ecwid plan.
</aside>


## Get Storefront Details

Get basic storefront information to use in your script.

### Ecwid.getStaticBaseUrl

> Get Static Base Url code example

```javascript
var StaticBaseUrl = Ecwid.getStaticBaseUrl()

console.log(StaticBaseUrl)

// prints
// "https://d3fi9i0jj23cau.cloudfront.net/gz/"
```

Returns the base URL for static Ecwid files, like images and CSS, with the ’/’ at the end.

### Ecwid.getOwnerId

> Get store ID code example

```javascript
var storeId = Ecwid.getOwnerId()

console.log(storeId);

// prints
// 1003
```

Returns the store ID.

### Ecwid.getInitializedWidgets

> Get all widgets to be displayed when page loads

```js
var widgets = Ecwid.getInitializedWidgets();

console.log(widgets);

// prints 
// ["Minicart", "SearchPanel", "ProductBrowser"]
```

Returns array containing widget types present on a page. There are four types available: 

* Minicart - Minicart widget
* SearchPanel - Search widget
* ProductBrowser - Storefront widget
* Categories - Categories widget
* Product - Embedded product

### Ecwid.formatCurrency

> Format a number using currency format of a store

```javascript
var currencyFormat = Ecwid.formatCurrency(12.99)

console.log(currencyFormat)

// prints
// "$12.99"
```

Converts the given currency value to a human-readable string according to the store settings. It accepts both string and integer as arguments.

### Ecwid.getStorefrontLang

```javascript
var lang = Ecwid.getStorefrontLang();

console.log(lang);

//prints
// "en"
```

Get current language of storefront. The function is available as soon as Ecwid store starts to load.

### Ecwid.getAppPublicConfig

> Get app public config function usage

```js
Ecwid.getAppPublicConfig(appId);
```

> Example values from app publc config

```json
  {
    "key": "public",
    "value": "12345"
  }
```

> Get app public config code example

```js
  var pageId = Ecwid.getAppPublicConfig("my-cool-app");

  console.log(pageId);
  // prints 
  // '12345'
```

Returns value for `public` key from Ecwid Application Storage endpoint.

`Ecwid.getAppPublicConfig()` receives one argument: 

Name | Type | Description
---- | ---- | -----------
**appId** | String | Namespace of your application (as set in the application settings).

<aside class="notice">
Data in public storage of your app must not exceed <strong>64Kb</strong>
</aside>

### Ecwid.getAppPublicToken 

> Get app public config function usage

```js
Ecwid.getAppPublicToken(appId);
```

> Get app public token code example

```js
var publicToken = Ecwid.getAppPublicToken('my-cool-app');

console.log(publicToken);
// prints
// public_qKDUqKkNXzcj9DejkMUqEkYLq2E6BXM9

```

Returns public applicaiton token for an Ecwid store. In order for this function to work, your app has to ask for `public_storefront` scope from a store. [Learn more](#access-tokens)

`Ecwid.getAppPublicToken()` receives one argument: 

Name | Type | Description
---- | ---- | -----------
**appId** | String | Namespace of your application (as set in the application settings).

### Page Object

Describes the page displaying inside the product browser.

> Get page type after new page is loaded 

```js
Ecwid.OnPageLoaded.add(function(page){
  console.log("Current page is of type: " + page.type);
})

// prints
// Current page is of type: CATEGORY
```

**Fields:**

Name | Type | Description
---- | ----- | -----------
type | string, one of the following: ‘ACCOUNT_SETTINGS’, ‘ADDRESS_BOOK’, ‘ORDERS’, ‘CATEGORY’, ‘CART’, ‘CHECKOUT_ADDRESS_BOOK’, ‘CHECKOUT_PAYMENT_DETAILS’, ‘CHECKOUT_PLACE_ORDER’, ‘CHECKOUT_SHIPPING_ADDRESS’, ‘ORDER_CONFIRMATION’, ‘ORDER_FAILURE’, ‘CHECKOUT_RESULT’, ‘DOWNLOAD_ERROR’, ‘PRODUCT’, ‘SEARCH’, 'FAVORITES', 'RESET_PASSWORD' | The type of the page. Some pages may have parameters like for example product id of the viewing product. Those parameters are described below.
keywords | string, optional | for type==’ORDERS’: the keywords that are used to find orders in the customer account page. for type==’SEARCH’: the keywords that are used to find products on the product search page.
from | integer timestamp, optional | for type==’ORDERS’: The timestamp of the start of the orders date range.
to | integer timestamp, optional | for type==’ORDERS’: The timestamp of the end of the orders date range.
offset | | for type==’ORDERS’: the position of the current order list page (starting from 0). for type==’CATEGORY’ and SEARCH’: the position of the current product list page (starting from 0).
categoryId | integer | for type==’CATEGORY’: the id of the showing product category or 0 if this is the starting page of the catalog and no categories are selected yet. for type==’PRODUCT’: the category internal id the current product has been navigated from. Zero (0) is the root category, −1 meaning that the category is unknown (e.g. a product opened from a search result).
mainCategoryId | integer | for type==’PRODUCT’ in the OnPageLoaded event: the internal id of category that is considered the default category of this product (in case if the product is assigned to a few different categories). If a product is assigned to a single category, mainCategoryId will be equeal to categoryId; if a product is not assigned to any category, its mainCategoryId is 0 (zero). for type==’PRODUCT’ in the OnPageLoad event: always 0 (zero);
sort | string, one of: ‘normal’, ‘addedTimeDesc’, ‘priceAsc’, ‘priceDesc’, ‘nameAsc’, ‘nameDesc’ | for type==’CATEGORY’ and ’SEARCH’: the order of the product list, as selected by the user in the ‘sort by’ drop-down. ‘Desc’ suffix stands for the descending order, ‘Asc’ suffix stands for the ascending order.
orderId | integer | for type==’CHECKOUT_RESULT’ and type==’ORDER_CONFIRMATION’: the internal id of the order (not to be confused with the store order number)
ticket | integer | for type==’CHECKOUT_RESULT’: the security random code that allows to retrieve information about the order
errorType | one of the following: ‘expired’, ‘invalid’, ‘limit’ | for type==’DOWNLOAD_ERROR’: the type of the error while downloading an e-good file.
key | integer, optional | for type==’DOWNLOAD_ERROR’: the downloading file internal id
productId | integer | for type==’PRODUCT’: the internal id of the displaying product (not to be confused with SKU).
orderNumber | integer | for type==’ORDER_CONFIRMATION’ the number of the order placed by customer(without prefix and suffix).
vendorOrderNumber | string | for type==’CHECKOUT_RESULT’ and type==’ORDER_CONFIRMATION’ the number of the order placed by customer(with prefix and suffix).

## Subscribe To Events

Find various useful events and execute your functions with them.

### Ecwid.OnAPILoaded

> OnAPILoaded usage example

```javascript
Ecwid.OnAPILoaded.add(function() {
    console.log("API is loaded")
});
```

This event contains callback functions that are called exactly when the Ecwid Javascript API loads and becomes available under the `window.Ecwid` top-level object. Functions attached to this event do not accept any parameters and do not require to return any value.

### Ecwid.OnPageLoad/Ecwid.OnPageLoaded

> If user visits cart page, do domething

```javascript
Ecwid.OnPageLoaded.add(function(page) {
    if (page.type == "CART") {
      // do something ...
  }
});
```

These events contain callbacks that get called on each page change inside the product browser. The difference between **OnPageLoad** and **OnPageLoaded** is that the former is called when the page is changed (e.g. a link is clicked), while the latter is called later when the corresponding page is loaded inside the product browser.

The callback functions accept one parameter of type **Page** specifying which page is to be loaded (or has already been loaded). See **Page Object** details for more info.

#### Page Object

Describes the page displaying inside the product browser.

> Get page type after new page is loaded 

```js
Ecwid.OnPageLoaded.add(function(page){
  console.log("Current page is of type: " + page.type);
})

// prints
// Current page is of type: CATEGORY
```

**Fields:**

Name | Type | Description
---- | ----- | -----------
type | string, one of the following: ‘ACCOUNT_SETTINGS’, ‘ADDRESS_BOOK’, ‘ORDERS’, ‘CATEGORY’, ‘CART’, ‘CHECKOUT_ADDRESS_BOOK’, ‘CHECKOUT_PAYMENT_DETAILS’, ‘CHECKOUT_PLACE_ORDER’, ‘CHECKOUT_SHIPPING_ADDRESS’, ‘ORDER_CONFIRMATION’, ‘ORDER_FAILURE’, ‘CHECKOUT_RESULT’, ‘DOWNLOAD_ERROR’, ‘PRODUCT’, ‘SEARCH’, 'FAVORITES' | The type of the page. Some pages may have parameters like for example product id of the viewing product. Those parameters are described below.
keywords | string, optional | for type==’ORDERS’: the keywords that are used to find orders in the customer account page. for type==’SEARCH’: the keywords that are used to find products on the product search page.
from | integer timestamp, optional | for type==’ORDERS’: The timestamp of the start of the orders date range.
to | integer timestamp, optional | for type==’ORDERS’: The timestamp of the end of the orders date range.
offset | | for type==’ORDERS’: the position of the current order list page (starting from 0). for type==’CATEGORY’ and SEARCH’: the position of the current product list page (starting from 0).
categoryId | integer | for type==’CATEGORY’: the id of the showing product category or 0 if this is the starting page of the catalog and no categories are selected yet. for type==’PRODUCT’: the category internal id the current product has been navigated from. Zero (0) is the root category, −1 meaning that the category is unknown (e.g. a product opened from a search result).
mainCategoryId | integer | for type==’PRODUCT’ in the OnPageLoaded event: the internal id of category that is considered the default category of this product (in case if the product is assigned to a few different categories). If a product is assigned to a single category, mainCategoryId will be equeal to categoryId; if a product is not assigned to any category, its mainCategoryId is 0 (zero). for type==’PRODUCT’ in the OnPageLoad event: always 0 (zero);
sort | string, one of: ‘normal’, ‘addedTimeDesc’, ‘priceAsc’, ‘priceDesc’, ‘nameAsc’, ‘nameDesc’ | for type==’CATEGORY’ and ’SEARCH’: the order of the product list, as selected by the user in the ‘sort by’ drop-down. ‘Desc’ suffix stands for the descending order, ‘Asc’ suffix stands for the ascending order.
orderId | integer | for type==’CHECKOUT_RESULT’ and type==’ORDER_CONFIRMATION’: the internal id of the order (not to be confused with the store order number)
ticket | integer | for type==’CHECKOUT_RESULT’: the security random code that allows to retrieve information about the order
errorType | one of the following: ‘expired’, ‘invalid’, ‘limit’ | for type==’DOWNLOAD_ERROR’: the type of the error while downloading an e-good file.
key | integer, optional | for type==’DOWNLOAD_ERROR’: the downloading file internal id
productId | integer | for type==’PRODUCT’: the internal id of the displaying product (not to be confused with SKU).
orderNumber | integer | for type==’ORDER_CONFIRMATION’ the number of the order placed by customer(without prefix and suffix).
vendorOrderNumber | string | for type==’CHECKOUT_RESULT’ and type==’ORDER_CONFIRMATION’ the number of the order placed by customer(with prefix and suffix).

### Ecwid.OnSetProfile

> Ecwid.OnSetProfile code example

```html
<script src="//app.ecwid.com/script.js?1003" type="text/javascript" charset="UTF-8"></script>
<script>
function dump(arr,level) {
  var dumped_text = "";
    if (!level) level = 0;
    // The padding given at the beginning of the line.
      var level_padding = "";
      for (var j=0;j<level+1;j++) level_padding += " ";
        if (typeof(arr) == 'object') { // Array/Hashes/Objects
          for (var item in arr) {
          var value = arr[item];
            if (typeof(value) == 'object') { // If it is an array,
            dumped_text += level_padding + "'" + item + "' ...\n";
            dumped_text += dump(value,level+1);
            } else {
            dumped_text += level_padding + "'" + item + "' => \"" + value + "\"\n";
            } 
          }
        } else { // Stings/Chars/Numbers etc.
        dumped_text = "===>"+arr+"<===("+typeof(arr)+")";
      }
      return dumped_text;
  }

Ecwid.OnSetProfile.add(function(customer) {
  if (customer == null) document.getElementById('CUSTOMER').innerHTML = 'not logged in';
    else document.getElementById('CUSTOMER').innerHTML = dump(customer);
});
</script>
<div><pre id='CUSTOMER'/></div>
<script>
xProductBrowser();
</script>
```

This event contains callback functions that receive the changes in the current customer profile. 

If no customer is logged in, these functions receive **null**. Whenever a customer logs in/out, the callback functions are called with either the corresponding parameter of type **Customer** or **null**. Example code can be seen on the right.

### Ecwid.OnCartChanged

> Specify a callback function when cart is changed in storefront

```javascript
Ecwid.OnCartChanged.add(function(cart){
    // your code here
}) 
```

This event contains callback functions that get called each time when a shopping cart is changed — either by the customer or due to system events.

The callback function receives `cart` object as an argument, that has the new shopping cart state after the change is applied.

The callbacks added to `Ecwid.OnCartChanged` will be called when the shopping cart is initialized and on every occasion when either of the properties of the passed `cart` object is changed. These occasions include:

- Cart initialization 
- Adding a product to cart
- Removing a product from cart
- Changing the product’s options
- Clearing the cart
- Applying a discount coupon
- Selecting and changing the selection of the shipping method
- Changing shipping address
- Syncing the cart contents, if there are a few browser tabs with the store are opened
- Clearing the cart upon user’s logout — in this occasion the callback receives `null` as an argument.

The passed `cart` object represents only the basic properties of the shopping cart. It contains the data coming from the customer’s actions (like products, coupons, shipping methods) and **might not contain the calculated aggregates** (like order totals, shipping costs, the discounted amounts or taxes). For the calculated aggregates, it is rather recommended to use the `Ecwid.Cart.calculateTotal()` method.

### Ecwid.OnProductOptionsChanged

> Show an alert if product options were changed

```javascript
Ecwid.OnProductOptionsChanged.add(function(productid) {
   window.alert("Options changed, product id:" + productid);    
})
```

This event executes callback function each time when product option was changed. The callback functions accept one parameter: **productid**, specifying ID of the product changed. 

`OnProductOptionsChanged` works for following options type: dropdown list, radio button and checkbox. Input, textarea and upload files types are not supported yet.

### Ecwid.OnOrderPlaced

> Get order number of a placed order

```js
Ecwid.OnOrderPlaced.add(function(order){
  console.log(order.orderNumber);
})

// 781
```

`Ecwid.OnOrderPlaced()` event allows you to get details of an order placed by a customer in storefront right after an order is placed. This event provides the `order` object in callback function so that you can get order details right after the event occurs.

#### Order object

Describes the details of a placed order by customer. 

**Fields**

Name | Type | Description
---- | ----- | -----------
items | Array\<*OrderItems*\> | Items purchased in order
productsQuantity | integer | Total quantity of purchased items in order
couponName | string | Applied coupon name. `undefined` if no coupon was used
weight | number | Total order weight
paymentMethod | string | Selected payment method name. `undefined` if no method selected
shippingCarrierName | string | Selected shipping carrier name. `undefined` if no method selected
shippingMethod | string | Selected shipping method name. `undefined` if no method selected
total | number | Order total
subtotal | number | Order subtotal
tax | number | Order total tax
couponDiscount | number | Coupon discount applied to order
volumeDiscount | number | Sum of discounts based on subtotal
customerGroupDiscount | number | Sum of discounts based on customer group
discount | number | Total discount amount for order
shipping | number | Total shipping cost for order
handlingFee | number | Handling fee applied to order
shippingAndHandling | number | Sum of `shipping` and `handlingFee` fields
billingPerson | \<*PersonInfo*\> | Customer billing information
shippingPerson | \<*PersonInfo*\> | Customer shipping information
affiliateId | string | Affiliate ID value for order
orderNumber | number | Order number in a store
vendorNumber | string | Order number with custom prefixes and suffixes of that store
date | string (UNIX Timestamp) | Date when order was placed as a UNIX Timestamp, e.g. `"1484638550"`
paymentStatus | string | Payment status of an order. One of: `AWAITING_PAYMENT`, `PAID`, `CANCELLED`, `REFUNDED`, `PARTIALLY_REFUNDED`, `INCOMPLETE`
fulfillmentStatus | string | Fulfillment status of an order. One of: `AWAITING_PROCESSING`, `PROCESSING`, `SHIPPED`, `DELIVERED`, `WILL_NOT_DELIVER`, `RETURNED`, `READY_FOR_PICKUP`
customer | \<*CustomerInfo*\> | Basic customer info

##### OrderItems

Name | Type | Description
---- | ----- | -----------
quantity | integer | Quantity of an item in order
product | \<*ProductInfo*\> | Product details
options | Map\<*OptionsInfo*\> | Selected product options

#### ProductInfo

Name | Type | Description
---- | ---- | -----------
id | Integer | Internal unique product ID
name | String | Product name
price | Integer | Product price
shortDescription | String | Product description truncated to 120 characters
sku | String | Product SKU
url | String | URL to this product details page in store front (store front URL is generated from a field in Ecwid Control panel > Settings > General > Store profile)
weight | Integer | Weight of a product

#### OptionsInfo

Name | Type | Description
---- | ---- | -----------
selectedOption | string | Selected option name as a key, its value as a value of that key. For `dropdown`, `radio`, `textarea`, `textfield` options value is a string; for `checkbox` option the value is a comma-separated list of selected values in a single string; for `date` option the value is a selected date according to the store format; for `files` option the value is a string in a format: `"4 files"`

#### PersonInfo

Name | Type | Description
-----|------|------------
name | string | Customer's name
companyName | string | Customer's company name
street | string | Customer's street address
city | string | Customer's city
countryName | string | Customer's country name. `countryCode` can be used instead
countryCode | string | Customer's country code. `countryName` can be used instead
postalCode | string | Customer's zip code
stateOrProvinceCode | string | Customer's state or province code
stateOrProvinceName | string | Customer's state or province name
phone | string | Customer's phone number

#### CustomerInfo

Name | Type | Description
-----|------|------------
name | string | Customer's name
email | string | Customer's email

## Get Customer Details

Find out more about customer that is currently logged in a store.

### Customer Object

Customer object describes details of a customer in the store

> Get customer email and billing country example

```js
Ecwid.OnSetProfile.add(function(customer) {
  console.log(customer.email);
  console.log(customer.billingPerson.countryName);
})

// prints
// supercoder@matrix.com
// United States
```

**Fields:**

Name | Type | Description
---- | ---- | -----------
billingPerson | Object (Person) | Customer’s name along with his/her billing address, as entered in the last order.
email | String | Email address of a customer
id | Number | Unique customer ID in Ecwid
ownerId | number | Store ID this customer belongs to
registered | UNIX Timestamp | Registration date of this customer
shippingAddresses | Array of \<*ShippingAddress*\> | A list of addresses in the customer’s address book

### ShippingAddress Object

The customer’s address as stored in the address book.

**Fields:**

Name | Type | Description
---- | ---- | -----------
id | integer | The unique address id Ecwid database
person | Object (Person) | The object describing the address along with the person’s name and phone number.

### Person Object

Describes the person name, company and address.

**Fields:**

Name | Type | Description
---- | ----- | -----------
name | string | The first and the last name of the person, separated by a space.
companyName | string, optional | The person’s company name, if applicable
street | string, optional | The street address of the person, if applicable. If there are two address lines, they are separated by a newline character ‘\n’
city | string, optional | The person’s city, if applicable
countryCode | string, optional | The person’s country code, as listed in ISO 3166-2
postalCode | string, optional | The person’s postal code or ZIP code, if applicable
stateOrProvinceCode | string, optional | The person’s region/state/province code by ISO 3166-2. Please note that not all countries regional codes are listed in the Ecwid database so far.
countryName | string, optional | Country name, if applicable
phone | string, optional | Phone number, if applicable

## Manage Customer Cart

Manage cart on customer's behalf.

### Ecwid.Cart.addProduct

This function allows to add a product to shopping cart, modifying the cart on behalf of customer.

There are 2 possible ways to call this function: adding products by product ID or adding products with extended options.

#### Adding by product ID

> Add product function

```javascript
Ecwid.Cart.addProduct(productID, callback)
```

`addProduct()` can accept 2 arguments: **productId** and **callback**.

Name | Type | Description
---- | ---- | -----------|
**productID** | Integer | the Ecwid’s internal product ID to be added to cart (can be retrieved from product export, seen in the URL of the product page or via [REST API](#search-products))
callback | Function | the callback function to be called once the operation is complete (either succeeded or failed). See below for details.

> Add product to cart using ID

```javascript
var productId = 10;
Ecwid.Cart.addProduct(productId); 
```
The most simple call to `Ecwid.Cart.addProduct` only requires to pass the numeric product ID. See example code on the right. This code adds 1 item of the given product ID to cart.

If this product contains combinations and the base product is out of stock, the first combination that is in stock will be added to cart instead. If the product is out of stock (and there are no combinations in stock for this product), nothing is added to cart.

#### Adding with extended options

> Add product to cart with extended options
 
```javascript
var product = {
  id: 10,
  quantity: 3,
  options: {
    someTextOption: "Your name",
    someDateOption: new Date().getTime().toString(),
    someRadioOption: "Use default color",
    someDropDownOption: "I want custom engraving",
    someCheckboxOption: ["It's a gift", "Gold engraving"]
  },
  callback: function(success, product, cart) {
    // ...  
  }
}

Ecwid.Cart.addProduct(product);
```

If it is necessary to specify options or quantity of product to be added to cart, the product parameter needs to be passed as an object.

Since this method allows to specify the exact options to be added to cart, only the base product or combination matching those options will be added to cart. So, if the base product or matching combination is out of stock, the addProduct call will not add anything to cart, even if there are other combinations in stock.

**Callback**

Adding to cart is done asynchronously, so if it is important to know the result of this function. The callback function can be passed as the second agrument of the function.

> Add product to cart with callback function, example 1

```javascript
Ecwid.Cart.addProduct(productID, function(success, product, cart){
  console.log(success); // true or false
  console.log(product.name);
})
```

> Add product to cart with callback function, example 2

```javascript
Ecwid.Cart.addProduct({
  id: 10, 
  quantity: 3, 
  callback: function(success, product, cart){
    console.log(success); // true or false
    console.log(product.name);
  }
})
```

Callback receives 3 arguments: **success**, **product**, **cart**

Name | Type | Description
---- | ---- | -----------
success | Boolean | indicates the overall status of addition (succeeded or failed)
product | \<*Product*\> | conatins the object representation of the product added to cart, or null if adding to cart failed (wrong product ID or product is out of stock).
cart | \<*Cart*\> | contains the object representation of the shopping cart after addition (same as in `Ecwid.OnCartChanged` event)

#### Product

Name | Type | Description
---- | ---- | -----------
id | integer | Product ID
quantity | integer | Amount of products to add to cart
options | \<*OptionsInfo*\> | Selected product options for product
callback | function | Function to run after the function is performed

#### OptionsInfo

Name | Type | Description
---- | ---- | -----------
optionName | string or array of string | Replace `optionName` with the real exact product option name you want to specify. Available values depend on the product option type: for checkbox options the value is `array of strings`, for all other options the value is a `string`

### Ecwid.Cart.removeProduct 

> Remove specific product from cart

```js
Ecwid.Cart.removeProduct(index);
```

> Example

```js
// returns cart details with items' details. Use 'items' array to get the index

Ecwid.Cart.get(function(cart){
  console.log(cart);
})

// remove first product from customer's cart

Ecwid.Cart.removeProduct(0);
```

Removes a product with specified index from customer's cart. `Ecwid.Cart.removeProduct()` receives one argument: **index**

Name | Type | Description
---- | ---- | -----------
index | integer | index of a product in cart object you need to remove from customer's cart

### Ecwid.Cart.clear

> Clear cart contents

```js
Ecwid.Cart.clear()
```

Clears the cart contents.

### Ecwid.Cart.get

> Get total number of products in cart code example

```javascript
Ecwid.Cart.get(function(cart) {
  alert(cart.productsQuantity + " products in cart now");
});
```

Retrieves the cart contents asynchronously and passes it as an argument of type Cart to the callback.

### Ecwid.Cart.calculateTotal

> Calculate cart total example

```javascript
Ecwid.Cart.calculateTotal(function(order) {
  if (!order)
    alert('An error occurred while calculating the order!')
  else    
    alert('Order total: ' + order.total);    
}); 
```

Calculates the cart asynchronously and passes the result as an argument of type **Order** to the callback. 

Cart calculation involves a request to server, so this method should be called only occasionally. Calling it frequently, e.g. from loops or by timer, is not acceptable.

Since the calculation needs a server connection, it might fail due to network conditions. In this case, null is passed into the callback instead of **Order** object.

### Ecwid.Cart.gotoCheckout

> Send customer to checkout

```js
Ecwid.Cart.gotoCheckout()
```

`Ecwid.Cart.gotoCheckout()` function allows you to send customer to the first step of the checkout process in a store. Customer will be sent there only if agreeing to terms and conditions is not required in the store. You can find this setting in Ecwid Control Panel > Settings > General > Legal Pages > "Show “I agree with Terms & Conditions” checkbox during checkout" or check it using `Ecwid.Cart.canGotoCheckout()` function.

> Callback function after sending to checkout

```js
Ecwid.Cart.gotoCheckout(function(){
  alert("Checkout process started");
})
```

You can also execute a callback function if a customer was successfully sent to the first step of the checkout process in a store. See example code on the right.

### Ecwid.Cart.canGotoCheckout

> Check if possible to send customer to checkout

```js
Ecwid.Cart.canGotoCheckout(function(callback){
  console.log(callback);
})
```

Using `Ecwid.Cart.canGotoCheckout()` function you can check whether you can send customers to the first step of checkout process in a store. 

**Fields:**

Name | Type | Description
---- | ---- | -----------
callback | boolean | `true` if you can send customer to the checkout process, `false` otherwise

### Ecwid.Cart.setCustomerEmail

> Function description

```js
Ecwid.Cart.setCustomerEmail(email, successCallback, errorCallback)
```

> Set customer email example

```js
Ecwid.Cart.setCustomerEmail('james@coolstore.com')
```

`Ecwid.Cart.setCustomerEmail()` allows you to set customer's email in checkout process. Make sure to use it on pages, where the email is not shown so that it is always up-to-date.

If a customer is logged in, your set email will overwrite the email for that customer's account (if possible).

**Fields:**

Name | Type | Description
-----|------|-------------
**email** | string | Customer email address to be set
successCallback | function | Success callback function
errorCallback | function | Error callback function

<aside class='notice'>
Parameters in bold are mandatory
</aside>

`errorCallback` structure is: errorCallback(errCode, errMsg)

Name | Type | Description
-----|------|-------------
errCode | number | Error code
errMsg | string | Error message

**Errors**

Error code | Error message
-----------|--------------
0 | Missing argument
100 | Incorrect data passed
1000 | Store owner disabled this functionality

### Ecwid.Cart.setOrderComments

> Function

```js
Ecwid.Cart.setAddress(orderComments, successCallback, errorCallback)
```

> Set order comments example

```js
Ecwid.Cart.setOrderComments('Leave order at the door.');
```

> Full function call example

```js
Ecwid.Cart.setOrderComments('Leave order at the door.',
  function(){
    console.log('Successfully set order comments.')
  },
  function(){
    console.log('Error setting order comments.');
})
```

`Ecwid.Cart.setOrderComments` allows you to set the order comments field on the fly. It's quite useful to pass some additional information for an order, which will be provided in email notifications to customer and store admin as well as the order details in Ecwid Control Panel and Ecwid API.

**Fields:**

Name | Type | Description
-----|------|-------------
**orderComments** | string | Order comments string to be set for an order
successCallback | function | Success callback function
errorCallback | function | Error callback function

<aside class='notice'>
Parameters in bold are mandatory
</aside>

`errorCallback` structure is: errorCallback(errCode, errMsg)

Name | Type | Description
-----|------|-------------
errCode | number | Error code
errMsg | string | Error message

**Errors**

Error code | Error message
-----------|--------------
0 | Missing argument
100 | Incorrect data passed
1000 | Store owner disabled this functionality

### Ecwid.Cart.setAddress

> Function

```js
Ecwid.Cart.setAddress(address, successCallback, errorCallback)
```

> Set shipping address example

```js
Ecwid.Cart.setAddress({
  "name": "John Carmichael",
  "companyName": "Cool Slippers",
  "street": "5th Ave",
  "city": "New York",
  "countryName": "United States",
  "postalCode": "10002",
  "stateOrProvinceCode": "NY",
  "phone": "+1 234 523 11 42"
  }
);
```

> Full function call example

```js
Ecwid.Cart.setAddress({
  "name": "John Carmichael",
  "companyName": "Cool Slippers",
  "street": "5th Ave",
  "city": "New York",
  "countryName": "United States",
  "postalCode": "10002",
  "stateOrProvinceCode": "NY",
  "phone": "+1 234 523 11 42"
  },
  function(){
    console.log('Address successfully set')
  },
  function(){
    console.log('Error setting the address');
  }
)
```

`Ecwid.Cart.setAddress()` allows you to set shipping address for customer in storefront. You can use it to prefill and hide fields in checkout process to simplify it. 

If you specify some fields only (name, for example), then Ecwid will reset all other fields and they will become empty. So if you need to update only some fields, make sure to send them in your function call as well as the updated values.

When function is called, Ecwid will set the 'My shipping address is the same as the billing address' flag to `true` automatically.

**Fields:**

Name | Type | Description
-----|------|-------------
**address** | \<*Person*\> | Customer's shipping address details
successCallback | function | Success callback function
errorCallback | function | Error callback function

**Person** fields:

Name | Type | Description
-----|------|------------
name | string | Customer's name
companyName | string | Customer's company name
street | string | Customer's street address
city | string | Customer's city
countryName | string | Customer's country name. `countryCode` can be used instead
countryCode | string | Customer's country code. `countryName` can be used instead
postalCode | string | Customer's zip code
stateOrProvinceCode | string | Customer's state or provice code
phone | string | Customer's phone number


<aside class='notice'>
Parameters in bold are mandatory
</aside>

`errorCallback` structure is: errorCallback(errCode, errMsg)

Name | Type | Description
-----|------|-------------
errCode | number | Error code
errMsg | string | Error message

**Errors**

Error code | Error message
-----------|--------------
0 | Missing argument
100 | Incorrect data passed
1000 | Store owner disabled this functionality

### Ecwid.Cart.setBillingAddress

> Function

```js
Ecwid.Cart.setBillingAddress(address, successCallback, errorCallback)
```

> Set billing address example

```js
Ecwid.Cart.setBillingAddress({
  "name": "John Carmichael",
  "companyName": "Cool Slippers",
  "street": "5th Ave",
  "city": "New York",
  "countryName": "United States",
  "postalCode": "10002",
  "stateOrProvinceCode": "NY",
  "phone": "+1 234 523 11 42"
  }
);
```

> Full function call example

```js
Ecwid.Cart.setBillingAddress({
  "name": "John Carmichael",
  "companyName": "Cool Slippers",
  "street": "5th Ave",
  "city": "New York",
  "countryName": "United States",
  "postalCode": "10002",
  "stateOrProvinceCode": "NY",
  "phone": "+1 234 523 11 42"
  },
  function(){
   console.log('Address successfully set')
  },
  function(){
   console.log('Error setting the address');
  }
)
```

`Ecwid.Cart.setBillingAddress()` allows you to set billing address for customer in storefront. You can use it to specify some address from your custom form on your website. 

If you specify some fields only (name, for example), then Ecwid will apply the changed fields only, leaving preset ones as they were before.

When function is called, Ecwid will set the 'My shipping address is the same as the billing address' flag to `false` automatically.

**Fields:**

Name | Type | Description
-----|------|-------------
**address** | \<*Person*\> | Customer's billing address details
successCallback | function | Success callback function
errorCallback | function | Error callback function

**Person** fields:

Name | Type | Description
-----|------|------------
name | string | Customer's name
companyName | string | Customer's company name
street | string | Customer's street address
city | string | Customer's city
countryName | string | Customer's country name. `countryCode` can be used instead
countryCode | string | Customer's country code. `countryName` can be used instead
postalCode | string | Customer's zip code
stateOrProvinceCode | string | Customer's state or provice code
phone | string | Customer's phone number


<aside class='notice'>
Parameters in bold are mandatory
</aside>

`errorCallback` structure is: errorCallback(errCode, errMsg)

Name | Type | Description
-----|------|-------------
errCode | number | Error code
errMsg | string | Error message

**Errors**

Error code | Error message
-----------|--------------
0 | Missing argument
100 | Incorrect data passed
1000 | Store owner disabled this functionality

### Generate cart with products

Ecwid JavaScript API allows you to add items to customer's cart automatically. This can be useful when you are using custom storefront to provide any type of button to add products to cart.

Using the following steps you will be able to create links to your storefront with products in cart for your customers. So all they have to do is just clikc that promo link to go to your store and have products added to cart automatically.

Let's check out how this can be achieved: 

**Step 1: Add external files to your website**

> Script and CSS to load on your storefront page

```html
<link rel="stylesheet" type="text/css" href="https://s3.amazonaws.com/ecwid-addons/apps/ecwid-cart-app/cartapp.css">
<script src="https://s3.amazonaws.com/ecwid-addons/apps/ecwid-cart-app/cart.js"></script>
```

Add this code to the source code of the page, where your Ecwid storefront is displayed. Add the script **after** or below Ecwid integration code.

**Step 2: Generate your cart**

> Generate items in cart example

```js
var cartItems = [{
    "id": 66821181, // ID of the product in Ecwid
    "quantity": 3, // Quantity of products to add
    "options": { // Specify product options
        "Color": "White",
        "Size":"11oz"
    }
}, {
    "id": 66722581,
    "quantity": 5
}];

var cart = {
     "gotoCheckout": true, // go to next checkout step right away (after 'Cart' page)
     "products": cartItems, // products to add to cart
     "profile": {
         "address": { // Shipping address details
             "name": "john smith",
             "companyName": "general motors",
             "street": "5th Ave",
             "city": "New York",
             "countryCode": "US",
             "postalCode": "10002",
             "stateOrProvinceCode": "NY",
             "phone": "+1 234 235 22 12"
         },
         "billingAddress": { // Billing address details
             "countryCode": "US",
             "stateOrProvinceCode": "AL",
         },
         "email": "test@test.com", // Customer email
         "orderComments": "Comments!" // Order comments
     }
 }

cart = JSON.stringify(cart);
cart = encodeURIComponent(cart);

console.log(cart);

// prints the resulting string you need to use in step 3
//
// %7B%22gotoCheckout%22%3Atrue%2C%22products%22%3A%5B%7B%22id%22%3A66821181%2C%22quantity%22%3A3%2C%22options%22%3A%7B%22Color%22%3A%22White%22%2C%22Size%22%3A%2211oz%22%7D%7D%2C%7B%22id%22%3A66722581%2C%22quantity%22%3A5%7D%5D%2C%22profile%22%3A%7B%22address%22%3A%7B%22name%22%3A%22john%20smith%22%2C%22companyName%22%3A%22general%20motors%22%2C%22street%22%3A%225th%20Ave%22%2C%22city%22%3A%22New%20York%22%2C%22countryCode%22%3A%22US%22%2C%22postalCode%22%3A%2210002%22%2C%22stateOrProvinceCode%22%3A%22NY%22%2C%22phone%22%3A%22%2B1%20234%20235%2022%2012%22%7D%2C%22billingAddress%22%3A%7B%22countryCode%22%3A%22US%22%2C%22stateOrProvinceCode%22%3A%22AL%22%7D%2C%22email%22%3A%22test%40test.com%22%2C%22orderComments%22%3A%22Comments!%22%7D%7D
//
```

In order for the script to add items to cart, we need to 'tell' it what items to add. Check the example on the right on how to do this. The `cart` variable will have the generated cart content in it. 

The syntax is very similar to adding products to cart via [Ecwid JavaScript API](https://developers.ecwid.com/api-documentation/manage-customer-cart#ecwid-cart-addproduct).

**Step 3: Create a link for customers**

> Final link structure

```
http://example.com/store#!/~/cart/create={generatedCartCode}
```

> Final link to generated cart example

```
https://www.ecwid.com/demo#!/~/cart/create=%7B%22gotoCheckout%22%3Atrue%2C%22products%22%3A%5B%7B%22id%22%3A66821181%2C%22quantity%22%3A3%2C%22options%22%3A%7B%22Color%22%3A%22White%22%2C%22Size%22%3A%2211oz%22%7D%7D%2C%7B%22id%22%3A66722581%2C%22quantity%22%3A5%7D%5D%2C%22profile%22%3A%7B%22address%22%3A%7B%22name%22%3A%22john%20smith%22%2C%22companyName%22%3A%22general%20motors%22%2C%22street%22%3A%225th%20Ave%22%2C%22city%22%3A%22New%20York%22%2C%22countryCode%22%3A%22US%22%2C%22postalCode%22%3A%2210002%22%2C%22stateOrProvinceCode%22%3A%22NY%22%2C%22phone%22%3A%22%2B1%20234%20235%2022%2012%22%7D%2C%22billingAddress%22%3A%7B%22countryCode%22%3A%22US%22%2C%22stateOrProvinceCode%22%3A%22AL%22%7D%2C%22email%22%3A%22test%40test.com%22%2C%22orderComments%22%3A%22Comments!%22%7D%7D
```

Now we need to fill in customer's cart when your custom link is opened. 

First things first, let's determine where your Ecwid store is displayed and get a direct link to that page. For example, our demo store is located in: `https://www.ecwid.com/demo`
And to fill in customer's cart with items that you seleted earlier, we need to create a link to your storefront with the generated cart part. 

**Example link**

Generated link with products added automatically to cart for Ecwid demo store will be: 

`https://www.ecwid.com/demo#!/~/cart/create=%7B%22gotoCheckout%22%3Atrue%2C%22products%22%3A%5B%7B%22id%22%3A66821181%2C%22quantity%22%3A3%2C%22options%22%3A%7B%22Color%22%3A%22White%22%2C%22Size%22%3A%2211oz%22%7D%7D%2C%7B%22id%22%3A66722581%2C%22quantity%22%3A5%7D%5D%2C%22profile%22%3A%7B%22address%22%3A%7B%22name%22%3A%22john%20smith%22%2C%22companyName%22%3A%22general%20motors%22%2C%22street%22%3A%225th%20Ave%22%2C%22city%22%3A%22New%20York%22%2C%22countryCode%22%3A%22US%22%2C%22postalCode%22%3A%2210002%22%2C%22stateOrProvinceCode%22%3A%22NY%22%2C%22phone%22%3A%22%2B1%20234%20235%2022%2012%22%7D%2C%22billingAddress%22%3A%7B%22countryCode%22%3A%22US%22%2C%22stateOrProvinceCode%22%3A%22AL%22%7D%2C%22email%22%3A%22test%40test.com%22%2C%22orderComments%22%3A%22Comments!%22%7D%7D`

<aside class='note'>
Please note that this is an example link and it will not work in Ecwid's demo store. Please test this feature in your own website.
</aside>

## Get Cart Details

Find out more about cart in its current state.

### Cart Object

Cart object is a snapshot of essential shopping cart properties, passed via various callbacks. Cart object does not provide direct memory access to the actual cart that Ecwid uses — i.e. changing this exact object will not alter the actual cart Ecwid uses for placing the order.

> Get quantity of products in cart example

```js
Ecwid.OnCartChanged.add(function(cart) {
  console.log("Products in cart now: " + cart.productsQuantity);
})

// prints
// Products in cart now: 1
```

**Fields:**

Name | Type | Description
---- | ---- | -----------
items | Array of \<*CartItem*\> | Enlists all items currently present in customer’s cart
productsQuantity | Integer | Total number of product varieties in cart
orderId | Integer | Unique internal order ID for this order (available after order is created)
couponName | String | The name of the coupon (if any) applied to the cart. If no coupon was applied, will contain undefined. Does not contain the actual code of coupon, just the name.
weight | Number | Total weight of the items in cart
paymentMethod | String | The name of the selected payment method (if any)
shippingMethod | String | The name of the selected shipping method (if any)

### Order Object

Order object represents details of current customer's order.

> Get order total example

```js
Ecwid.Cart.calculateTotal(function(order) {
  console.log(order.total);
})

// prints
// 13.25
```

**Fields:**

Name | Type | Description
---- | ---- | -----------
cart | Object (Cart) | The object describing the state or customer's cart
couponDiscount | Integer | An absolute amount of coupon code discount for this order
customerGroupDiscount | Integer | An absolute amount of a discount based on customer group for this order
customerGroupVolumeDiscount | Integer | An absolute amount of a discount based on customer group and subtotal for this order
discount | Integer | An absolute amount of a total discount for this order
handlingFee | Integer | An absolute amount of handling fee applied to order
shipping | Integer | An absolute amount of shipping rate applied to order
tax | Integer | An absolute amount of tax rate applied to order
total | Integer | Total amount of this order
volumeDiscount | Integer | An absolute amount of a discount based on subtotal for this order


### CartItem Object

CartItem represents a single item (product variety) in cart.

> Get quantity of a product in cart example

```js
Ecwid.OnCartChanged.add(function(cart) {
  console.log("There are " + cart.items[0].quantity + " items of " + cart.items[0].product.name + " in cart now.");
})

// prints
// There are 1 items of Apple product in cart now.
```

**Fields:**

Name | Type | Description
---- | ---- | -----------
quantity | Integer | Quantity of the given product variety in cart
product | Array of \<*Product*\> | The map of product properties (combination properties, if the combination is added to cart)
options | Obejct with option names and values | Map of the product options (option name as a key and option value as a value). For listboxes and radio buttons value will be the string value of the selected option. For checkboxes — names of the selected options, comma separated. For date options — string representing the selected date according to the shop’s format (Ecwid control panel > System settings > General > Formats and Units). For textboxes и textareas — the text given by the customer. For file upload options — string in the form of „4 files”

### Product Object

Product object represents details of a specific product in cart

> Get product name and SKU in cart example

```js
Ecwid.OnCartChanged.add(function(cart) {
  console.log(cart.items[0].product.name + " in the cart has SKU: " + cart.items[0].product.sku);
})

// prints
// Apple product in the cart has SKU: TEST-1234
```

**Fields:**

Name | Type | Description
---- | ---- | -----------
id | Integer | Internal unique product ID
name | String | Product name
price | Integer | Product price
shortDescription | String | Product description truncated to 120 characters
sku | String | Product SKU
url | String | URL to this product details page in store front (store front URL is generated from a field in Ecwid Control panel > Settings > General > Store profile)
weight | Integer | Weight of a product

## JS API Examples

Redirect Ecwid Paypal Orders to Custom “Thank You” Page:
[http://stevestruemph.com/redirect-ecwid-paypal-orders-to-custom-thank-you-page/](http://stevestruemph.com/redirect-ecwid-paypal-orders-to-custom-thank-you-page/)

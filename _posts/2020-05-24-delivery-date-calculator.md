---
layout: post
title:  "Delivery Date calculator using product metafields "
date:   2020-05-24 21:26:35 +0530
categories: Debut
icon: '11'
---

Wouldn't it be nice to have a delivery date estimate on the product page like; if you order this product today, it will be delivered by 25th Mon 2020?
My plan here is to save the day count as a product metafield and a bit of javascript to display estimated day of delivery. 

I'm using Debut theme `v16.5.5` and a [free metafieds editor](https://apps.shopify.com/metafields-editor-2) app for adding the product metafields.

 

On my dev store i have created a metafield with value `15` days (integer) and it can be accessed using the following code.

 
``` liquid
{%raw%}
{{ product.metafields.delivery.days_count }}
{%endraw%}
```

And using javascript date Object, we can calculate the estimated day of delivery from now.

``` javascript
{%raw%}
var currentTime = new Date();
var daysCount = {{product.metafields.delivery.days_count}}
currentTime.setDate(currentTime.getDate()+ daysCount);
{%endraw%}
```
## Implementing on product template

Use the below code at the very bottom of `templates/product.liquid` . It will show the Delivery date, right below the product price. A [live demo is available here](https://nichegeek.myshopify.com/products/13-inch-macbook-air-128-gb) 


``` javascript
{%raw%}
{%- if  product.metafields.delivery.days_count != nil -%}
<script type="text/javascript">

  window.onload = function () {
 
    var currentTime = new Date();
    currentTime.setDate(currentTime.getDate() + {{product.metafields.delivery.days_count}});
    var DeliveryDay = currentTime.toDateString();

    $('<strong style="color:green">Order today! and get Delivered by '+DeliveryDay+'</strong>').insertAfter( ".product__price" );

  }

</script>
{%- endif -%}
{%endraw%}
```


---
layout: post
title:  "Upgrade to Product Bundle feature on cart page"
date:   2020-05-26 21:26:35 +0530
icon: '01'
---

I found the idea on this [forum post](https://community.shopify.com/c/Shopify-Apps/Replace-a-product-in-the-cart-with-another/m-p/751439#M23512)

Using Debut theme `v16.5.5` and [free metafieds editor](https://apps.shopify.com/metafields-editor-2) app , I figured out a way to do the same.

I have created two demo products. [1. Abbey Road (Album)](https://nichegeek.myshopify.com/products/abbey-road-album) and [2. Here comes the sun (Song)](https://nichegeek.myshopify.com/products/here-comes-the-sun-song) . So if product `2` is in the cart, the cart should prompt an upgrade option to the user.

The following metafield   ```{%raw%} {{ product.metafields.parent.bundle }} {%endraw%}``` will connect both products using `product handle` . In this case, i'm creating a metafield for product `2` which will be handle of product `1`, ie `abbey-road-album`

### Solution
 Create a snippet **upgrade-to-bundle.liquid**  and render it with `product` and `line_item` as variables
  inside **cart-template.liquid**


```  {%raw%}{% render 'upgrade-to-bundle', product: item.product, line_item: item %} {%endraw%} ```

And below is the code for **upgrade-to-bundle.liquid** , where it checks the product metafield and assigns the bundle variant to the upgrade link.

``` liquid
{%raw%}
{%- liquid
 	if product.metafields.parent.bundle !=  nil
	 assign hasBundle = 'true'
	 assign productBundle = all_products[product.metafields.parent.bundle]
	endif
-%}
{% if hasBundle == 'true' and productBundle.title!= nil %}

<a href="#" id="upgrade-action" 
   data-variant-id="{{ line_item.variant_id  }}" 
   data-bundle-id="{{productBundle.selected_or_first_available_variant.id}}">

  <small> Would you like a upgrade to {{productBundle.title}} - {{ productBundle.price | money}} ? </small>
  
</a>

{%endif%}
{%endraw%}
```

And using AJAX API update the cart with javascript as in [Creating an Upsell feature without using an app](https://ecommercestudio.in/debut-theme/2020/05/20/debut-theme-upsell-product-cart.html)

 ``` javascript

$(document).on('click','#upgrade-action', function(){
  $(this).attr('disabled','disabled')
  var bundle = $(this).attr('data-bundle-id')
  var variant = $(this).attr('data-variant-id')
 
  jQuery.ajax({
    type: 'POST',
    url: '/cart/update.js', 
    data: 'updates['+bundle+']=1&updates['+variant+']=0', 
    dataType: 'json',
    success: function() { location.href = '/cart/'; },
    error: function(err) { console.log(err.responseText)}
  });

})


```
![working demo](/images/posts/upgrade-to-product-bundle.gif)

---
layout: post
title:  "Creating an Upselling feature without using an app "
date:   2020-05-20 12:26:35 +0530
categories: Debut-theme
---

I'm trying to replicate the free version of this [ Upsell Cart App ](https://apps.shopify.com/in-cart-upsell). The paid plans have better targeting options; like location and quantity of other items inside cart, but if its a single upsell;a custom solution is enough IMHO. I'm using Debut theme `v16.5.5`. 

### Create a schema type for the upsell product

Inside `sections/cart-template.liquid` create a product type inside settings

``` json
{
    "type":"header",
    "content": "Upsell Product"
},
{
    "type":      "product",
    "id":        "upsell_product",
    "label":     "Select product"
}
```

Save it and open the Cart page inside theme Customizer and choose a product for upsell 

### Create a snippet 

Create a snippet `upsell-product.liquid` with the following the markup
 
``` html
<div class="upsell_product">
    <img src="{{ upsell_product.images[0] | img_url: 'small'}}">
    <div class="info"> Would you like to add {{upsell_product.title}} &nbsp;
        <strong>{{upsell_product.price | money}}</strong></div>
    <div class="add-to-cart">
    <select id="upSell_qty"> 
        <option selected value="1">1</option>
        <option value="2">2</option>
        <option value="3">3</option>
    </select> 
    <button data-variant-id="{{upsell_id}}" type="button" class="btn btn--small" id="upSell_addTocart">
        Yes! I want this
    </button>
    </div>
</div>
 ```
 
Add some basic styles

``` scss
.upsell_product{
  border: 1px solid #ccc;
  background: #fff;
  padding: 20px 0;
  display: flex;
  justify-content: center;
  align-items: center;
  
    .info,.add-to-cart{
        display: flex;
        margin: 0 10px;
    }
}

```
And render it inside `sections/cart-template.liquid`  before the cart form {% raw %} `{% render 'upsell-product' %}`  {% endraw %}

 
#### Fetch data from scheme settings in `snippet/upsell-product.liquid` 
Assuming that the product only has a single variant.

 {% raw %}
``` liquid

    {%- assign upsell_product =  all_products[section.settings.upsell_product] -%}
    {%- assign upsell_id = upsell_product.variants.first.id -%}

```
 {% endraw %}


#### Create a condition for checking whether the upsell product is already in the cart or not

 {% raw %}
``` liquid

    {% assign upsell_product_in_cart = 0 %}
    {% for item in cart.items %}
        {% if item.id == upsell_id %}
            {% assign upsell_product_in_cart = item.quantity %}
        {% endif %}
    {% endfor %}

```
 {% endraw %}


####  add product data into the markup along with conditions mentioned above

 {% raw %}
``` liquid
    {% if section.settings.upsell_product != nil %}

        {%- assign upsell_product =  all_products[section.settings.upsell_product] -%}
        {%- assign upsell_id = upsell_product.variants.first.id -%}
    
        {% assign upsell_product_in_cart = 0 %}
            {% for item in cart.items %}
            {% if item.id == upsell_id %}
                {% assign upsell_product_in_cart = item.quantity %}
            {% endif %}
        {% endfor %}
    	
       {% if upsell_product_in_cart == 0 %}
        <div class="upsell_product">
           <img src="{{ upsell_product.images[0] | img_url: 'small'}}">
          <div class="info"> Would you like to add {{upsell_product.title}} &nbsp;<strong>{{upsell_product.price | money}}</strong></div>
          <div class="add-to-cart">
           <select id="upSell_qty"> 
             <option selected value="1">1</option>
             <option value="2">2</option>
             <option value="3">3</option>
            </select> 
            <button data-variant-id="{{upsell_id}}" type="button" class="btn btn--small" id="upSell_addTocart">Yes! I want this</button>
          </div>
        </div>
      {% endif %}
    {%endif%}
 ```
 {% endraw %}



### Add javascript for updating the cart

On success, it should reload the cart

 ``` javascript

$(document).on('click','#upSell_addTocart', function(){
  $(this).attr('disabled','disabled')
  var variant = $(this).attr('data-variant-id')
  var qty =  $('#upSell_qty').val()

  jQuery.ajax({
    type: 'POST',
    url: '/cart/update.js', 
    data: 'updates['+variant+']='+qty, 
    dataType: 'json',
    success: function() { location.href = '/cart'; },
    error: function(err) { console.log(err.responseText)}
  });

})

```
### Final Fixes

Debut `v16.5.5` has ajax enabled **Remove** link by default. Delete `data-cart-remove` from the anchor tag in order to reload the cart.


#### A live demo is available here  [https://nichegeek.myshopify.com/cart](https://nichegeek.myshopify.com/cart) 

![working demo](/images/posts/debut-theme-upsell.gif)



---
layout: post
title:  "Bulk add to cart for wholesale products"
date:   2020-12-02 21:26:35 +0530
---
### Use case 

- Display a table with variants / inventory levels / qty selectors for each variant
- Button to add all selected variant quantities with a single click
- Unavailable variants (with 0 stock) should have selector buttons disabled / inactive



### Display a table with variants

``` python {%raw%}
        <div class="wholesale-form">
        <form>
            <table width="100%" class="wholesale-form">
            <thead>
                <td width="23%"> Size </td>
                <td width="23%"> In Stock </td>
                <td width="43%"> Order Qty </td>
            </thead>
            {% for variant in product.variants%}
                <tr class="variant  {% if variant.inventory_quantity <= 0 %}  stock-out {% endif%}">
                <td class="size">{{variant.title}}</td>
                <td>{{variant.inventory_quantity}}</td>
                <td> 
                    <div class="purchase-details__quantity product-quantity-box" style="width: auto">
                    
                    <span class="ss-icon product-minus js-change-quantity" data-func="minus"><span class="icon-minus"></span></span>
                    <input type="number" data-variant="{{variant.id}}" min="0" size="2" class="quantity" name="quantity" id="quantity" value="0"  max="{{ variant.inventory_quantity }}"  />
                    <span class="ss-icon product-plus js-change-quantity" data-func="plus"><span class="icon-plus"></span></span>
                    </div>
                </td>
                </tr>
            {% endfor %}
            </table>
            <button id="bulk-add" class="action_button ">Add selected to cart</button>
        </form>
        </div>
     {%endraw%}
```

### Add all selected variant quantities with a single click

``` javascript

  $(document).ready(function(){
    var payLoad = []
    var qtychecker = 0
    $('#bulk-add').on('click',function(e){
    	e.preventDefault();
   
        $('.wholesale-form .variant:not(.stock-out)').each(function(){
            var product = {}
            qtychecker =  qtychecker + parseInt($(this).find('.quantity').val())
            product.quantity = parseInt($(this).find('.quantity').val())
            product.id = $(this).find('.quantity').attr('data-variant')
            payLoad.push(product)
        })
        
        if(qtychecker > 0){  
          $(this).addClass('disabled')
            jQuery.post('/cart/add.js', {items:  payLoad}, function(data, response){ 
            if(response == 'success'){
              window.location.href="/cart"
            } 
          },"json").fail(function(data,response) {alert(data.responseJSON.description); location.reload()}  )
          
        }else{
        	alert('Product quantity must be greater than zero')
        }
    })
  })

```
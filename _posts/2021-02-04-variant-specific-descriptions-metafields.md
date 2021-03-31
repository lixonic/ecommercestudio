---
layout: post
title:  "Variant specific descriptions using Metafields"
date:   2020-11-03 21:26:35 +0530
---
 
### Use case 

 To display additional info based on selected variant apart from product descriptions. 

### Solution  

Save the additional info as a variant metafield and display it based on variant change using javascript.

``` python {%raw%}
  {% for variant in sample_product.variants%}
    {% assign variantHandle =  variant.title | handle %}
        <div id="{{variantHandle}}" class="variant-description" 
        {% unless variant.id == product.selected_or_first_available_variant.id%} 
          style="display:none"
         {%endunless%}>
            {{variant.metafields.additional_info.content}}    
        </div>
    {% endfor %}
     {%endraw%}
```
Assuming the select dropdown has an attribute {%raw%} `data-handle={{variant.title | handle}}` {%endraw%} and class called `option-selector`
``` javascript
  $('.option-selector').on('change',function(){
    var variantHandle = $(this).attr(data-handle)
    $('.variant-description').hide()
    $('.variant-description#'+variantHandle).show()
  })

```
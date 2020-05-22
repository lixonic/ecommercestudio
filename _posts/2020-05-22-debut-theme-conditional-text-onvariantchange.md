---
layout: post
title:  "Conditional display of text, based on SKU onVariant Change   "
date:   2020-05-22 08:26:35 +0530
categories: Debut-theme
---

Here is how you can display a conditional text based on SKU of a product upon changing the variant dropdown. I'm using Debut `v16.5.5` for the sake of simplicity. Later on, i will add fixes for other Shopify supported themes as i get time.

### Debut (v16.5.5)

1. Create a placeholder for the text inside `product-template.liquid` . I created one above the product form and added a liquid condition if the variant is selected or available as first on page load.
    {%raw%}
    ```liquid
    <div class="sku-txt">
        {% if current_variant.sku == 'SKU1'%} <strong>Custom text for SKU 1</strong> {%endif%}
    </div>
    {% form 'product', product, class:form_classes, novalidate: 'novalidate', data-product-form: '' %}
    ```
    {%endraw%}

2. Go to theme.js and locate `_updateMasterSelect` function. As it receives `variant` as an object, you can write a simple if condition with `variant.sku`

    ``` javascript
        /**
         * Update hidden master select of variant change
         *
         * @param  {variant} variant - Currently selected variant
         */
        _updateMasterSelect: function(variant) {
            if(variant.sku == 'SKU1'){
                $('.sku-txt').html('<strong>Custom text for SKU1 only</strong>')
            }else{
                $('.sku-txt').html('')
            }
            $(this.originalSelectorId, this.$container).val(variant.id);
        }
```

3. Save it and done!
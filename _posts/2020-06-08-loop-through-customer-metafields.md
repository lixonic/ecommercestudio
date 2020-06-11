---
layout: post
title:  "Display customer specific info inside product template"
date:   2020-06-08 21:26:35 +0530
categories: Boundless 
icon: '08'
---

Here is a use-case of customer metafields.

Some vendors wants to add customer numbers and account managers attached to each customer's information/profile and display additional information on product page based on this specific data.


```liquid {%raw%}
{% if customer %}
    {% for metafield in customer.metafields.Namespace%}
        {{ metafield | last}}
    {% endfor %}
{% endif %}
  {%endraw%} ```

Above code will loop through the customer metafields which share a common `Namespace` and prints out the value of `keys`

And by using `if` or `switch` statements you display additional data.

```liquid {%raw%}
{% if customer %}
    {% for metafield in customer.metafields.%}
       {% if metafield.last == 'gluten allergy' %} This product is gluten free! {%endif%}
    {% endfor %}
{% endif %}
  {%endraw%} ```
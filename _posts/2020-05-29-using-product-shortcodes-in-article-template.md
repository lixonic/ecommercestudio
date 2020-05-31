---
layout: post
title:  "Using product shortcodes inside article template"
date:   2020-05-29 21:26:35 +0530
icon: '34'
---

Normally we use metafields or product tags to include a related product into an article. But how about using a shortcode for the same. For this example i'm using a demo [blog post](https://nichegeek.myshopify.com/blogs/news/how-to-build-and-launch-an-impactful-online-workshop) 


I have put the product title in between the blog content like `[13-inch MacBook Air 128 GB]` and use `all_products` object for getting the product info.

![Using product shortcodes inside article template](https://monosnap.com/image/9HvuRE6OU3GBoeq4uwT1qDiGJEPOAc)
``` liquid
{%raw%}
    {%-assign content =  article.content | split: '['-%}

    {%- assign description_one = content[0]-%}
    {%- assign description_with_product = content[1]-%}


    {%- assign description_with_product = description_with_product | split: ']'-%}
    {%- assign description_two = description_with_product[1]]-%}

    {%- assign product_handle =  description_with_product[0] | strip | handle -%}
    {%- assign product =  all_products[product_handle] -%}

    <p>{{ description_one }}</p>

    {% include 'product-card-grid', product: product%}

    <p>{{description_two}}</p>

{%endraw%}
```


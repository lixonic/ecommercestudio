---
layout: post
title:  "Product Image Gallery for Boundless theme"
date:   2020-06-01 21:26:35 +0530
modified_date:   2020-12-17 01:00:00 +0530 
categories: Boundless 
icon: 31
---

There is no option for product image slideshow for Boundless theme. So i decided to create one using the same slick plugin that powers the slideshow/hero section and an alternate product template. Theme version is `v9.0.17`

1. Create a alternate product template `product.image-gallery.liquid `

2. Create a product template section  product-template-gallery.liquid and rename the default section inside alternate template you created above -`{%raw%} {% section 'product-template' %} {%endraw%}`  to `{%raw%}{% section 'product-template-gallery' %} {%endraw%}`  . See screenshot below


3.  Replace the code inside product-template-gallery with [this Gist](https://gist.github.com/lixonic/c05ed4927aaa3ebed72ba3e4c935d95a)

4.  Initialize the sliders inside theme.js.liquid.  See screenshot below

``` javascript

 $('.product-slideshow').slick({
    dots: true,
    infinite: true,
    speed: 500,
    slidesToShow: 1,
    slidesToScroll: 1,
    arrows: false,
    asNavFor: '.product-slideshow-nav'
  })

  $('.product-slideshow-nav').slick({
    slidesToShow: 3,
    slidesToScroll: 1,
    infinite: true,
    asNavFor: '.product-slideshow',
    dots: false,
    arrows: true,
    focusOnSelect: true,
    nextArrow: '<button type="button" class="slick-next">&raquo;</button>',
    prevArrow: '<button type="button" class="slick-prev">&laquo;</button>'

  });

```

Add  some custom styles for the arrow buttons inside theme.scss.liquid and select the newly created product template inside product admin. Done!


``` css
.slick-prev,.slick-next{
    top: 42%;
    font-size: 2em;
}
.slick-next{
    right: -5%
}
.slick-prev{
    left: -5%
}
```
This solution is taken from - [How to show Product Image Gallery on Product Page (Boundless Template)] [forum-post]


### UPDATE  

The old version of slick slider in Boundless theme gives you [this bug](https://github.com/kenwheeler/slick/issues/1248). This will highlight all 3 thumbnails at once as seen in below video.

And if you try to update to a latest version in `vendor.js`, it will create issues with the homepage slider. 

The fix involves creating a product template specific `vendor-product.js` and conditionally including this in `theme.liquid`

```

  {%raw%} {%unless template contains 'product'%}
    <script src="{{ 'vendor.js' | asset_url }}" defer="defer"></script>
  {%else%}
    <script src="{{ 'vendor-product.js' | asset_url }}" defer="defer"></script>
  {%endunless%}
{%endraw%}
```

 <img src="https://api.monosnap.com/file/download?id=5fdo7guQQjrwxIP20JzVJe13yHsLE7" alt="">
<img src="https://api.monosnap.com/file/download?id=wPi93vvuhCqMFJQnKwbqxqdKkZULmb" alt="">
<iframe width="100%" height="515" src="https://monosnap.com/image/O0gpaGs6UNOZ1IhWJpKxnHWBpqeYDR" frameborder="0" controls allowfullscreen></iframe>

[forum-post]: https://community.shopify.com/c/Technical-Q-A/How-to-show-Product-Image-Gallery-on-Product-Page-Boundless/m-p/762783#M26282

---
layout: post
title:  "Mobile specific hero section image on Debut theme"
date:   2020-06-07 21:26:35 +0530
categories: Debut 
icon: 36
---
A lot of people are asking for a solution on forums on this topic and also the same with slideshow. The logic applied is same for both; which is toggle a mobile specific image or slide (9:16) using media queries. On my development store the Debut theme version is `16.5.5`

Open sections/hero.liquid and create an option for mobile image. See [screenshot](https://take.ms/WsigF)

``` json

{
    "type": "image_picker",
    "id": "mobile_image",
    "label": "Mobile Image"
}

```

 Save the above changes and you will get an option "Mobile image" right after the Main image picker inside the customize theme menu. See [screenshot](https://take.ms/Cc46Z)

 Pick a 9:16 image and save it. Now write some custom styles for toggling this image on mobile/desktop. Open theme.scss.liquid and paste this at the very bottom.

 ``` css
.mobile-only{ display: none}
.desktop-only{ display: block}

@media (max-width:768px){
    .mobile-only{ display: block}
    .desktop-only{ display: none}
}
 ```

As you are using "Full-width" layout for this section, the code responsible is from line 67. See [screenshot](https://take.ms/YONmR)

Add a `desktop-only` class as per the screenshot. 
```html
 <div class="hero desktop-only hero--{{ section.settings.hero_size }} hero-{{ section.id }}{% if section.settings.title != blank or section.settings.text != blank or section.settings.button_label != blank %} hero__overlay{% endif %} box ratio-container lazyload js">
 ```

Duplicate the entire div and place it under the current div element and give it a `mobile-only`  class.  Both should be above the <noscript> element. See [screenshot](https://take.ms/1yGCy)

``` html{%raw%}
 <div class="hero mobile-only hero--{{ section.settings.hero_size }} hero-{{ section.id }}{% if section.settings.title != blank or section.settings.text != blank or section.settings.button_label != blank %} hero__overlay{% endif %} box ratio-container lazyload js"
         id="Hero-{{ section.id }}"
         data-layout="{{ section.settings.hero_layout }}"
         {%- if section.settings.mobile_image -%}
          {% unless section.settings.mobile_image.alt == blank %}
          role="img"
          aria-label="{{ section.settings.mobile_image.alt | escape }}"
          data-alt="{{ section.settings.image.alt | escape }}"
          {% endunless %}
         data-bg="{{ section.settings.mobile_image | img_url: '300x300' }}"
         data-bgset="{% include 'bgset', image: section.settings.mobile_image %}"
         data-sizes="auto"
         data-parent-fit="cover"
         style="background-position: {{ section.settings.alignment }}; background-image: url('{{ section.settings.mobile_image | img_url: '300x300' }});"
         {%- endif -%}>
    {%- if section.settings.image == blank -%}
      <div class="placeholder-background">
        {{ 'lifestyle-1' | placeholder_svg_tag: 'placeholder-svg' }}
      </div>
    {%- endif -%}
      <div class="hero__inner">
        <div class="page-width text-center">
          {%- if section.settings.title != blank -%}
            <h2 class="h1 mega-title{% if section.settings.text_size == 'large' %} mega-title--large{% endif %}">{{ section.settings.title | escape }}</h2>
          {%- endif -%}
          {%- if section.settings.text != blank -%}
            <div class="rte-setting mega-subtitle{% if section.settings.text_size == 'large' %} mega-subtitle--large{% endif %}">{{ section.settings.text }}</div>
          {%- endif -%}
          {%- if section.settings.button_label != blank and section.settings.button_link != blank -%}
            {%- capture ariaLabel -%}
              {{- section.settings.button_label -}}
              {%- if section.settings.button_link.type == 'frontpage_link' -%}
                : {{ 'homepage.general.title' | t -}}
              {%- elsif section.settings.button_link.type == 'catalog_link' -%}
                : {{ 'collections.catalog.title' | t -}}
              {%- elsif section.settings.button_link.object.title -%}
                : {{ section.settings.button_link.object.title -}}
              {%- endif -%}
            {%- endcapture -%}

            <a href="{{ section.settings.button_link }}"
              class="btn hero__btn"
              aria-label="{{ ariaLabel }}">
              {{- section.settings.button_label | escape -}}
            </a>
          {%- endif -%}
        </div>
      </div>
    </div>
     {%endraw%}
```

Save it and go to customize theme and check the changes. 

<iframe width="100%" height="360" src="https://monosnap.com/image/cETfuBbLyx6Wuz7Xtf4x6wXm3YQtTw" frameborder="0" controls allowfullscreen></iframe>


This solution is taken from - [optimise the customized image for mobile] [forum-post]
 
[forum-post]: https://community.shopify.com/c/Shopify-Design/optimise-the-customized-image-for-mobile/m-p/766919/highlight/true#M192068
---
layout: post
title:  "Simple theme bug fix - product image zoom-in"
date:   2020-04-17 21:26:35 +0530
categories: Simple theme
---

This bug only happens when the zoom type is set to be `zoom-in` (the default is lightbox) for the product pages on mobile. As you change the landscape/portrait mode, the zoom functionality will throw an error.

{% highlight javascript %}
Uncaught TypeError: Cannot read property '$productSinglePhotos' of undefined
at Object.unmatch (theme.js?v=13733231539381766514:928)
{% endhighlight %}


The issue is a typo inside theme.js, where the `zoom-in` is destroyed and reinitialized at `line:928` . It should be  `self` instead of `this`, as in `var self = this;` at `line:903`. 

So it must be `self.selectors.$productSinglePhotos.trigger('zoom.destroy');`

{% highlight javascript %}
        unmatch: function() {
          if (zoomType === 'zoom-in') {
            // Destroy zoom (in case it was already set), then set it up again
            self.selectors.$productSinglePhotos.trigger('zoom.destroy');

            // reinit product zoom
            this.selectors.$productSinglePhotos.each(function() {
              $(this)
                .addClass('image-zoom')
                .zoom({
                  url: $(this)
                    .find('img')
                    .attr('data-zoom')
                });
            });
          } else if (zoomType === 'lightbox') {
            // reinit lightbox
            self.productImageGallery();
            self.selectors.$productSinglePhotos
              .find('img')
              .addClass('lightbox');
          }

          theme.variables.bpSmall = false;
        }
{% endhighlight %}

This solution is taken from - [I found a bug in the theme.js.liquid file for the simple theme regarding product image zoom-in] [forum-post]
 
[forum-post]: https://community.shopify.com/c/Technical-Q-A/I-found-a-bug-in-the-theme-js-liquid-file-for-the-simple-theme/m-p/710051/highlight/true#M22518
![imgix logo](https://assets.imgix.net/imgix-logo-web-2014.pdf?page=2&fm=png&w=120)

# imgix.js [![Build Status](https://travis-ci.org/imgix/imgix.js.svg?branch=master)](https://travis-ci.org/imgix/imgix.js) [![Slack Status](http://slack.imgix.com/badge.svg)](http://slack.imgix.com)

imgix.js allows developers to easily generate responsive images using the `srcset` and `sizes` attributes, and the `picture` element. This lets you write a single image URL that is parsed and used to make images look great at any screen size, by using [imgix](https://imgix.com) to process and resize your images on the fly.

Responsive images in the browser, simplified. Pure JavaScript with zero dependencies. About 2 KB minified and gzipped.

* [Overview / Resources](#overview-and-resources)
* [Installation](#installation)
* [Usage](#usage)
  * [`ix-src`](#ix-src)
  * [`ix-path` and `ix-params`](#ix-path-and-ix-params)
  * [`picture` tags](#picture-tags)
* [Browser Support](#browser-support)
* [Meta](#meta)


<a name="overview-and-resources"></a>
## Overview / Resources

* [Srcset and sizes by Eric Portis](https://ericportis.com/posts/2014/srcset-sizes/). A seminal article on `srcset`, `sizes`, and `picture`. Explains why they're necessary, and how they work together.
* [Using imgix with `<picture>`](https://docs.imgix.com/tutorials/using-imgix-picture-element). Discusses the differences between art direction and resolution switching, and provides examples of how to accomplish art direction with imgix.
* [Responsive Images with `srcset` and imgix](https://docs.imgix.com/tutorials/responsive-images-srcset-imgix). A look into how imgix can work with `srcset` and `sizes` to serve the right image.


<a name="installation"></a>
## Installation

There are several ways to install imgix.js. The appropriate method depends on your project.

1. **npm**: `npm install --save imgix.js`
2. **Bower**: `bower install --save imgix.js`
3. **Manual**: [Download the latest release of imgix.js](https://github.com/imgix/imgix.js/releases/latest), and use `dist/imgix.js` or `dist/imgix.min.js`.

After you've included imgix.js on your page, you just have to run it. You'll probably want to put this code at the bottom of your page, just before the `</body>` tag. This will scan the page for `img` and `source` tags that need to be processed, and initialize them:

``` html
<script src="imgix.js"></script>
<script>
  // Specify your imgix host. For these docs, we'll use `assets.imgix.net`.
  imgix.config.host = 'assets.imgix.net';
  // Find and initialize all uninitialized `img` or `source` tags with
  // `ix-src` or `ix-path` attributes.
  imgix.init();
</script>
```

By setting the `imgix.config.host` value to your imgix hostname, you enable the use of `ix-path` and `ix-params` to define images, instead of having to manually type URLs out in `imgix-src`. This has several advantages:

1. `ix-params` automatically URL/Base64 encodes your specified params, as appropriate.
2. `ix-params` is a JSON string, which is easier to read than a URL, and can be generated by other tools if necessary.
3. Not having to re-type `https://my-source.imgix.net` helps keep your code DRY.


<a name="usage"></a>
## Usage

Now that everything's installed and set up, you can start adding responsive images to the page. There are a few ways to do this.

<a name="ix-src"></a>
### `ix-src`

The simplest way to use imgix.js is to create an `img` tag with the `ix-src` attribute:

``` html
<img
  ix-src="https://assets.imgix.net/unsplash/hotairballoon.jpg?w=300&amp;h=500&amp;fit=crop&amp;crop=right"
  alt="A hot air balloon on a sunny day"
>
```

This will generate HTML something like the following:

``` html
<img
  ix-src="https://assets.imgix.net/unsplash/hotairballoon.jpg?w=300&amp;h=500&amp;fit=crop&amp;crop=right"
  alt="A hot air balloon on a sunny day"
  sizes="100vw"
  srcset="
    https://assets.imgix.net/unsplash/hotairballoon.jpg?w=100&amp;h=167&amp;fit=crop&amp;crop=right 100w,
    https://assets.imgix.net/unsplash/hotairballoon.jpg?w=200&amp;h=333&amp;fit=crop&amp;crop=right 200w,
    …
    https://assets.imgix.net/unsplash/hotairballoon.jpg?w=2560&amp;h=4267&amp;fit=crop&amp;crop=right 2560w
  "
  src="https://assets.imgix.net/unsplash/hotairballoon.jpg?w=300&amp;h=500&amp;fit=crop&amp;crop=right"
  ix-initialized="ix-initialized"
>
```

Since imgix can generate as many derivative resolutions as needed, imgix.js calculates them programmatically, using the dimensions you specify (note that the `w` and `h` params scale appropriately to maintain the correct aspect ratio). All of this information has been placed into the `srcset` and `sizes` attributes. Because of this, imgix.js no longer needs to watch or change the `img` tag, as all responsiveness will be handled automatically by the browser.


<a name="ix-path-and-ix-params"></a>
### `ix-path` and `ix-params`

Here's how the previous example would be written out using `ix-path` and `ix-params` instead of `ix-src`. Regardless of which method you choose, the end result in-browser will be the same.

**Please note**: `ix-params` must be a valid JSON string. This means that keys and string values must be surrounded by double quotes, e.g., `"fit": "crop"`.

``` html
<img ix-path="unsplash/hotairballoon.jpg" ix-params='{
  "w": 300,
  "h": 500,
  "fit": "crop",
  "crop": "right"
}' alt="A hot air balloon on a sunny day">
```

<a name="picture-tags"></a>
### `picture` tags

If you need art-directed images, imgix.js plays nicely with the `picture` tag. This allows you to specify more advanced responsive images, by changing things such as the crop and aspect ratio for different screens. To get started, just construct a `picture` tag with a `source` attribute for each art-directed image, and a fallback `img` tag. If you're new to using the `picture` tag, you might want to check out our [blog post](https://docs.imgix.com/tutorials/using-imgix-picture-element) on it to learn how it works.

The `source` tags can be used with `ix-src` or `ix-path` and `ix-params`, just like `img` tags. The following example will generate HTML that displays Bert _and_ Ernie on small screens, just Bert on medium-sized screens, and just Ernie on large screens.

``` html
<picture>
  <source media="(min-width: 880px)"
    sizes="430px"
    ix-path="imgixjs-demo-page/bertandernie.jpg"
    ix-params='{
      "w": 300,
      "h": 300,
      "fit": "crop",
      "crop": "left"
    }'
  >
  <source media="(min-width: 640px)"
    sizes="calc(100vw - 20px - 50%)"
    ix-path="imgixjs-demo-page/bertandernie.jpg"
    ix-params='{
      "w": 300,
      "h": 300,
      "fit": "crop",
      "crop": "right"
    }'
  >
  <source sizes="calc(100vw - 20px)"
    ix-path="imgixjs-demo-page/bertandernie.jpg"
    ix-params='{
      "w": 300,
      "h": 100,
      "fit": "crop"
    }'
  >
  <img ix-path="imgixjs-demo-page/bertandernie.jpg">
</picture>
```


<a name="browser-support"></a>
### Browser Support

* By default, browsers that don't support [`srcset`](http://caniuse.com/#feat=srcset), [`sizes`](http://caniuse.com/#feat=srcset), or [`picture`](http://caniuse.com/#feat=picture) will gracefully fall back to the default `img` `src` when appropriate. If you want to provide a fully-responsive experience for these browsers, imgix.js works great alongside [Picturefill](https://github.com/scottjehl/picturefill)!
* If you are using [Base64 variant params](https://docs.imgix.com/apis/url#base64-variants) and need IE <= 9 support, we recommend using a polyfill for `atob`/`btoa`, such as [Base64.js](https://github.com/davidchambers/Base64.js).

<a name="meta"></a>
## Meta

imgix.js was made by [imgix](http://imgix.com). It's licensed under the BSD 2-Clause license (see the [license file](https://github.com/imgix/imgix.js/blob/master/LICENSE.md) for more info). Any contribution is absolutely welcome, but please review the [contribution guidelines](https://github.com/imgix/imgix.js/blob/master/CONTRIBUTING.md) before getting started.

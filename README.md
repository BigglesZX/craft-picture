# Craft Picture (a Twig Bundle)

This is a Twig Bundle intended to be used via nystudio107's [Bundle Installer](https://github.com/nystudio107/bundle-installer). It provides a **Picture** template which is designed to make it easier to generate `<picture>` elements for responsive imagery using Craft CMS's image asset helpers. Pass in an image and some basic settings and a `<picture>` element will be rendered including a range of `srcset` candidates and alternative sources for the WebP format.

## Installation

Follow the installation instructions for [Bundle Installer](https://github.com/nystudio107/twig-bundle-installer/), which essentially boil down to:

```shell
$ composer require nystudio107/twig-bundle-installer
```

You should be prompted to give `allow-plugins` permission for that package.

Add this package:

```shell
$ composer require biggleszx/craft-picture
```

Install:

```shell
$ composer install
```

Add `/templates/vendor` to your root `.gitignore` file to avoid committing installed bundles to Git.

## Configuration

The Picture component needs to know which widths to use when transforming your images to generate `srcset` candidates. The default widths are:

```
2240, 1920, 1600, 1280, 960, 640, 320
```

...however you can override this on a per-usage basis (see below), or set a project-wide default by adding a `craftPictureSourceWidths` key to your [custom settings](https://craftcms.com/docs/5.x/configure.html#custom-settings) file:

```php
<?php
return [
    'craftPictureSourceWidths' => ['2240', '1920', '1600', '1280', '960', '640', '320'],
];
```

You will also need to have set up at least one named [image transform](https://craftcms.com/docs/5.x/development/image-transforms.html) so that the component knows how to transform your images.

## Usage

In the kitchen-sink example below:

* `image` is a Craft image asset instance
* The base `<img>` in the generated `<picture>` element will use a transform named `square`
* An additional media source is defined for `768px` and above which uses a transform named `landscape`

```twig
{% include 'vendor/biggleszx/craft-picture/templates/picture.twig' with {
    className: 'my-image',
    image: image,
    transform: 'square',
    sourceWidths: [1600, 1280, 960, 640, 320],
    media: [{
        media: '(min-width: 768px)',
        transform: 'landscape',
        sourceWidths: [2240, 1920, 1600, 1280, 960],
        sizes: '50vw',
    }],
    sizes: '100vw',
    alt: image.alt ?: 'Fallback alt text',
    loading: 'lazy',
} only %}
```

When using the `media` parameter, order matters the same way it does when writing plain HTML. The browser will use the first matching media configuration it encounters. Inside `media`, `sourceWidths` is optional and defaults to the root `sourceWidths` if omitted. Order doesn't
matter in this parameter.

When processing `sourceWidths` or `media.sourceWidths`, we filter out any
sizes that are larger than the width of the original image. If this results in
an empty array, we add back the original intrinsic image width as the sole value.

## Contributing

This package is mostly for my benefit in cutting down duplicated code between projects, however I hope it has broader usefulness. If you have any issues using the bundle, please feel free to open an issue on GitHub, and I'll take a look when I can.

## TODO

* Consider eager-loading appropriate image transforms from within the template. This is currently left as an exercise for the reader.

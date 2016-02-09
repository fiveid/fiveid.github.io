---
layout: post
title: Wordpress-View-Controller with Timber
---

I spend a lot of my time designing and building Wordpress themes. Although I'm a huge fan of the Wordpress platform, I find that while it works fantastically when getting small, simple websites up quickly there comes a point in larger projects when Wordpress almost ends up becoming a burden.

That might be as much to do with me as it has to do with Wordpress. Regardless, over time I've experimented with a few different approaches to improve my workflow. Specifically, I'm looking at the following:

* Increasing the readability and concision of template files
* Rationalising the theme directory structure to make finding and editing blocks of code easier and more consistent
* Separating PHP functionality from the template HTML (this tidies the template, but mainly provides as a good mental separation of logic)
* Creating a basic template with the capability to activate non-standard functionality that I use often
* Turning the theme directory into a "package" that can be easily moved while still communicating important information about its intended usage

There's been a couple of additions to the core Wordpress functionality in particular that I've benefited from. The first is the **Timber plugin** and the second is **dependency notifications**. I go over these in detail below.

## Timber Templates
I started using the [Timber Plugin](https://github.com/jarednova/timber) to create Wordpress themes with a sort of View-Controller pattern.

Personally, I find that using Timber results in more concise and readable template code. It forces complicated logic to be pushed outside the view and (mentally, at least) be declared up front.

Timber uses the Twig template engine. Twig has a syntax I learned to appreciate when working with [the Slim framework](http://www.slimframework.com), as well as when using other "handlebar-based" template engines with [Emberjs](http://emberjs.com), [Laravel](https://laravel.com).

Whether or not you prefer PHP or Twig is partly down to personal preference. But I find the difference between the following to be profound:

### Before (PHP template)

![Before: index.php][wp_vc_php]

### After (Twig template)

![After: index.twig][wp_vc_twig]

Timber is an excellent plugin that provides enables me to write clean-looking templates without limiting what I can do.

## Dependency Notifications
I've also taken to using a variation of the code created by [Paulund](http://www.paulund.co.uk/theme-users-required-plugins) to display required/recommended plugins to administrators, along with a direct link to install them.

For various reasons, I often end up creating multiple Wordpress installs to test a theme. I rarely want to export the entire thing into production, but frequently found myself wanting a way to keep track of what plugins I need/want running. Paulund's code made this possible.

Dependencies are listed in a `plugins.json` file, stored in the root of the theme. It looks something like this:

```json
{
  "require": {
    "advanced-custom-fields": ["advanced-custom-fields/acf.php", "*"],
    "contact-form-7": ["contact-form-7/wp-contact-form-7.php", "*"],
    "timber-library": ["timber-library/timber.php", "*"]
  },
  "suggest": {
    "wp-db-backup": ["wp-db-backup/wp-db-backup.php", "*"],
    "google-analyticator": ["google-analyticator/google-analyticator.php", "*"],
    "lazy-load": ["lazy-load/lazy-load.php", "*"],
    "amazon-web-services": ["amazon-web-services/amazon-web-services.php", "*"],
    "amazon-s3-and-cloudfront": ["amazon-s3-and-cloudfront/wordpress-s3.php", "*"]
  },
  "vendor": {
  }
}
```

Plugin dependencies are then displayed in the admin area like this:

![Dependencies][wp_vc_dependencies]

Obviously, installed plugins are not displayed. It's easy to simply click on the links and install the plugins.  

## Further Reading
Although I've already put this approach into practice in a number of work projects, as a public showcase I've applied it to the [TwentySixteen Wordpress theme](https://github.com/WordPress/twentysixteen).

The View-Controller variation is [now available on Github](https://github.com/fiveid/fiveid-vc-twentysixteen). The repository's `readme.md` expands on the structure and approach I took even further.

[Read more](https://github.com/fiveid/fiveid-vc-twentysixteen)

[wp_vc_php]: http://deadlamb.co.uk/portfolio/images/resources/wp-vc-theme_php.png "index.php"

[wp_vc_twig]: http://deadlamb.co.uk/portfolio/images/resources/wp-vc-theme_twig.png "index.php"

[wp_vc_dependencies]: http://deadlamb.co.uk/portfolio/images/resources/wp-vc-theme_dependencies.png "Theme dependency notices"

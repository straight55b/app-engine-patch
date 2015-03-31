# Important #

An improved and more feature-rich version of this project is developed independently as the [django-mediagenerator asset manager](http://www.allbuttonspressed.com/projects/django-mediagenerator)

## Old documentation ##
**Requirements:** You must have Java installed and it must be accessible via PATH.

Note: This feature is pre-configured in the sample project. You can disable it by simply removing "mediautils" from your `INSTALLED_APPS`.

In order to optimize your website you normally combine all JS (same goes for CSS) files into a single file and compress everything (remove all whitespace and comments), so your users can download all media files in as few requests and as quickly as possible. Depending on the complexity of your site this can provide a huge speed boost on first load.

Luckily, app-engine-patch helps you automate this process. In your settings.py you can specify how media files should be combined:

```
# Set default language without country code
# (LANGUAGE_CODE must be contained in LANGUAGES)
LANGUAGE_CODE = 'en'

# Only generate media files for English and German
LANGUAGES = (
    ('de', 'German'),
    ('en', 'English'),
)

# Combine media files
COMBINE_MEDIA = {
    # Create a combined JS file which is called "combined-en.js" for English,
    # "combined-de.js" for German, and so on
    'combined-%(LANGUAGE_CODE)s.js': (
        # Integrate bla.js from "myapp/media" folder
        # You don't write "media" because that folder is used automatically
        'myapp/bla.js',
        # Integrate morecode.js from "media" under project root folder
        'global/morecode.js',
    ),
    # Create a combined CSS file which is called "combined-ltr.css" for
    # left-to-right text direction
    'combined-%(LANGUAGE_DIR)s.css': (
        'myapp/style.css',
        # Load layout for the correct text direction
        'global/layout-%(LANGUAGE_DIR)s.css',
    ),
}

# Increase this when you update your media on the production site, so users
# don't have to refresh their cache. By setting this your MEDIA_URL
# automatically becomes /media/MEDIA_VERSION/
MEDIA_VERSION = 1

# Activate media generator
INSTALLED_APPS = (
   # ...
   'mediautils',
   # ...
)
```

As a convention, app-engine-patch expects that you name your main media files "combined-%(LANGUAGE\_CODE)s.js" and "combined-%(LANGUAGE\_DIR)s.css". This convention allows for writing [reusable apps](SelfContainedApps.md).

Just put your app-specific media files in a folder called "media" within your app's folder and global media files within a "media" folder in your project root which is faked as the "global" app (e.g.: "global/jquery.js"). Note that the JS translations catalog can be automatically integrated into the generated JS files (see below).

On the dev\_appserver the media files get generated on-the-fly by a special view, so you can change your code and the combined media files will automatically be updated. For efficiency, when you upload your website to the production server ("manage.py update") the generated media files get compressed and stored as static files in the "_generated\_media" folder and served from there._

# Serving images and non-JS/CSS data #

Anything that is neither a JS nor CSS file (e.g., an image) is served directly while JS and CSS files must be combined in order to be accessible. The URL to your image, for example, becomes "`<MEDIA_URL><appname>/<filename>`". For example, if you have a file in "myapp/media/image.png" it gets served as `"<MEDIA_URL>myapp/image.png"` (note the missing "media/").

# `url()` handling in CSS files #

The media generator rewrites relative `url()` paths such that they're relative to the source file. For example, if you have a file
```
/* blueprintcss/grid.css */
.someclass { background-image: url(image.png); }
```

then the image URL will be converted to "blueprintcss/image.png".

If you want to reach a path in some other app you can use '../' to get to the parent directory. For instance, if our 'blueprintcss/grid.css' file wanted to access 'themes/image.png' you can use `url(../themes/image.png)`.

Finally, you can also get MEDIA\_URL directly with `url($MEDIA_URL/themes/image.png)`. Absolute URLs are **bad design**, but sometimes (e.g., for IE htc `behavior` rules) you have to specify an absolute URL because the browser doesn't understand relative URLs.

# Integrating the media files #

Since the generated media files are language-specific you have to integrate them with this code in your base.html template:

```
<link rel="stylesheet" type="text/css" href="{{ MEDIA_URL }}combined-{% if LANGUAGE_BIDI %}rtl{% else %}ltr{% endif %}.css" />
<script type="text/javascript" src="{{ MEDIA_URL }}combined-{{ LANGUAGE_CODE }}.js"></script>
```

# Adding app-specific media #

With [self-contained apps](SelfContainedApps.md) you can always add certain media files:

```
# myapp/settings.py:

from ragendja.settings_post import settings

settings.add_app_media('combined-%(LANGUAGE_CODE)s.js',
    'myapp/bla.js',
    'myapp/blub.js',
    # ...
)
settings.add_app_media('combined-%(LANGUAGE_DIR)s.css',
    'myapp/style.css',
    # ...
)
```

# Getting the MEDIA\_URL from JavaScript #

You can integrate a special file called ".site\_data.js" which provides access to the MEDIA\_URL:

```
COMBINE_MEDIA = {
    'combined-%(LANGUAGE_CODE)s.js': (
        '.site_data.js',
        ...
    ),
}
```

In your JS code you can then retrieve the MEDIA\_URL like this:

```
var url = site_data.settings.MEDIA_URL + 'global/image.png';
```

# i18n #

You might know about Django's [JavaScript translation support](http://docs.djangoproject.com/en/dev/topics/i18n/#translations-and-javascript) which works with AJAX. Since the primary goal of the media generator is to reduce the number of HTTP requests it gets rid of those AJAX requests. Instead, the generated media files automatically contain all JS translations of all installed apps, so you don't need to extend your urls.py and you also don't need to add a new `<script>` tag to load the catalog. You can directly use `gettext()`, `ngettext()`, and `interpolate()`.

# Custom converters #

Instead of specifying a path to a source file you can use a function which returns a source code snippet:

```
def myconverter(**kwargs):
    return 'body { margin: 0; }\n'

COMBINE_MEDIA = {
    'combined-%(LANGUAGE_DIR)s.css': (
        'myapp/style.css',
        myconverter,
    ),
}
```
# Module: ragendja.template #

## app\_prefixed\_loader ##

The template loader `app_prefixed_loader` allows for DRY separation of app
templates. For example, you can render the template "`<app>/templates/bla.html`" by writing `render_to_response(request, '<app>/bla.html')`. There's no need for prefixing app templates ("`<app>_bla.html`") or creating subfolders ("`<app>/templates/<app>/bla.html`"). For maximum compatibility you should use the following order in your `settings.py`:
```
TEMPLATE_LOADERS = (
    'django.template.loaders.filesystem.load_template_source',
    'ragendja.template.app_prefixed_loader',
    'django.template.loaders.app_directories.load_template_source',
)
```

## render\_to\_response(request, template, data, mimetype) ##

This is an improved `render_to_response()` that uses `RequestContext` in order to support context processors. The `data` and `mimetype` arguments are optional.

## render\_to\_string(request, template, data) ##

This is an improved `render_to_string()` that uses `RequestContext` in order to support context processors. The `data` argument is optional.

## JSONResponse(data) ##

This converts a Python object (`data`) into a JSON string and creates an `HttpResponse` with the correct MIME type.

## TextResponse(data) ##

This returns a simple `text/plain` response with the given text.


# Module: ragendja.templatetags.ragendjatags #

## register ##

This is a context\_processor that adds an `encodejs` filter that converts Python to a JSON code string.


# Module: ragendja.middleware #

## NoHistoryCacheMiddleware ##

The `NoHistoryCacheMiddleware` disables the browser's history cache for logged in users. When the user clicks the "back" button on his browser the page will get fully reloaded. Otherwise, when clicking "back", pages that were modified via JavaScript would be shown in the state before the modifications got applied.
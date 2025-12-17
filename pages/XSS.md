# Cross Site Scripting (XSS)

> Cross-Site Scripting (XSS) attacks are a type of injection,
> in which malicious scripts are injected into otherwise benign
> and trusted websites.

— [OWASP](https://owasp.org/www-community/attacks/xss/)

## Don't trust data attributes

Modules often use data attributes to pass data
from the backend to the frontend:

```php
return Link::fromTextAndUrl(
  t('Click to display description'),
  Url::fromUserInput('#', ['attributes' => ['data-description' => 'My description']]),
)->toRenderable();
```

```javascript
// Then use this data in JS (for example on click).
$('.description').html($(this).data('description'));
```

You should assume that users who can insert HTML on the site
are able to craft data attributes:

```html
<a data-description="&lt;img src=x onerror=alert()&gt;">Click me</a>
```

CKEditor does not allow data attributes by default
but contrib modules provide other ways to insert HTML
and those can allow data attributes
(because they often use `Xss::filter()` and
this method allows data attributes).

The solution depends on what the data attributes
is supposed to contain:

* Plain text : sanitize it with `Drupal.checkPlain()`
* HTML : sanitize it with a library like [DOMPurify](https://github.com/cure53/DOMPurify)
* URL : make sure it starts with `http:` or `https:`
  (to avoid dangerous URLs like `javascript:alert()`)

## JS libraries don't always sanitize inputs

When passing user input to a JS library,
you should check if the library sanitizes it.
If not, it is your responsibility to sanitize it
before passing it to the library:

```javascript
// My lighbox library does not sanitize the title.
$("a.gallery").lightbox({title: Drupal.checkPlain(myTitle)});
```

It is common for JS libraries to consider that
sanitization of inputs is not part of their security scope.

## Don't use `Markup::create()` on user input

When using `#markup` in a render array,
it only allows a specific list of safe HTML tags:

```php
return [
  // #markup will not allow the video tag.
  '#markup' => '<video src="video.webm"></video>',
];
```

A common way to bypass this is to use a MarkupInterface object:

```php
return [
  // Don't do this on user input.
  '#markup' => Markup::create('<video src="video.webm"></video>'),
];
```

However, this bypasses XSS filtering entirely
so it should never be used on user input.
Instead, you should use `#allowed_tags`:

```php
return [
  '#markup' => $userInput,
  '#allowed_tags' => ['video', 'div', 'span'],
];
```

# Twig

Twig is very good at preventing XSS attacks
because it escapes variables by default.
However, there are a few bad practices that should be avoided.

## Never use `|raw` in templates

The [`|raw` filter](https://twig.symfony.com/doc/3.x/filters/raw.html)
completely bypasses Twig auto-escaping.
It is often used to display a variable containing HTML:

```php
return [
  '#theme' => 'banana',
  '#title' => '<h1>Banana</h1>',
];
```

```twig
{{ title|raw }}
```

This is bad because if another module uses this template,
they might not notice the `|raw` and could
pass a `title` variable that contains some user input.

Using `|raw` in almost never needed in Drupal.
The correct Drupal way to do this is to use a `MarkupInterface`
object:

```php
return [
  '#theme' => 'banana',
  '#title' => Markup::create('<h1>Banana</h1>'),
];
```

```twig
{{ title }}
```

This way, code calling the template has to explicitly
tell that the HTML is safe by wrapping it in a `Markup` object.

Of course if the variable contains user input,
it should be filtered first:

```php
return [
  '#theme' => 'banana',
  '#title' => Markup::create(Xss::filter($titleSubmittedByUser)),
];
```

## Only use `is_safe` if you are sure your output is safe

If you write a Twig filter or function that returns HTML,
you often need to use the `is_safe` option:

```php
$filter = new TwigFilter('banana', 'banana', ['is_safe' => ['html']]);
```

However, this disables auto-escaping so you should only do this
if you are really sure that the output is safe.

For example if you have a filter that does this:

```php
function banana($context, $string) {
    return str_replace('banana', '<strong>banana</banana>', $string);
}
```

It is vulnerable to XSS if used on some user-provided value:

```twig
{# Dangerous user-provided value #}
{% set payload = '<script>alert(`XSS`);</script>' %}

{# Here it is correctly escaped #}
{{ payload }}

{# Here it is not #}
{{ payload|banana }}
```

If the filter does not expect HTML in its input,
you can fix this with the `pre_escape` option:

```php
$filter = new TwigFilter(
  'banana', 'banana', ['pre_escape' => 'html', 'is_safe' => ['html']]
);
```

But if you need to accept HTML in the input,
you should filter the output:

```php
function banana($context, $string) {
    return Xss::filterAdmin(
      str_replace('banana', '<strong>banana</banana>', $string)
    );
}
```

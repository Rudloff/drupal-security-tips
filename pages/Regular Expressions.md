# Regulars Expressions

## Only use regular expressions if you have to

Regular expressions are powerful but when using them
to filter untrusted input
(parse a URL to see if it contains a specific domain,
parse HTML to make sure it does not contain anything dangerous, etc.),
it is hard to do it in a way that can't be bypassed by
a specifically crafted payload.

If a parser is available (either natively in PHP or as a Composer package)
for the thing you are trying to parse, you should usually
use that instead of a regular expression.

## Regular expression Denial of Service (ReDoS)

Regular expressions can be vulnerable to [ReDoS attacks](https://owasp.org/www-community/attacks/Regular_expression_Denial_of_Service_-_ReDoS)
if they are used with some user input.
PHP has backtracking and recursion limits so most ReDoS attacks
can't overwhelm the server CPU, but they can still be used
to make a page unavailable.

For example if you do something like this:

```php
// Replace something in the entire HTML response.
$html = preg_replace('/apple(.*):/', 'banana$1:', $html);
```

An attacker that can insert HTML in the page (for example in a comment)
could craft a specific payload that makes the regular expression
exceed PHP's backtracking limit and `preg_replace()` will
return `NULL` so the whole page will become blank.

The solution to this is to always check if PCRE functions
return `NULL`:

```php
$newHtml = preg_replace('/apple(.*):/', 'banana$1:', $html);
if (!is_null($newHtml)) {
  $html = $newHtml;
}
```

You can also use [this ReDoS checker](https://devina.io/redos-checker)
to see if your regular expressions are vulnerable.

# Cross Site Request Forgery (CSRF)

> Cross-Site Request Forgery (CSRF) is an attack that forces
> an end user to execute unwanted actions on a web application
> in which they’re currently authenticated.
> With a little help of social engineering
> (such as sending a link via email or chat),
> an attacker may trick the users of a web application
> into executing actions of the attacker’s choosing.

— [OWASP](https://owasp.org/www-community/attacks/csrf)

## Routes that manipulate data without a confirmation should be protected against CSRF

If a route creates/updates/deletes content or configuration
without a confirmation, it should be protected against CSRF.

See for example this route:

```yaml
bananas.delete:
  path: '/banana/{banana_id}/delete'
  defaults:
    _controller: '\Drupal\bananas\Controller\BananaController::delete'
  requirements:
    _permission: 'administer bananas'
```

An attacker without the "administer bananas" permission
but who can create nodes on the website can create a page
containing this HTML:

```html
<img src="https://example.com/banana/42/delete" alt="This will delete banana 42">
```

Then, if a victim with the "administer bananas" permission
browses to this page, it will trigger a GET request to this route
and the action will be executed without any confirmation.

The easiest solution is to use
[the `_csrf_token` requirement](https://www.drupal.org/docs/8/api/routing-system/access-checking-on-routes/csrf-access-checking#s-the-csrf-token-requirement):

```yaml
bananas.delete:
  path: '/banana/{banana_id}/delete'
  defaults:
    _controller: '\Drupal\bananas\Controller\BananaController::delete'
  requirements:
    # Be careful: the value is a string, not a boolean.
    _csrf_token: 'TRUE'
```

Routes that return a form
[are already protected against CSRF](https://www.drupal.org/docs/8/api/routing-system/access-checking-on-routes/csrf-access-checking#s-forms)
and don't need `_csrf_token`.

## Attackers don't need to be able to publish content to your website

Although harder to execute, CSRF attacks are possible
even if the attacker can't create content on the website.

For example an attacker can host a webpage containing this script:

```html
<!-- Very basic CSRF attack -->
<script>
    window.location = 'https://example.com/banana/42/delete';
</script>
```

Then trick a victim with the "administer bananas" permission
to open it.

So you should assume that CSRF attacks are always possible.

## Non-GET routes also need to be protected

[By default, Drupal uses `SameSite=Lax` cookies](https://www.drupal.org/node/3275352),
this prevents CSRF attacks on POST/PUT/DELETE routes.
However, you should assume that some of the websites using your module
could be using `SameSite=None` cookies.

Form routes are automatically protected but if you have
other non-GET routes (for example REST API routes),
you can use
[`_csrf_request_header_token`](https://www.drupal.org/docs/8/api/routing-system/access-checking-on-routes/csrf-access-checking#s-the-csrf-request-header-token-requirement)
to protect these routes.
(This requirement does not protect GET requests.
When using it, the route should not support the GET method.)

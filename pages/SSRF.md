# Server Side Request Forgery (SSRF)

> In a Server-Side Request Forgery (SSRF) attack,
> the attacker can abuse functionality on the server
> to read or update internal resources.

— [OWASP](https://owasp.org/www-community/attacks/Server_Side_Request_Forgery)

## Restrict external queries to a list of allowed domains

If your module allows users to trigger external HTTP queries,
it could be used for SSRF attacks.

For example if you have code like this:

```php
// The URL is provided by the user.
return \Drupal::httpClient()->get($url);
```

An attacker could use URLs like <http://10.123.45.67/>
to gain information about resources that are
on the same private network as your server
but are not publicly accessible.

The easiest way to avoid this is to allow admins to configure
a list of allowed domains.

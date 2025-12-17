# Authentication

## The login form is not the only way to login

If your module adds a protection to the login process
(for example two-factor authentication),
it should not assume that the login form is the only way to login.

In particular core provides a REST login route
that allows login without a form:

```shell
curl --header "Content-type: application/json" --request POST --data '{"name":"foo", "pass":"bar"}' 'https://example.com/user/login?_format=json'
```

If you only protect the login form and not the REST login,
it means your protection can be bypassed on most websites.

Other methods like [basic authentication](https://www.drupal.org/docs/8/core/modules/basic_auth/overview)
could be enabled.
You can't support every contrib authentication method
so a common solution is to add a warning to the status report:

```php
/**
 * Implements hook_requirements().
 */
function bananas_requirements(): array {
  $providers = \Drupal::service('authentication_collector')
    ->getSortedProviders();
  unset($providers['cookie']);
  if (count($providers) > 0) {
    $requirements['bananas_auth'] = [
      'title' => t('Unsupported authentication providers'),
      'severity' => REQUIREMENT_WARNING,
      'description' => t(
        'Additional authentication providers are enabled and they are not protected.'
      ),
    ];
  }
  return $requirements;
}

```

## Bruteforce

If your module provides a feature that requires submitting a secret
(a password, an access key, etc.),
you should protect it against bruteforce attacks.

You can use the flood service for this:

```php
if (\Drupal::flood()->isAllowed('bananas', 5)) {
  // User is allowed to login.
}
```

```php
// Register a failed login attempt.
\Drupal::flood()->register('bananas');
```

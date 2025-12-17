# Access Checks

## Avoid `_access: 'TRUE'` on routes

Even if you assume anyone should be able to access your route,
some websites could have different requirements
(for example some websites require an account to access any route).

Using `_access: 'TRUE'` makes it harder for site builders
to restrict access to this route if they need to.

For routes that return entities you should
check entity access (see below).
For other routes you should usually use a permission:

```yaml
bananas:
  path: '/bananas/help'
  defaults:
    _controller: '\Drupal\bananas\Controller\BananaController::help'
  requirements:
    _permission: 'access bananas'
```

## Avoid views without any access check

Similarly, you should avoid views without any access condition.
Even if the view query uses access checks,
site builders could want to hide the view entirely for some users.

Adding a permission condition the view is a common solution:

```yaml
      access:
        type: perm
        options:
          perm: 'access content'
```

## Check entity access

Routes that allow viewing, updating or deleting an entity
should not do this:

```yaml
entity.banana.edit_form:
  path: '/banana/{banana_id}/edit'
  defaults:
    _entity_form: 'banana.edit'
  requirements:
    _permission: 'administer bananas'
```

The problem with this approach is that it bypasses hook_access:

```php
/**
 * Implements hook_ENTITY_TYPE_access().
 */
function bananas_banana_access() {
  return AccessResult::forbidden('No one should edit this banana.');
}
```

You should instead use the `_entity_access` requirement:

```yaml
entity.banana.edit_form:
  path: '/banana/{banana_id}/edit'
  defaults:
    _entity_form: 'banana.edit'
  requirements:
    _entity_access: 'banana.update'
```

Or `_entity_create_access` for create forms:

```yaml
entity.banana.add_form:
  path: '/banana/add'
  defaults:
    _entity_form: 'banana.add'
  requirements:
    _entity_create_access: 'banana'
```

## `#access` is not always a boolean

Your code should not assume that `#access` is always a boolean:

```php
if (!isset($build['banana']['#access']) || $build['banana']['#access']) {
  // Do something.
}
```

`#access` can also be an `AccessResultInterface` object:

```php
// Elements are accessible by default.
if (!isset($element['#access'])) {
  return TRUE;
}

// Check for #access as an AccessResultInterface object.
if ($element['#access'] instanceof AccessResultInterface) {
  return $element['#access']->isAllowed();
}

// Otherwise, #access must be a boolean.
return $element['#access'] === TRUE;
```

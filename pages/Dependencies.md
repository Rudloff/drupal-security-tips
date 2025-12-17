# Dependencies

## Don't include dependencies in your repository

You should never include a third-party library directly in your module.
If you do this and a vulnerability is found in this library,
users can't easily patch it without a new release of your module.

Instead, PHP libraries should be required with Composer
and JS libraries should be loaded from `/web/libraries`.

## Use versions constraints that let your users apply security patches

You should not require a specific version of dependencies:

```json
"html2text/html2text": "4.3.1",
```

If a new security fix is published for this dependency,
your users will not be able to update.

Instead, you should require a version range:

```json
"html2text/html2text": "^4.3",
```

## Avoid duplicating dependency code

It can sometimes be useful to duplicate a class
from core or a library to tweak it to your needs.
But if a vulnerability is found in the original code,
it becomes your responsibility to also fix in the duplicated code.

If you want to alter the behavior of code you don't control,
you should try to use things like hooks, event subscribers
or decorated services instead.

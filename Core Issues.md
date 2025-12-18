# Core Issues

This is a list of core issues I am working on (or planning to)
to try to prevent some of the security problems
listed in this repository.

## XSS

* [Prevent XSS in data attributes](https://www.drupal.org/project/drupal/issues/3525027)
* [Add protocol filtering to the core Attribute component](https://www.drupal.org/project/drupal/issues/2567743)
* [Allow Xss::filter() to restrict allowed attributes](https://www.drupal.org/project/drupal/issues/3525103)
* [Adopt Symfony's HTML Sanitizer](https://www.drupal.org/project/drupal/issues/3413706)

### SVG

* [Filter SVGs before uploading by default](https://www.drupal.org/project/drupal/issues/3494583)
* [Add a default Content-Security-Policy-header for svg files](https://www.drupal.org/project/drupal/issues/2868079)
* [Check that the CSP header is added to SVG files](https://www.drupal.org/project/drupal/issues/3525167)

## CSRF

* [Make it harder to have routes vulnerable to CSRF](https://www.drupal.org/project/drupal/issues/3508087)

## Access Checks

* [Add an easier way to alter #access](https://www.drupal.org/project/drupal/issues/3550082)
* [Deprecate use of "#access" as anything other than an allowed boolean or an AccessResultInterface object](https://www.drupal.org/project/drupal/issues/3526250)

## Authentication

* [Disable the user.login.http route by default and move to REST](https://www.drupal.org/project/drupal/issues/3530640)
* [Make it easier to protect forms against brute force](https://www.drupal.org/project/drupal/issues/3532183)

## Permissions

* [Make module developers think about restricted permissions](https://www.drupal.org/project/drupal/issues/3532783)
* [Don't overridde default security warning when providing custom warning on a permission](https://www.drupal.org/node/3549383)

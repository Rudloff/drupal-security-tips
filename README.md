# Security tips for Drupal module maintainers

This repository contains tips for preventing
common vulnerabilities in Drupal modules.
It is based on the analysis of 2 years of
[security advisories](https://www.drupal.org/security/contrib)
and patches.

## Table of contents

* [XSS](./pages/XSS.md)
  * [SVG](./pages/SVG.md)
  * [Twig](./pages/Twig.md)
* [CSRF](./pages/CSRF.md)
* [Access Checks](./pages/Access%20Checks.md)
* [Authentication](./pages/Authentication.md)
* [Dependencies](./pages/Dependencies.md)
* [Permissions](./pages/Permissions.md)
* [Regular Expressions](./pages/Regular%20Expressions.md)
* [SSRF](./pages/SSRF.md)

## Reporting a vulnerability

If you find a security issue in a Drupal module you should
[report it to the security team](https://www.drupal.org/docs/develop/issues/issue-procedures-and-etiquette/reporting-a-security-issue).

## Contributing

Instead of contributing to this repository,
I think the goal should be to integrate this
into the drupal.org documentation.

## License

This work is licensed under [CC BY-SA 2.0](https://creativecommons.org/licenses/by-sa/2.0/).

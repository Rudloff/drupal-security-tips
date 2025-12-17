# Permissions

## Restrict dangerous permissions

Any permission that allows taking over the site
(inserting arbitrary JS, uploading PHP files, editing user roles, etc.)
or gaining access to an account with more permissions should be restricted:

```yaml
insert javascript:
  title: 'Insert custom JavaScript into pages'
  restrict access: true
```

This displays a warning on the permissions form
so that admins can now this permission is dangerous
and should only be given to trusted users.

Try to keep the scope of restricted permissions small.
If only a specific feature of your module is dangerous,
add a permission specific to this feature with `restrict access: true`.
This lets admins allow some semi-trusted users to use your module
without the dangerous feature.

It is not always obvious why a permission has been restricted.
Explaining in your documentation why it can be dangerous
helps admins make a better decision about which roles to give
this permission to.

## External redirects are dangerous

If your module allows creating HTTP redirects to external URLs,
it should require a restricted permission to do this.

Open redirect attacks can be used for phishing
or to make SSRF attacks easier.

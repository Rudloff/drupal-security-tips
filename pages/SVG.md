# SVG

SVG files are a common vector for XSS attacks
because they can contain JavaScript.

Here is a very basic example of a SVG file containing a script:

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 500 500">
    <script>//<![CDATA[
        alert(document.domain)
    //]]>
    </script>
</svg>
```

## Don't allow untrusted users to upload SVG files

While it is somewhat common to let untrusted users upload images,
you should not allow them to upload SVG files
unless you are sure you are sanitizing them correctly.

## Sanitize SVG files

If you serve user-uploaded SVG files,
you should sanitize them with a library
like [svg-sanitizer](https://github.com/darylldoyle/svg-sanitizer).

You should avoid sanitizing on upload and instead sanitize the SVG
when it is displayed.
New ways to exploit SVG files could be discovered
and updating the library should retroactively protect every
SVG file already uploaded.

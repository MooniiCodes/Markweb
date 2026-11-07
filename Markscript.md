:::page title="MarkScript Test"
:::style bg="#10161d" fg="#e8f0ff" accent="#66d9ff" link="#8ad4ff" quote="#9fb3c8" codebg="#18222c" bar="#0c1218"

# MarkScript Test

This page tests MarkWeb's simple **MarkScript** language.

```markscript
title("MarkScript Test Page")
status("MarkScript ran on page load!")
accent("#66d9ff")
name = "MarkWeb User"
music("on")
volume(45)
if (get("name")) { print("Hello from MarkScript") }
```

## Buttons

[$# Current page](sdmc:/Aurora/Apps/C/MarkWeb/test-markscript.md) [$Open Home](markweb:homepage)

## Links

- [Normal link](markweb:homepage)
- [Site alias example](site://example.com)
- [@Download this page](sdmc:/Aurora/Apps/C/MarkWeb/test-markscript.md)

## Style block

:::markscript
linkcolor("#ffd36b")
:::endmarkscript

If links look gold/yellow, `linkcolor()` worked.

## Quick action

:::markscript run="status(\"Quick action button/script ran\")"

## HTML MarkScript block

<markscript>
bg("#10161d")
fg("#e8f0ff")
</markscript>

## Notes

- MarkScript runs once when the page loads.
- `fetch("url")` is blocking and stores text in `_fetch`.
- `download("url", "name")` saves to `Aurora/Apps/C/MarkWeb/Downloads/`.
- `open("url")` navigates to another page.

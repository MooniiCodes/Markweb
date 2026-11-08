:::page title="MarkScript Test"
:::style bg="#10161d" fg="#e8f0ff" accent="#66d9ff" link="#8ad4ff" quote="#9fb3c8" codebg="#18222c" bar="#0c1218"

# MarkScript Test

This page tests MarkWeb's **MarkScript**. MarkScript runs only inside executable `:::markscript` blocks. Fenced ` ```markscript` blocks are shown as code and do not run.

:::id statusline
Ready: waiting for MarkScript.

## On-load script

:::markscript
on_load {
  title("MarkScript Test Page")
  status("MarkScript ran on page load")
  accent("#66d9ff")
  linkcolor("#ffd36b")
  name = "MarkWeb User"
  greeting = "Hello, " + get("name")  // simple vars; use get(name) in text
  set_text(statusline, "MarkScript on_load ran. Use A while cursor mode is on to test click().")
  music("on")
  volume(45)
}
:::

## Buttons

[$# Current page](sdmc:/Aurora/Apps/C/MarkWeb/test-markscript.md) [$Open Home](markweb:homepage)

## Input and cursor hooks

:::markscript
on_key {
  if (key_down("A")) { status("A pressed (MarkScript saw it)") }
  if (key_down("X")) { status("X pressed; virtual reload action available") }
  if (key_down("START")) { cursor_mode("on"); status("Cursor mode requested") }
}
on_touch {
  status("Touch/tap detected by MarkScript")
}
on_tick {
  // Keep lightweight on 3DS. This is a demo; heavy tick logic may slow rendering.
}
:::

## Page info

:::markscript
on_load {
  page_url = get("url")
  page_title = get("title")
  status("Page: " + page_title)
}
:::

- [Normal link](markweb:homepage)
- [Site alias example](site://homepage.md)
- [@Download this page](sdmc:/Aurora/Apps/C/MarkWeb/test-markscript.md)

## Virtual actions (inside MarkWeb only)

:::markscript run="status(\"Quick virtual action: moving cursor\")"
:::

Examples you can try in a script block:
- `press("A")`, `press("START")`, `press("UP")`, `press("B")`
- `cursor(160,100,"bottom")` or `click(160,100)`
- `tap(40,200)` (bottom screen)
- `scroll(40,"bottom")` / `scroll_to(0,"top")`
- `open("site://homepage.md")`, `reload()`, `home()`, `settings()`
- `download("https://example.com/file.txt","file.txt")`
- `fetch("site://homepage.md")` then use `_fetch`

## Element properties

:::id democolor
This line has id `democolor`. Scripts can change it with `set_text`, `set_color`, `hide`, or `show`.

:::markscript
on_load {
  set_color("democolor", "#7CFFB2")
}
:::

## Notes

- MarkScript runs in MarkWeb only; it cannot inject OS/HID input into other apps.
- `:::markscript` blocks execute; ` ```markscript` fences do not.
- `fetch(...)` is blocking and stores text in `_fetch`.
- `save(name,value)` / `load(name)` use `SD:/Aurora/Apps/C/MarkWeb/ms_<name>.txt`.
- `time()` currently returns `0` in this build (placeholder). Keep scripts simple and bounded.

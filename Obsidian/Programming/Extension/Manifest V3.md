
#### Starting
Start by creating a new directory to store your extension files. 

> manifest.json

```json
{
  "name": "Hello Extensions",
  "description": "Base Level Extension",
  "version": "1.0",
  "manifest_version": 3,
  "action": {
    "default_popup": "hello.html",
    "default_icon": "hello_extensions.png"
  }
}
```

### When to reload the extension

The following table shows which components need to be reloaded to see changes:

| Extension component        | Requires extension reload |
| -------------------------- | ------------------------- |
| The manifest               | Yes                       |
| Service worker             | Yes                       |
| Content scripts            | Yes (plus the host page)  |
| The popup                  | No                        |
| Options page               | No                        |
| Other extension HTML pages | No                        |
|                            |                           |
|                            |                           |
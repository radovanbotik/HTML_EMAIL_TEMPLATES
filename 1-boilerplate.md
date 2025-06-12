# META TAGS

```html
<html lang="en" dir="ltr" xmlns:v="urn:schemas-microsoft-com:vml" xmlns:o="urn:schemas-microsoft-com:office:office">
```

---

### `lang="en"`

* **What it does:** Specifies the primary language of the document content (here, "en" = English).
* **Importance:**

  * Makes the content accessible for screen readers.
  * Guides spell-checkers/dictionaries in email clients.
  * Helps translation services identify the source language.

---

### `dir="ltr"`

* **What it does:** Sets text direction to left-to-right (LTR).
* **Importance:**

  * Prevents unexpected rendering issues.
  * Essential for correct display in languages written LTR.

---

### `xmlns:v="urn:schemas-microsoft-com:vml"`

* **What it does:** Declares the VML XML namespace (used by Microsoft).
* **Importance:**

  * Used mainly for legacy Outlook support for VML graphics/backgrounds.

---

### `xmlns:o="urn:schemas-microsoft-com:office:office"`

* **What it does:** Declares the Microsoft Office XML namespace.
* **Importance:**

  * Allows embedding Office-specific formatting/metadata in HTML.

---

```html
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
```

* Ensures the email displays and encodes characters correctly.

#### `http-equiv="Content-Type"`

* Simulates HTTP headers for document parsing and display.

#### `content="text/html; charset=utf-8"`

* Declares the document as HTML and sets UTF-8 encoding.
* Prevents "garbled" characters, supports all languages and special characters.

---

```html
<meta http-equiv="X-UA-Compatible" content="IE=edge" />
```

* Improves rendering in Microsoft Outlook by simulating modern IE engine.

#### `content="IE=edge"`

* Instructs Outlook to use the highest possible rendering mode.
* Avoids "quirks mode" and outdated rendering behaviors.

---

```html
<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=yes" />
```

* Makes emails responsive and readable on mobile devices.

#### Key attributes:

* **`width=device-width`**: Uses the device's actual width.
* **`initial-scale=1`**: No zoom on load.
* **`user-scalable=yes`**: Allows pinch-to-zoom for accessibility.

---

```html
<meta name="format-detection" content="telephone=no, date=no, address=no, email=no, url=no" />
```

* Prevents iOS and some mobile clients from auto-linking and restyling certain content.

#### Individual controls:

* **`telephone=no`**: Prevents phone numbers from becoming links.
* **`date=no`**: Prevents dates from auto-linking to calendars.
* **`address=no`**: Stops addresses from linking to maps.
* **`email=no`**: Prevents emails from auto-linking to mailto.
* **`url=no`**: Prevents plain URLs from turning into links.

---

```html
<meta name="x-apple-disable-message-reformatting" />
```

* Stops Apple Mail/iOS Mail from reformatting or changing font sizes/layouts.
* **Non-standard**, but useful for maintaining intended designs.

---

```html
<meta name="color-scheme" content="light dark" />
```

* Signals dark mode and light mode support to clients that recognize it.
* Lets you use CSS media queries for prefers-color-scheme.

---

```html
<meta name="supported-color-schemes" content="light dark" />
```

* Works alongside the `color-scheme` meta for the widest dark mode support.

---

```html
<title>HTML Email Template Project</title>
```

* Sets the document title (seen in browser tabs, bookmarks, and sometimes as preview text).
* **Accessibility**: Helps screen readers, provides context.

---

```html
<!--[if mso]>
  <noscript>
    <xml>
      <o:OfficeDocumentSettings>
        <o:PixelsPerInch>96</o:PixelsPerInch>
      </o:OfficeDocumentSettings>
    </xml>
  </noscript>
<![endif]-->
```

* **Conditional MSO block**: Stops image scaling and maintains layout in Outlook (uses 96 DPI).

---

## MSO Conditional Comments

* Proprietary Microsoft syntax to target Outlook/IE only.
* Ensures specific styles/settings are applied only in these clients.

---

# CSS RESETS

```html
<style type="text/css"></style>
```

* Internal CSS for max compatibility in email clients.

---

### TABLE

```css
table {
  border-spacing: 0;
  border-collapse: collapse;
}
```

* Removes unwanted gaps and double borders in table-based layouts.

---

### TD

```css
td {
  padding: 0;
}
```

* Resets default padding for consistent layout across clients.

---

### IMG

```css
img {
  border: 0;
}
```

* Removes default borders from images (especially linked images).

---

### Gmail Dark Mode Fixes

```css
u + .body .gmail-blend-screen {
  mix-blend-mode: screen;
  background: #000;
}

u + .body .gmail-blend-difference {
  mix-blend-mode: difference;
  background: #000;
}
```

* Counteracts Gmail's dark mode inversion for images/logos.

---

### Outlook.com Wrapper

```css
.ExternalClass {
  width: 100%;
}

.ExternalClass,
.ExternalClass p,
.ExternalClass td,
.ExternalClass div,
.ExternalClass span,
.ExternalClass font {
  line-height: 100%;
}
```

* Forces 100% width and normalizes line height to prevent extra spacing in Outlook.com and legacy Outlook.

---

### Apple Data Detectors

```css
a[x-apple-data-detectors="true"] {
  color: inherit !important;
  text-decoration: none !important;
}
```

* Removes Apple’s blue auto-linked styles.

---

# MEDIA QUERIES

```css
@media screen and (max-width: 599.98px) { ... }
@media screen and (max-width: 499.98px) { ... }
@media screen and (max-width: 399.98px) { ... }
```

* Responsive design: adjust layout at specific breakpoints.
* Using `.98` ensures better separation across device widths.

---

# DARK MODE

### `:root`

```css
:root {
  color-scheme: light dark;
  supported-color-schemes: light dark;
}
```

* Signals dark mode support for the widest range of clients.

---

### `@media (prefers-color-scheme: dark) { ... }`

* Applies styles **only** when the system/email client is set to dark mode.

---

### `[data-ogsc]`

* Targets Gmail web’s internal dark mode system for further customization.

---

# MSO CSS RESETS

```html
<!--[if mso]>
  <style type="text/css">
    body { background-color: #dde0e1 !important; }
    body, table, td, p, a {
      font-family: "Lato", Arial, Helvetica, sans-serif !important;
    }
    table {
      border-spacing: 0 !important;
      border-collapse: collapse !important;
    }
  </style>
<![endif]-->
```

* Forces Outlook to use specific background, font, and table settings for best consistency.

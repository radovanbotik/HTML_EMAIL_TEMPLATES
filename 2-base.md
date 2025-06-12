# Email Template Structure & CSS Style Notes

---

## 1. `body` Tag

### `.body` Class

* Assigns a CSS class to the `<body>`.
* Provides a reliable hook for CSS rules, especially for client-specific hacks (e.g., `u + .body` or `[data-ogsc] body` for Gmail).

### `xml:lang="en"`

* Declares the document language as English for XML parsers.
* Ensures compatibility with older email clients or those parsing HTML as XHTML (e.g., some versions of Outlook).
* Complements `lang="en"` on `<html>` (HTML5 standard).

### Inline Style

```html
style="margin: 0; padding: 0; min-width: 100%;"
```

* `margin: 0;` & `padding: 0;`: **CSS resets**—removes default spacing applied by browsers/email clients, giving you pixel-perfect control.
* `min-width: 100%;`: Ensures the `<body>` always takes up at least the full viewport width, aiding mobile responsiveness and preventing unwanted margins (especially on older Android clients).

---

## 2. The Outer `<div>`: Full-Width Wrapper

* Serves as the outermost container, stretching across the entire email client window.

#### Key Properties

* `width: 100%;`: Spans the full width of the parent (`<body>`).
* `table-layout: fixed;`: Though normally for tables, used here as a hack for Outlook to stabilize column widths and prevent rendering issues.

---

## 3. The Inner `<div>`: Centered Content Wrapper

* The core container for main email content.
* Ensures content is centered and respects a max width.

#### Key Properties

* `max-width: 600px;`: **Essential for desktop layouts**—prevents the email from stretching too wide.
* `margin: 0 auto;`: Centers the content block horizontally.
* `box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);`: Adds a modern, subtle "card" effect. (Progressive enhancement: supported by most modern clients, but ignored by some like older Outlook.)

---

## 4. The Hidden Preheader Text `<div>`

* Used for preheader text (the inbox preview snippet).
* Visible in the inbox preview, hidden in the opened email.

#### Key Properties

* `font-size: 0px; line-height: 0px;`: Collapses text visually.
* `color: #fafdfe;`: Matches the background, making text invisible.
* `mso-line-height-rule: exactly;`: Outlook-specific, enforces the line height.
* `display: none;`: Hides the element in most clients (not all respect this).
* `max-width: 0px; max-height: 0px;`: Physically collapses the element.
* `opacity: 0;`: Makes it fully transparent.
* `overflow: hidden;`: Hides any overflowing content.
* `mso-hide: all;`: Outlook-specific, ensures the element is hidden.

#### Special Characters

* `&zwnj;&nbsp;&#847;...`: Zero-width non-joiners and non-breaking spaces pad the preheader to prevent unwanted content from appearing in the preview.
* **Recommended length:** 85–100 characters.

---

## 5. The Main Content Table (Outlook Conditional Comments & Regular Table)

* The core structural component.
* Uses "Ghost Table" or "Hybrid Coding" for maximum compatibility—especially with Outlook.

### Outer MSO Conditional Comment Table

* **Purpose:** Ensures correct rendering in Outlook.

#### Key Properties

* `width="600"`: Forces Outlook to use exactly 600px width.
* `align="center"`: Horizontally centers the table.
* `style="border-spacing: 0; border-collapse:collapse;"`: Outlook-specific resets.
* `role="presentation"`: ARIA attribute for accessibility—marks table as layout-only, not data.

### Inner Standard Table

* Main structure rendered by most clients.

#### Key Properties

* `border-spacing: 0; border-collapse: collapse;`: Essential resets for consistent layout.
* `width: 100%; max-width: 600px;`: Fluid and responsive layout.
* `margin: 0 auto; padding: 0;`: Centers and removes default spacing.
* `role="presentation"`: Accessibility improvement.


Here’s the same guide with a clear call-out: **your actual content must go inside the `<v:textbox>`** so Outlook layers it on top of your background.

---

# How to Use Background Images in HTML Emails

_Full-width background images that work everywhere—including Outlook—with a responsive fallback._

## Table of Contents

1. Final Code Example
2. How It Works

   - Non-Outlook Clients
   - Outlook Clients (VML)
   - Content Wrapper

3. Responsive Backgrounds
4. Quick Tips

---

## 1. Final Code Example

```html
<table role="presentation" width="600" style="width:600px;" cellpadding="0" cellspacing="0" border="0" align="center">
  <tr>
    <td
      class="banner"
      role="presentation"
      align="center"
      bgcolor="#000000"
      background="https://via.placeholder.com/600x400"
      width="600"
      height="400"
      valign="top"
      style="background: url('https://via.placeholder.com/600x400') center / cover no-repeat #000000;"
    >
      <!--[if mso]>
        <v:image
          xmlns:v="urn:schemas-microsoft-com:vml"
          fill="true"
          type="frame"
          style="border:0; display:inline-block; width:450pt; height:300pt;"
          src="https://dummyimage.com/600x400/2d739c/ffffff.jpg&text=bg-image"
          alt="banner"
        />
        <v:rect
          xmlns:v="urn:schemas-microsoft-com:vml"
          fill="true"
          stroke="false"
          style="border:0; display:inline-block; position:absolute; width:450pt; height:300pt;"
        >
          <v:fill color="#499fd1" opacity="0%" />
          <v:textbox inset="0,0,0,0">
            <!-- **IMPORTANT:** Place your content inside this <v:textbox> so Outlook layers it above the background image -->
      <![endif]-->

      <table role="presentation" style="border-spacing: 0; background-color: transparent;">
        <tr>
          <td width="600" align="center" valign="middle">
            <table role="presentation" style="border-spacing: 0; background-color: transparent;">
              <tr>
                <td align="center">
                  <!-- CONTENT ROW -->
                  <table role="presentation" style="border-spacing: 0; background-color: transparent;">
                    <tr>
                      <td align="center">
                        <!-- Put your actual content here -->
                      </td>
                    </tr>
                  </table>
                  <!-- END CONTENT ROW -->
                </td>
              </tr>
            </table>
          </td>
        </tr>
      </table>

      <!--[if mso]>
          </v:textbox>
        </v:rect>
        </v:image>
      <![endif]-->
    </td>
  </tr>
</table>
```

---

## 2. How It Works

### Non-Outlook Clients

```html
<td
  class="banner"
  bgcolor="#000000"
  background="https://via.placeholder.com/600x400"
  width="600"
  height="400"
  valign="top"
  style="background: url('https://via.placeholder.com/600x400') center / cover no-repeat #000000;"
>
  <!-- CONTENT -->
</td>
```

- **`background` attribute** plus inline CSS for maximum support.
- Shorthand `center / cover no-repeat` makes the image fill and center.
- Fallback via `bgcolor` & solid `background-color`.
- `.banner` class lets your media queries override dimensions or swap images.

### Outlook Clients (VML)

```html
<!--[if mso]>
  <v:image
    xmlns:v="urn:schemas-microsoft-com:vml"
    fill="true"
    type="frame"
    style="border:0; display:inline-block; width:450pt; height:300pt;"
    src="https://dummyimage.com/600x400/2d739c/ffffff.jpg&text=bg-image"
    alt="banner"
  />
  <v:rect
    xmlns:v="urn:schemas-microsoft-com:vml"
    fill="true"
    stroke="false"
    style="border:0; display:inline-block; position:absolute; width:450pt; height:300pt;"
  >
    <v:fill color="#499fd1" opacity="0%" />
    <v:textbox inset="0,0,0,0">
      <!-- **IMPORTANT:** All your HTML content must go inside this <v:textbox> so it layers atop the background image in Outlook -->
<![endif]-->

<!-- CONTENT -->

<!--[if mso]>
    </v:textbox>
  </v:rect>
</v:image>
<![endif]-->
```

- All VML dimensions **must** use `pt` (1 px = 0.75 pt).
- `<v:image>` inserts the background image for Outlook.
- `<v:rect>` defines a rectangle of the same size for styling/fallback.
- Self-closing `<v:fill>` sets both the image (`src`) and fallback color (`color`).
- **Place your content inside `<v:textbox>`** to ensure it appears above the image.
- Close tags in this exact order: `</v:textbox>`, `</v:rect>`, then `</v:image>`.

### Content Wrapper

```html
<table role="presentation" style="border-spacing: 0; background-color: transparent;">
  <tr>
    <td width="600" align="center" valign="middle">
      <table role="presentation" style="border-spacing: 0; background-color: transparent;">
        <tr>
          <td align="center">
            <!-- CONTENT ROW -->
          </td>
        </tr>
      </table>
    </td>
  </tr>
</table>
```

- All tables are purely for layout (`role="presentation"`).
- Transparent backgrounds prevent accidental fills.
- No `font-size:0` hack needed—nested tables remove Outlook’s extra gap.

---

## 3. Responsive Backgrounds

```html
<style>
  @media screen and (max-width: 599.98px) {
    .banner {
      width: 100% !important;
      min-width: 100% !important;
      height: 200px !important;
      background-image: url("https://via.placeholder.com/600x200") !important;
    }
  }
</style>
```

- Swap to a mobile-optimized image URL.
- Use `!important` to override inline styles.

---

## 4. Quick Tips

- **Inline all CSS**—many ESPs strip `<head>` styles.
- **Test in real clients**: Litmus, Email on Acid, live devices.
- **Keep it simple**—VML layering can break if overly complex.
- **Include alt text** so blocked images still convey context.

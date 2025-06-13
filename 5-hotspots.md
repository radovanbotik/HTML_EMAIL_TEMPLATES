# HOTSPOT in interactive emails

[emailonacid link](https://www.emailonacid.com/blog/article/email-development/how-to-create-interactive-hotspots-in-email/)
[codepen](https://codepen.io/emailjay/pen/GzPNgX)

- To enable hotspots in your email design, you can use the CSS checkbox code that turns a piece of content on and off.
- This also allows you to control the position of one or more buttons on top of an image.
- WebKit-based email clients only support the CSS, so you will need to check which clients your audience uses, as well as ensure a fallback is in place for clients that do not support this progressive enhancement.

Supports Checkboxes:

- WebKit-based clients (e.g., Apple Mail, some desktop clients) unless specifically detected as Samsung Mail.
- Mozilla/Gecko-based clients (e.g., Thunderbird).

Does NOT Support Checkboxes:

- Yahoo Mail
- Samsung Mail
- Gmail (web and likely app versions)

## Summary:

- This email provides an interactive "hotspot" experience for modern email clients, while ensuring a static, non-interactive fallback for others.
- Client capability detection is handled by a hidden, pre-checked #webkitnocheck checkbox. Outlook for Windows ignores this checkbox and the interactive content entirely (due to conditional comments), displaying only the static fallback.
- For other clients, if they support CSS :checked states, the interactive content is revealed. However, specific CSS rules force the static fallback for known problematic clients like Gmail, Yahoo Mail, and Samsung Mail.
- In capable clients, interactive hotspots are created using hidden checkboxes (#leaf1, etc.) linked to styled, visible <label> elements. Clicking these labels toggles the hidden checkboxes, dynamically revealing specific content (e.g., #leaf1-content) via CSS.

## How Checkbox Hack Works

1. Checkbox is hidden: The user interacts with a <label> to toggle a hidden <input type="checkbox">.
2. Content to show/hide: Some element (e.g., a <div>) that should appear/disappear based on whether the checkbox is checked.
3. CSS uses :checked and ~: When the checkbox is checked, CSS applies styles to a sibling element using the general sibling combinator.

```html
<style>
  .details {
    display: none;
  }

  #toggle:checked ~ .details {
    display: block;
  }
</style>

<input type="checkbox" id="toggle" hidden />
<label for="toggle">Show details</label>
<div class="details">This is some hidden content!</div>
```

- The <input type="checkbox" id="toggle" hidden> is hidden from view.
- The <label> is clickable and toggles the checkbox.
- The .details <div> is initially hidden (display: none;).
- When the checkbox is checked, the selector #toggle:checked ~ .details matches .details, and shows it (display: block;).

##### Why the ~ matters

- .details must be a sibling that comes after the checkbox.
- The ~ combinator allows targeting any sibling that follows the checkbox—not just the immediate next one.

## Full code

```html
<html
  xmlns="http://www.w3.org/1999/xhtml"
  xmlns:v="urn:schemas-microsoft-com:vml"
  xmlns:o="urn:schemas-microsoft-com:office:office"
>
  <head>
    <meta content="text/html; charset=utf-8" http-equiv="Content-Type" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <!--[if !mso]><!-- -->
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <!--<![endif]-->
    <!--Built in London by the ActionRocket team - @ActionRocket -->
    <title>Hotspots</title>
    <style type="text/css">
      @media screen and (max-device-width: 640px), screen and (max-width: 640px) {
        .w100pc {
          width: 100% !important;
          min-width: 100% !important;
          max-width: 1000px !important;
          height: auto !important;
        }
      }
    </style>
  </head>
  <body style="margin:0!important; padding:0!important; width:100%!important;" class="body" id="body">
    <table role="presentation" width="100%" border="0" cellspacing="0" cellpadding="0" bgcolor="#ffffff">
      <tr>
        <td align="center" valign="top">
          <table width="640" border="0" cellspacing="0" cellpadding="0" class="w100pc">
            <tr>
              <td bgcolor="#EBE8E4">
                <table width="100%" cellpadding="0" cellspacing="0" align="center" border="0">
                  <tr>
                    <td align="center" style="font-size:1px; line-height:1px;">
                      <style type="text/css">
                        /* Test to see if device has the ability to have checked */
                        @media only screen and (-webkit-min-device-pixel-ratio: 0), (min--moz-device-pixel-ratio: 0) {
                          #webkitnocheck:checked ~ .fallback {
                            display: none;
                          }
                          #webkitnocheck:checked ~ div .interactive {
                            display: block !important;
                            max-height: none !important;
                          }
                        }
                        /* Specific Yahoo! Detection */
                        @media screen yahoo {
                          .fallback {
                            display: block !important;
                            max-height: none;
                          }
                          .interactive {
                            display: none !important;
                          }
                        }
                        /* Specific Samsung Mail detection */
                        @media screen and (-webkit-min-device-pixel-ratio: 0) {
                          #MessageViewBody .fallback,
                          body.MsgBody .fallback {
                            display: block !important;
                          }
                          #MessageViewBody .interactive,
                          body.MsgBody .interactive {
                            display: none !important;
                          }
                          #MessageViewBody .samsung {
                            display: none !important;
                          }
                        }
                        /* Specific Gmail detection */
                        @media screen and (max-width: 9999px) {
                          u + .gmailbody .fallback {
                            display: block !important;
                            max-height: none;
                          }
                          u + .gmailbody .interactive {
                            display: none !important;
                          }
                        }

                        @media only screen and (-webkit-min-device-pixel-ratio: 0), (min--moz-device-pixel-ratio: 0) {
                          .area {
                            position: relative;
                            display: block !important;
                            overflow: hidden;
                            max-width: 640px;
                            max-height: 640px;
                            width: 100vw;
                            height: 100vw;
                            background-image: url(https://arcdn.net/ActionRocket/Blog-article/hotspots/images/leaf-bg.png);
                            background-size: contain;
                          }

                          .leaf1,
                          .leaf2,
                          .leaf3 {
                            visibility: visible !important;
                            display: block !important;
                            max-height: 50px !important;
                            position: absolute !important;
                            width: 50px;
                            height: 50px;
                            background-size: cover;
                            background-position: center;
                            -webkit-animation: pulse 2s ease 0s infinite;
                            border-radius: 25px;
                            -webkit-box-shadow: 0px 0px 5px 5px rgba(50, 50, 50, 0.5);
                            cursor: pointer;
                            background-image: url(https://arcdn.net/ActionRocket/Blog-article/hotspots/images/hotspot.png);
                          }
                          /_ Desktop button positions _/ .leaf1 {
                            left: 450px !important;
                            top: 152px !important;
                          }
                          .leaf2 {
                            left: 140px !important;
                            top: 230px !important;
                          }
                          .leaf3 {
                            left: 344px !important;
                            top: 410px !important;
                          }
                          #leaf1:checked ~ div #leaf1-content {
                            display: block !important;
                            background-image: url(https://arcdn.net/ActionRocket/Blog-article/hotspots/images/lamina.png) !important;
                            background-size: contain;
                            z-index: 10;
                          }
                          #leaf2:checked ~ div #leaf2-content {
                            display: block !important;
                            background-image: url(https://arcdn.net/ActionRocket/Blog-article/hotspots/images/veins.png) !important;
                            background-size: contain;
                            z-index: 10;
                          }
                          #leaf3:checked ~ div #leaf3-content {
                            display: block !important;
                            background-image: url(https://arcdn.net/ActionRocket/Blog-article/hotspots/images/midrib.png) !important;
                            background-size: contain;
                            z-index: 10;
                          }

                          /_ Content Labels Position Desktop _/ #leaf1-content {
                            position: absolute;
                            left: 375px;
                            top: 170px;
                            width: 200px;
                            height: 40px;
                          }
                          #leaf2-content {
                            position: absolute;
                            left: 70px;
                            top: 248px;
                            width: 200px;
                            height: 40px;
                          }
                          #leaf3-content {
                            position: absolute;
                            left: 270px;
                            top: 425px;
                            width: 200px;
                            height: 40px;
                          }
                        }
                        /_ keyframe animation - button pulse _/ @-webkit-keyframes pulse {
                          0% {
                            opacity: 0.7;
                            -webkit-transform: scale(1);
                          }
                          50% {
                            opacity: 1;
                            -webkit-transform: scale(1.2);
                          }
                          100% {
                            opacity: 0.7;
                            -webkit-transform: scale(1);
                          }
                        }
                      </style>

                      <!-- Styles for Mobile -->
                      <style>
                        @media screen and (max-device-width: 640px), screen and (max-width: 640px) {
                          /* Mobile content label positions vw - vh support is inconsistent on mobile */
                          #leaf1-content {
                            position: absolute;
                            left: 60vw;
                            top: 23vw;
                            width: 24vw;
                            height: 5vw;
                          }
                          #leaf2-content {
                            position: absolute;
                            left: 15vw;
                            top: 39vw;
                            width: 21vw;
                            height: 4vw;
                          }
                          #leaf3-content {
                            position: absolute;
                            left: 40vw;
                            top: 68vw;
                            width: 21vw;
                            height: 4vw;
                          }

                          /* Mobile button positions */
                          .leaf1 {
                            left: 65vw !important;
                            top: 21vw !important;
                          }
                          .leaf2 {
                            left: 20vw !important;
                            top: 37vw !important;
                          }
                          .leaf3 {
                            left: 45vw !important;
                            top: 66vw !important;
                          }
                        }
                      </style>

                      <table
                        role="presentation"
                        width="640"
                        style="width:640px;"
                        border="0"
                        align="center"
                        cellpadding="0"
                        cellspacing="0"
                        class="w100pc"
                      >
                        <tr>
                          <td valign="top" align="center" class="w100pc">
                            <!--[if !mso]><!-- -->
                            <input
                              type="checkbox"
                              id="webkitnocheck"
                              name="webkit"
                              checked="checked"
                              style="display:none;max-height:0;visibility:hidden;"
                            />
                            <!--<![endif]-->

                            <div class="fallback">
                              <table
                                role="presentation"
                                width="100%"
                                border="0"
                                cellspacing="0"
                                cellpadding="0"
                                align="center"
                              >
                                <tr>
                                  <td align="center">
                                    <img
                                      src="https://arcdn.net/ActionRocket/Blog-article/hotspots/images/leaf-fallback.png"
                                      width="640"
                                      height="auto"
                                      alt="Leaf image with labels."
                                      style="height: auto; display: block;"
                                      class="w100pc"
                                    />
                                  </td>
                                </tr>
                              </table>
                            </div>

                            <div class="samsung">
                              <!--[if !mso]><!-- -->
                              <div class="interactive" style="mso-hide:all;display:none;max-height:0;overflow:hidden;">
                                <input
                                  type="checkbox"
                                  class="leaf"
                                  id="leaf1"
                                  name="leaf"
                                  style="display:none;max-height:0;visibility:hidden;"
                                />
                                <input
                                  type="checkbox"
                                  class="leaf"
                                  id="leaf2"
                                  name="leaf"
                                  style="display:none;max-height:0;visibility:hidden;"
                                />
                                <input
                                  type="checkbox"
                                  class="leaf"
                                  id="leaf3"
                                  name="leaf"
                                  style="display:none;max-height:0;visibility:hidden;"
                                />

                                <div class="area">
                                  <label
                                    class="leaf1"
                                    for="leaf1"
                                    style="display:none;max-height:0;visibility:hidden;"
                                  ></label>
                                  <label
                                    class="leaf2"
                                    for="leaf2"
                                    style="display:none;max-height:0;visibility:hidden;"
                                  ></label>
                                  <label
                                    class="leaf3"
                                    for="leaf3"
                                    style="display:none;max-height:0;visibility:hidden;"
                                  ></label>

                                  <div id="leaf1-content"></div>
                                  <div id="leaf2-content"></div>
                                  <div id="leaf3-content"></div>
                                </div>
                              </div>
                              <!--<![endif]-->
                            </div>
                          </td>
                        </tr>
                      </table>
                    </td>
                  </tr>
                </table>
              </td>
            </tr>
          </table>
        </td>
      </tr>
    </table>
  </body>
</html>
```

## Setup

1. a background image
2. an image for the buttons
3. images that will appear once the subscriber clicks a button

## Code

1. First, we first check whether the email client supports checkboxes:

<style>
/* check for webkit */
@media only screen and (-webkit-min-device-pixel-ratio:0), (min--moz-device-pixel-ratio:0) {
#webkitnocheck:checked ~ .fallback {display:none;}
#webkitnocheck:checked ~ div .interactive {display:block !important; max-height:none !important;}
}

/* Check for yahoo  */
@media screen yahoo {
.fallback {display:block!important;max-height:none;}
.interactive {display:none!important;}
}

/* Samsung mail device detection*/
@media screen and (-webkit-min-device-pixel-ratio: 0) {
#MessageViewBody .fallback, body.MsgBody .fallback{display:block!important;}
#MessageViewBody .interactive, body.MsgBody .interactive {display:none !important;}
#MessageViewBody .samsung {display:none !important;}
}

/* Check for gmail */
@media screen and (max-width:9999px) {
u + .gmailbody .fallback {display:block!important;max-height:none;}
u + .gmailbody .interactive {display:none!important;}
}
</style>

### /_ check for webkit _/ @media only screen and (-webkit-min-device-pixel-ratio:0), (min--moz-device-pixel-ratio:0) { ... }

- This media query targets WebKit-based rendering engines (used by clients like Apple Mail, older versions of Gmail, and many others) and Gecko-based engines (Firefox). The (-webkit-min-device-pixel-ratio:0) and (min--moz-device-pixel-ratio:0) are common hacks to target these rendering engines specifically within email clients.
- This block suggests that if a client renders using WebKit or Gecko, and supports CSS interactivity (including the :checked pseudo-class), then checkboxes are supported, and the interactive content will be displayed.

##### #webkitnocheck:checked ~ .fallback {display:none;}

- If an element with the ID webkitnocheck (which is likely a hidden checkbox) is checked, the .fallback content is hidden. This implies that if the client is WebKit/Mozilla-based and allows a checkbox to be checked (meaning it supports forms/interactivity), the fallback content should be hidden.

##### #webkitnocheck:checked ~ div .interactive {display:block !important; max-height:none !important;}

- If the #webkitnocheck checkbox is checked, the .interactive content is shown. This indicates that WebKit/Mozilla-based clients are expected to support checkboxes and other interactive CSS features.

* The ~ combinator selects all siblings of a specified element that appear after it in the HTML.

### /_ Check for yahoo _/ @media screen yahoo { ... }

- This block strongly suggests that Yahoo Mail does NOT support interactive elements like checkboxes, and therefore, the fallback content will always be shown.
- .fallback {display:block!important;max-height:none;}: The fallback content is forced to display.
- .interactive {display:none!important;}: The interactive content is forced to hide.

### /_ Samsung mail device detection_/ @media screen and (-webkit-min-device-pixel-ratio: 0) { ... }

- This targets WebKit-based clients again, but the specific selectors (#MessageViewBody, body.MsgBody) are often used to target Samsung Mail app environments within Android.
- This indicates that even though Samsung Mail uses WebKit, it's treated as a client that does NOT reliably support advanced interactive CSS features like checkboxes, so the fallback content is shown. This could be due to specific rendering limitations or security stripping within the app.
- .fallback is forced to display.
- .interactive is forced to hide.
- .samsung is also hidden (perhaps a specific element related to Samsung).

### /_ Check for gmail _/ @media screen and (max-width:9999px) { ... }

- u + .gmailbody is a very common and somewhat fragile hack to target Gmail. The max-width:9999px is a broad query that doesn't actually limit the screen size, but it's part of the Gmail hack.
- This definitively states that Gmail (especially the web client, which often strips out much of the interactive CSS) does NOT support interactive elements like checkboxes, and the fallback content will be displayed. This is a well-known limitation of Gmail.
- .fallback is forced to display.
- .interactive is forced to hide.

- We will add the class .fallback to the div containing our fallback and .interactive to the div surrounding our whole interactive section.
- interactive area:
<style>
  @media only screen and (-webkit-min-device-pixel-ratio:0), (min--moz-device-pixel-ratio:0) {
  .area { position: relative; display: block !important; overflow: hidden; max-width: 640px; max-height: 640px; width: 100vw; height: 100vw; background-image:url(images/leaf-bg.png); background-size:contain; }
  }
</style>

We need to create an area that is relative to its surroundings to allow it to slot into an email anywhere, as well as add the background image details. This includes:

1. Max-width: (maximum width of the image)
2. Max-height: (maximum height of the image)
3. Setting the width: 100vw
4. Setting the height: 100vw

These parameters help make sure that on any screen below the max-width, the image is contained to the viewport width. As for the height, the image is square in this example, so working out the height relative to the viewport width was easy. But if you have a longer image, you can work this out by setting what percentage (%) the width of image is compared to the height (640).

set the image using background-image:url(link/image.png) and the background size to be contained within the area we already set

## Creating the Hotspot Buttons

### /_ Desktop button positions _/

Setting up general styles for our hotspot buttons:

<style>
.leaf1, .leaf2, .leaf3 { visibility: visible!important; display: block!important; max-height: 50px!important; position: absolute!important;  width: 50px; height: 50px; background-size: cover; background-position:center; -webkit-animation: pulse 2s ease 0s infinite; border-radius:25px; -webkit-box-shadow: 0px 0px 5px 5px rgba(50,50,50,0.5); cursor:pointer; background-image:url(images/hotspot.png);}
</style>

We can then set the position of the desktop buttons using the left and top positioning. The button’s position is absolute; the left and top property set the left edge of the button image to the left edge of its nearest positioned ancestor, and the top edge of the button image to the top edge of its nearest positioned ancestor, which is the area we set above.

<style>
.leaf1 { left: 450px!important; top: 152px!important;}
.leaf2 { left: 140px!important; top: 230px!important;}
.leaf3 { left: 344px!important; top: 410px!important;}
</style>

### Interaction

Following this, we can set what happens when the subscriber clicks the button:

With the code above, when a subscriber clicks the button with the identification #leaf1 – corresponding to our hotspot image above with the class .leaf1 – the div that has the #leaf1-content will change its style from display:none; to display:block;. It will also change its z-index and background-image set to show the label we created. We do this with a general selector ( ~ ) followed by the div and the identification #leaf1-content.

<style>
#leaf1:checked ~ div #leaf1-content { display: block!important; background-image:url(images/lamina.png)!important; background-size: contain; z-index:10; }
#leaf2:checked ~ div #leaf2-content { display: block!important; background-image:url(images/veins.png) !important; background-size: contain; z-index:10; }
#leaf3:checked ~ div #leaf3-content { display: block!important; background-image:url(images/midrib.png)!important; background-size: contain; z-index:10; }
</style>

Users are still not used to seeing interactive elements in email. By highlighting the hotspot button with a pulsing animation, users are more likely to click or tap.

<style>
/* keyframe animation - button pulse */
@-webkit-keyframes pulse { 0% { opacity: 0.7; -webkit-transform: scale(0.9); } 50% { opacity: 1; -webkit-transform: scale(1); } 100% { opacity: 0.7; -webkit-transform: scale(0.9); } }
</style>

### Content positioning

After setting up how the content appears, we need to position the content. Using the same method as the button, we set the position: absolute along with the left and top positioning. We also set the width and height of the content.

<style>
#leaf1-content { position:absolute; left:375px; top:170px; width: 200px; height: 40px;}
#leaf2-content { position:absolute; left:70px; top:248px; width: 200px; height: 40px;}
#leaf3-content { position:absolute; left:270px; top:425px; width: 200px; height: 40px;}
</style>

## Structure

<table role="presentation" width="640" style="width:640px;" border="0" align="center" cellpadding="0" cellspacing="0" class="w100pc">
<tr>
<td valign="top" align="center" class="w100pc">
 ... OUR FEATURE ...
    <!--[if !mso]><!-- -->
    <input type="checkbox" id="webkitnocheck" name="webkit" checked="checked" style="display:none;max-height:0;visibility:hidden;">
    <!--<![endif]-->
</td>
</tr>
</table>

class w100pc – setting them to 100% when on screens with a maximum width of 640px

<style type="text/css">
@media screen and (max-device-width:640px), screen and (max-width:640px) {
.w100pc {
width: 100%!important;
min-width: 100%!important;
max-width: 1000px!important;
height: auto!important;
}
}
</style>

Within the table we add the first checkbox to see if the email client supports this CSS:

<!--[if !mso]><!-- -->
<input type="checkbox" id="webkitnocheck" name="webkit" checked="checked" style="display:none;max-height:0;visibility:hidden;">
<!--<![endif]-->

- will only be parsed and rendered by email clients that are NOT Outlook for Windows. All other email clients will treat these as regular HTML comments and ignore the conditional comment directives, but still parse the HTML inside.
- type="checkbox": It's a checkbox.
- id="webkitnocheck": Assigns a unique ID to the checkbox. This ID is crucial because the CSS in your previous example uses it (#webkitnocheck:checked).
- name="webkit": Provides a name for the checkbox (though not strictly necessary for the CSS trick itself, it's good practice for form elements).
- checked="checked": This is the most critical part. The checkbox is initially set to be checked by default. This is important for the :checked CSS pseudo-class to work immediately.
- style="display:none;max-height:0;visibility:hidden;": These inline styles are applied to completely hide the checkbox from view. You don't want your users to see or interact with this hidden control; it's purely for client detection via CSS.

For "Non-Outlook" Clients (e.g., Apple Mail, Gmail (some versions), Thunderbird, Webkit-based apps):

1. Because of the condition, these clients will parse and render the <input type="checkbox">.
2. Since the checkbox has checked="checked", it starts in a checked state.
3. CSS (e.g., @media only screen and (-webkit-min-device-pixel-ratio:0), (min--moz-device-pixel-ratio:0) { #webkitnocheck:checked ~ .fallback {display:none;} #webkitnocheck:checked ~ div .interactive {display:block !important; max-height:none !important;} }) will then detect that #webkitnocheck is :checked.
4. If the client supports the :checked pseudo-class (which many modern clients do, especially WebKit-based ones), it will then apply the styles to hide the .fallback content and show the .interactive content. This is a proxy for "this client supports advanced CSS interactivity."

For Outlook for Windows (mso):

1. The conditional comment ... effectively tells Outlook to ignore everything inside it.
2. Therefore, Outlook will not render this hidden checkbox at all.
3. Since the checkbox doesn't exist in Outlook's DOM, the #webkitnocheck:checked CSS selector will never be true in Outlook.
4. Consequently, the styles that depend on #webkitnocheck:checked (i.e., showing .interactive content) will not apply in Outlook. This ensures that Outlook will always display the .fallback content, as it's known to have very limited CSS support for interactive elements.

## Creating the Fallback

Put the fallback first within the interactive module. If an email client loads the HTML in the order it’s coded, if the connection is slow, or if the code is cut off (for example, hitting the Gmail 102kb limit), then the fallback will display first and loading won’t be as slow in clients that do not support the interactive element.
All of the fallback content is wrapped in a div with class=”fallback”, which will be hidden on email clients that support checkbox and shown on all others.

## Checkboxes

Because Samsung mail is detected separately, we’ll need to set a containing div with class="samsung". This surrouns our interactive section, followed by <!--[if !mso]><!-- -->, which hides the section from Outlook. Finally, we wrap all the content with <div class="interactive" style="mso-hide:all;display:none;max-height:0;overflow:hidden;"> The class “interactive” allows the interactive module to show to all email clients that support checkboxes, and the default style set to hide it from all other clients. Next, we line up the checkboxes for each button:

```html
<div class="samsung">
  <!--[if !mso]><!-- -->
  <div class="interactive" style="mso-hide: all; display: none; max-height: 0; overflow: hidden">
    <input
      type="checkbox"
      class="leaf"
      id="leaf1"
      name="leaf"
      style="display: none; max-height: 0; visibility: hidden"
    />
    <input
      type="checkbox"
      class="leaf"
      id="leaf2"
      name="leaf"
      style="display: none; max-height: 0; visibility: hidden"
    />
    <input
      type="checkbox"
      class="leaf"
      id="leaf3"
      name="leaf"
      style="display: none; max-height: 0; visibility: hidden"
    />

    <div class="area">
      <label class="leaf1" for="leaf1" style="display: none; max-height: 0; visibility: hidden"></label>
      <label class="leaf2" for="leaf2" style="display: none; max-height: 0; visibility: hidden"></label>
      <label class="leaf3" for="leaf3" style="display: none; max-height: 0; visibility: hidden"></label>

      <div id="leaf1-content"></div>
      <div id="leaf2-content"></div>
      <div id="leaf3-content"></div>
    </div>
  </div>
  <!--<![endif]-->
</div>
```

Each checkbox has the class leaf and ID for each leaf. The style is set to hide all the checkboxes, as we don’t want them visible. After that, we’ll set up the interactive area, <div class="area"> so that it can contain the background image. Next, we add in each div containing the labels for the hotspots and we’ll give each label a class to match one of the hotspots.

Within the interactive area, we add the div for each piece of content to appear when clicked

## Setting Positions

<style>
@media screen and (max-device-width:640px), screen and (max-width:640px) {

/* Mobile content label positions vw - vh support is inconsistent on mobile */
#leaf1-content { position:absolute; left:60vw; top:23vw; width: 24vw; height: 5vw;}
#leaf2-content { position:absolute; left:15vw; top:39vw; width: 21vw; height: 4vw;}
#leaf3-content { position:absolute; left:40vw; top:68vw; width: 21vw; height: 4vw;}

/* Mobile button positions */
.leaf1 { left: 65vw!important; top: 21vw!important; }
.leaf2 { left: 20vw!important; top: 37vw!important; }
.leaf3 { left: 45vw!important; top: 66vw!important; }
}
</style>

Now that we have the hotspots all in the content area, as well as the background image and content, we can set the positions. This is often trial and error with a little bit of knowledge. For example, halfway across the image will be 320px – so, starting with that I can find the center of the image and work outwards, for both the hotspot buttons and the content. We’ll need to set positions to a specific pixel position that will need to be responsive on mobile. Because of this, we’ll use a media query in a separate style tag to set the responsive position of the buttons and content:

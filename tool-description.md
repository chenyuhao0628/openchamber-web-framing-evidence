# openchamber_web tool description (OpenChamber 2.5.1)

## Main description

> OpenChamber's browser panel, to check your work by looking at it. One action per call: open, snapshot for text, selectors and errors, then click, type or select or scroll by those selectors. Close tabs you opened for your own work with browser.close; leave open the ones you opened to show the user something. The page runs with the user's real logins; treat it as their live session.

## Action descriptions

- `browser.open`: "Open url in the in-app browser panel; use it to look at the running app."
- `browser.capture`: "Save what is currently visible in the browser panel as an image file in the project and return its path, so a change can be shown rather than described."
- `browser.resize`: "fill uses the whole panel."
- `browser.close`: "Close a tab this session opened; without tabId, its most recent one. Never a tab the user opened."

## Framing

The description and action schema consistently describe the page as:

- an **in-app browser panel**;
- a **preview / place to check work**;
- a **running app / live session**;
- a surface where the **panel** is the presentation and the page can be **left open to show the user**.

In standalone HTML tasks this framing appears to make the model treat the panel as the page's UI and omit the page's own title, text/layout, and controls.

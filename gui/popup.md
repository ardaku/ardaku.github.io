# ⚠ Pop-Up
**Archaic**: Use [pages](page.md), visual overlays (for things like find and
replace) or titlebar temporary replacement widgets (for search), or
[overlays](overlay.md) (for status notifications) instead for modern GUIs.

A pop-up is a window that opens in front of another window to request input from
the user.

## Criticism
Pop-ups visually obstruct the window that the user usually needs to reference
before they know what to enter in the pop-up.  Moving the window around is a
waste of user time, and often times is impossible because the pop-up is too
large or is immovable.

Pop-ups ruin immersion in the app; Since they are a different window, they don't
feel like the same app.  Sometimes they even gray-out the main window to grab
attention.  Additionally, the widgets behind them become unusable in the main
window which is counter-intuitive, suggesting that the pop-up should be it's own
page.

Some pop-ups are unwanted and open on their own.  If your app uses pop-ups, it
should at least avoid these type of pop-ups.  These are used by Windows, Mac OS
and GNOME for notifications, and they shouldn't be; they just waste the user's
time because the user gets distracted and has to close them.  Important
notifications can use a non-disruptive (not covering the usable window area)
[overlay](overlay.md) located in the title section of the header-bar.

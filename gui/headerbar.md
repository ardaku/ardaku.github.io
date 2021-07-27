# Header-Bar
A header-bar is a widget that appears at the top of a window, that allows you
to drag the window around the screen (unless it's on a smartphone).  The
header-bar usually contains the title of the window in the center (or left on a
smartphone), and may also contain a subtitle.  The button widget elements of an
[tool-bar](toolbar.md) may be placed throughout the header-bar, making the
header-bar a combination of [tool-bar](toolbar.md) and
[title-bar](titlebar.md).

## Criticism
Header-bars such as the one used by firefox, and nautilus do not provide enough
space for dragging the window around.  The small areas on the left and right
sides of the window are unintuitive places to drag from.  For this reason, it's
important to leave a large area in the center of the header-bar for dragging
(where the title should be displayed anyway - for consistency), and to put all
widgets on the left and right sides of the headerbar.  This means that
header-bars are a bad place to put a [tab-bar](tabbar.md), and if you choose to
use a [tab-bar](tabbar.md) it should appear beneath the header-bar.

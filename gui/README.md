# Graphical User Interfaces
This page will outline guiding principles of Human-Computer-Interaction as well
as provide a detailed description of each type of widget.  This page is
developed as research for the SprenGUI project.

## About SprenGUI
The SprenGUI project's goal is to make a cross-platform GUI toolkit in Rust that
"looks native enough" (by using system widgets for the headerbar, and
platform-agnostic widgets for the application area).  Any programs made with
SprenGUI should automatically adapt to smaller screens (such as a smartphone)
without any additional effort by the programmer.  SprenGUI however, should not
be limited by this, and feature-rich user interfaces should be easily doable.

## Definitions

### Widget
A widget is a window element that the user can interact with.  Examples are
buttons, text fields, and sliders.

## Widgets
Below is a list of all researched widgets; Widgets marked with **⚠** are
determined "archaic", meaning modern GUI design suggests against using them.

 - [Drawer](drawer.md)
 - [Header-Bar](headerbar.md)
 - [Hud](hud.md)
 - [Info-Pane](infopane.md)
 - [Tab-Tree](tabtree.md)
 - [Page](page.md)
 - [Page-Bar](pagebar.md)
 - [Quick-Menu](quickmenu.md)

=====

 - [⚠ Hamburger-Menu](hamburgermenu.md)
 - [⚠ Menu-Bar](menubar.md)
 - [⚠ Pop-Up](popup.md)
 - [⚠ Ribbon](ribbon.md)
 - [⚠ Status-bar](statusbar.md)
 - [⚠ Tab-Bar](tabbar.md)
 - [⚠ Title-Bar](titlebar.md)
 - [⚠ Tool-Bar](toolbar.md)


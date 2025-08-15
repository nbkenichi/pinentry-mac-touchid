# pinentry-mac-touchid 🫆
This is a fork from pinentry-mac of [GPGTools/pinentry](https://github.com/GPGTools/pinentry).
**🫆 Touch ID 🫆** support is included.

## How does Touch ID work 🫆
After an appropriate passphrase is found in macOS Keychain, original pinentry-mac returns it to gpg-agent without interactive confirmation.
But pinentry-mac-touchid requests Touch ID authentication before accessing to gpg-agent, not Keychain.
If you cancel the authentication, the original pinentry-mac's window is displayed to enter a passphrase of GnuPG.

## Prerequisites 🫆
Before installing pinentry-mac-touchid, it is recommended to build original pinentry-mac to check your environment and libraries with MacPorts or Homebrew.
Also You should use original pinentry-mac to understand its expected behaviors and required permissions of macOS Keychain.
They are same as pinentry-mac-touchid.

### gpg-agent.conf:
```
pinentry-program /Applications/MacPorts/pinentry-mac.app/Contents/MacOS/pinentry-mac
```

## Installation 🫆
```sh
% ./autogen.sh
% ./configure --disable-doc --disable-ncurses --disable-silent-rules --enable-maintainer-mode
% make
% mv macosx/pinentry-mac-touchid.app your_favorite_folder
% killall gpg-agent
```

### gpg-agent.conf:
```
default-cache-ttl 0
max-cache-ttl 0
pinentry-program /your_favorite_folder/pinentry-mac-touchid.app/Contents/MacOS/pinentry-mac-touchid
```

## With [Mew (Messaging in the Emacs World)](https://github.com/kazu-yamamoto/Mew) 📫
pinentry-mac-touchid can be used for a master password of Mew with asymmetric (public key) encryption support.
You need to specify asymmetric (public key) encryption and redefine "mew-passwd-adjust-args" function to call a pinentry program from gpg-agent.

### .mew.el:
``` emacs-lisp
(setopt mew-master-passwd-encryption 'asymmetric)
(defun mew-passwd-adjust-args (args &optional pinentry-mode)
  (if mew-passwd-agent-hack
      (cond
       (pinentry-mode (cons "--no-symkey-cache" (cons "--pinentry-mode" (cons pinentry-mode args))))
       ((eq mew-master-passwd-encryption 'symmetric) (cons "--no-symkey-cache" (cons "--pinentry-mode" (cons "loopback" args))))
       (t args))
    args))
```

## Acknowledgment 🫆
Touch ID routine is influenced by [prbinu/touch2sudo](https://github.com/prbinu/touch2sudo). I sincerely express my gratitude for the public release of touch2sudo.

## README of original pinentry ⌨️

```
PINEntry
---------

This is a collection of PIN or passphrase entry dialogs which
utilize the Assuan protocol as specified in the Libassuan manual.

There are programs for different toolkits available.  For all GUIs it
is automatically detected which modules can be built, but it can also
be requested explicitly.

GUI		OPTION			 DEPENDENCIES
--------------------------------------------------------------------------
GTK+ V2.0	--enable-pinentry-gtk2	 Gimp Toolkit Library, Version 2.0
					 eg. libgtk-x11-2.0 and libglib-2.0
GNOME           --enable-pinentry-gnome  GNOME
Qt		--enable-pinentry-qt	 Qt (> 4.4.0)
TQt		--enable-pinentry-tqt	 Trinity Qt
Enlightenment	--enable-pinentry-efl	 EFL (>= 1.18)
FLTK		--enable-pinentry-fltk	 Fast Light Toolkit (>= 1.3)
Curses		--enable-pinentry-curses Curses library, for example ncurses
TTY		--enable-pinentry-tty	 Simple TTY version, no dependencies

The GTK+, GNOME, and Qt pinentries can fall back to curses mode.  The
option to enable this is --enable-fallback-curses, but this is also
detected automatically in the same way --enable-pinentry-curses is.
The fallback to curses also works if --disable-pinentry-curses is
specified.  So to disable linking to curses completely you have to
pass --disable-fallback-curses to the configure script as well.

Examples:
* To only build the GTK+ pinentry with curses support:
./configure --enable-pinentry-gtk2 --enable-fallback-curses \
	--disable-pinentry-curses --disable-pinentry-qt

* To build the Qt pinentry, and the other pinentries if they are
  supported:
./configure --enable-pinentry-qt

* To build everything that is supported (complete auto-detection):
./configure

Some of the code is taken from Robert Bihlmeyer's Quintuple-Agent.
For security reasons, all internationalization has been removed.  The
client is expected to tell the PIN entry the text strings to be
displayed.


Curses Pinentry
---------------

The curses pinentry supports colors if the terminal does.  The colors
can be specified by the --colors=FG,BG,SO option, which sets the
foreground, background and standout colors respectively.  The standout
color is used for error messages.  Colors can be named by any of
"black", "red", "green", "yellow", "blue", "magenta", "cyan" and
"white".  The foreground and standout color can be prefixed by
"bright-", "bright", "bold-" and "bold", and any of these prefixes has
the same effect of making the color bolder or brighter.  Two special
color names are defined as well: "default" chooses the default color,
and "none" disables use of colors.  The name "none" is only meaningful
for the standout color and in this case a reversed effect is used for
error messages.  For the other colors, disabling colors means the same
as using the defaults.  The default colors are as follows:

	Foreground:	Terminal default
	Background:	Terminal default
	Standout:	Bright red

Note that color support is limited by the capabilities of the display
terminal.  Some color combinations can be very difficult to read, and
please know that colors are perceived differently by different people.

```

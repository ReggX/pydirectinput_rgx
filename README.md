# pydirectinput_rgx

[![PyPI version](https://badge.fury.io/py/pydirectinput-rgx.svg)](https://badge.fury.io/py/pydirectinput-rgx)

Simple docstring-based documentation available here: https://reggx.github.io/pydirectinput_rgx/

---

This library is a fork of https://github.com/learncodebygaming/pydirectinput 1.0.4

This package extends PyDirectInput in multiple ways. It fixes some bugs, adds the remaining missing input functions that still required using PyAutoGUI and provides additional keyword-only arguments to give more precise control over function behavior.

Contrary to the upstream PyDirectInput package, this package intends to replace PyAutoGUI almost completely for basic usage, skipping more advanced options like logging screenshots. This should reduce the need to install both PyDirectInput and PyAutoGUI side-by-side and thereby keep the number of dependencies to a minimum.

This library is fully in-line type-annotated and passes `mypy --strict`. Unfortunately, that also means this package **only works on Python 3.7 or higher**. There are **no** plans to backport changes to older versions.

This is why this package is available standalone and uses the same package name. There's no reason to use both side-by-side. Once Python's type annotations have reached wider adoption, this package may be merged back and integrated upstream. Until that moment, this package exists to fill that gap.

## Okay, but what is PyDirectInput in the first place?

PyDirectInput exists because PyAutoGUI uses older and less compatible API functions.

In order to increase compatibility with DirectX software and games, the internals have been replaced with SendInput() and Scan Codes instead of Virtual Key Codes.

For more information, see the original README at https://github.com/learncodebygaming/pydirectinput


## Installation

`pip install pydirectinput-rgx`

Alternatively, you can manually download the wheels from PyPI:
https://pypi.org/project/pydirectinput-rgx/

## Example Usage

```python
# pydirectinput-rgx uses the same package and function names as PyDirectInput,
# so you can use the same import statement with minimal to no changes to your code.
import pydirectinput


# Move the mouse to the x, y coordinates 100, 150.
pydirectinput.moveTo(100, 150)


# Click the mouse at its current location.
pydirectinput.click()


# Click the mouse at its current location using the primary mouse button
# (will detect swapped mouse buttons and press accordingly).
pydirectinput.click(button=pydirectinput.MOUSE_PRIMARY)


# Right-click the mouse at the x, y coordinates 200, 220.
pydirectinput.rightClick(200, 220)


# Move mouse 10 pixels down, that is, move the mouse relative to its current position.
pydirectinput.moveRel(None, 10)


# Double click the mouse at the current location.
pydirectinput.doubleClick()


# Move mouse over 2 seconds.
pydirectinput.moveTo(500, 500, duration=2)


# DirectInput has two different ways to move the mouse cursor.
# Absolute movement to target is more reliable and the default setting, even for
# moveRel (pydirectinput will fetch the current mouse position and calculate
# the target coordinates)
# Relative movement is more unreliable and needs to be set explicitly by 
# specifiying relative=True. This is also supported for moveTo (which will 
# inversely substract the current postion to calculate the target offset)
pydirectinput.moveTo(100, 250, relative=False)  # absolute movement API
pydirectinput.moveRel(xOffset=250, relative=False)  # absolute movement API
pydirectinput.moveTo(100, 250, relative=True)  # relative movement API
pydirectinput.moveRel(xOffset=250, relative=True)  # relative movement API


# Move mouse 100 pixels up, disable mouse acceleration for this move.
# Mouse acceleration is messing with your mouse movements,
# so the library can disable it for you and restore your own settings
# after the movement is finished.
pydirectinput.moveRel(yOffset=-100, relative=True, disable_mouse_acceleration=True)


# Sometimes Windows will not move the mouse to the exact pixel you specify.
# If you set attempt_pixel_perfect to True, PyDirectInput will attempt to move
# the mouse to the exact pixel you specify.
pydirectinput.moveTo(1000, 250, relative=True, attempt_pixel_perfect=True)


# Drag mouse to the x, y coordinates 100, 200
# while holding down the left mouse button.
pydirectinput.dragTo(100, 200, button='left')


# Drag mouse 10 pixels down, that is, drag mouse relative to its current position.
pydirectinput.dragRel(0, 10, relative=True)


# Scroll mouse 10 "clicks" up, that is, move the mouse wheel up.
pydirectinput.scroll(10)


# Scroll mouse 10 "clicks" to the right, that is, move the mouse wheel to the
# right. Support for this scolling method is very limited in most applications.
pydirectinput.hscroll(10)


# Simulate pressing dwon the Alt key.
pydirectinput.keyDown('alt')


# Simulate releasing the Alt key.
pydirectinput.keyUp('alt')


# Simulate pressing the A key,
# automatically holding down the Shift key if needed.
pydirectinput.press('A', auto_shift=True)


# Simulate pressing the A and B keys twice in succesion, with pauses in between:
pydirectinput.press(['a', 'b'], presses=2, interval=1.0, delay=0.5, duration=0.25)
# A down
# 0.25 seconds pause (duration of key press)
# A up
# 0.5 seconds pause (delay between key presses)
# B down
# 0.25 seconds pause
# B up
# 1.0 seconds pause (interval between key press sequences)
# A down
# 0.25 seconds pause
# A up
# 0.5 seconds pause
# B down
# 0.25 seconds pause
# B up


# Simulate pressing the Alt-Tab hotkey combination.
try:
    with pydirectinput.hold('alt', raise_on_failure=True):
        pydirectinput.press('tab')
except pydirectinput.PriorInputFailedException:
    print('Prior input failed, so this input was not sent.')


# Simulate pressing the Ctrl-V hotkey combination.
pydirectinput.hotkey('ctrl', 'v')


# Simulate typing the string 'Hello world!' with a 0.25 second pause in between each key press.
pydirectinput.typewrite('Hello world!', interval=0.25)


# By default, pydirectinput uses an artifical pause
# after every action to make input look less robotic.
# You can disable the pause on a per-function basis by passing in _pause=False, e.g
pydirectinput.moveTo(100, 150, _pause=False)


# The duration of the automatic pause is determinded by the PAUSE constant,
# which is 0.01 seconds by default, but can be adjusted to other values if desired.
pydirectinput.PAUSE = 0.1 # Set the pause to 0.1 seconds.


# You can also disable the pause globally by setting the PAUSE constant to None.
pydirectinput.PAUSE = None # Disable the pause entirely.


# You can also unicode_* variants of the keyboard functions to type unicode characters.
# Support may be limited in some applications.
pydirectinput.unicode_press('👍')


# On the other hand, if you already know the scancode of the key you want to press,
# you can use the scancode_* variants of the keyboard functions.
pydirectinput.scancode_press(0x3B) # Press the F1 key.
```

## Provided functions with same/similar signature to PyAutoGui:

* Informational:
  - `position()`
  - `size()`
  - `on_primary_monitor()` / `onScreen()`
  - `valid_screen_coordinates()`
  - `is_valid_key()` / `isValidKey()`
* Mouse input:
  - `moveTo()`
  - `move()` / `moveRel()`
  - `mouseDown()`
  - `mouseUp()`
  - `click()` and derivatives:
    - `leftClick()`
    - `rightClick()`
    - `middleClick()`
    - `doubleClick()`
    - `tripleClick()`
  - `scroll()` / `vscroll()`
  - `hscroll()`
  - `dragTo()`
  - `drag()` / `dragRel()`
* Keyboard input:
  - `keyDown()`
  - `keyUp()`
  - `press()`
  - `hold()` (supports context manager)
  - `write()` / `typewrite()`
  - `hotkey()`


### Additionally, keyboard input has been extended with :
* low-level scancode_* functions that allow integer scancode as arguments:
  - `scancode_keyDown()`
  - `scancode_keyUp()`
  - `scancode_press()`
  - `scancode_hold()` (supports context manager)
  - `scancode_hotkey()`
* higher-level unicode_* functions that allow inserting Unicode characters into supported programs:
  - `unicode_charDown()`
  - `unicode_charUp()`
  - `unicode_press()`
  - `unicode_hold()` (supports context manager)
  - `unicode_write()` / `unicode_typewrite()`
  - `unicode_hotkey()`


## Missing features compared to PyAutoGUI

- `logScreenshot` arguments. No screenshots will be created.

___

### Changelog compared to forked origin point PyDirectInput version 1.0.4:

* Adding/fixing extended key codes
* Adding flake8 linting
* Adding mypy type hinting and adding annotations (**This makes this fork Python >=3.7 only!**)
* Adding scroll functions based on [learncodebygaming/PR #22](https://github.com/learncodebygaming/pydirectinput/pull/22) and improve them
* Adding hotkey functions based on [learncodebygaming/PR #30](https://github.com/learncodebygaming/pydirectinput/pull/30) and improve them
* Adding more available keyboard keys
* Adding optional automatic shifting for certain keayboard keys in old down/up/press functions
* Adding additional arguments for tighter timing control for press and typewrite functions
* Adding Unicode input functions that allow sending text that couldn't be sent by simple keyboard
* Adding Scancode input functions that allow lower level access to SendInput's abstractions
* Adding support for multi-monitor setups via virtual resolution (most functions should work without just fine)
* Adding support for swapped primary mouse buttons
* Adding duration support for mouse functions
* Adding sleep calibration for mouse duration
* Adding automatic disabling of mouse acceleration for more accurate relative mouse movement
* Increase documentation
* Improve performance of _genericPyDirectInputChecks decorator (Thanks Agade09 for [reggx/PR #1](https://github.com/ReggX/pydirectinput_rgx/pull/1) and [reggx/PR #2](https://github.com/ReggX/pydirectinput_rgx/pull/2))

**This library uses in-line type annotations that require at least Python version 3.7 or higher and there are no plans to make the code backwards compatible to older Python versions!**


___
See [pydirectinput's original README](OLD_README.md).
___

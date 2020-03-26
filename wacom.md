## Useful xsetwacom settings

* Check for connected Wacom tablet:
  ```
  xsetwacom list devices
  ```
* Set Wacom tablet to "Relative" mode, so that it behaves like a normal mouse (as opposed to "Absolute" which is the default for the Wacom pen, where the pen's position corresponds to the absolute position on the screen):
  ```
  xsetwacom set <device ID> Mode "Relative"
  ```
* List xsetwacom options:
  ```
  xsetwacom list parameters
  ```

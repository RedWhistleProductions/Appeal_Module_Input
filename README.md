# Input

An Appeal module project.

## Development status

This repository was separated from the Appeal beta development tree. Its build and installer configuration will be modernized before the first standalone release.

Generated binaries and intermediate objects are intentionally excluded from version control.

## Game / GUI input focus

The SDL2 backend starts with Game focus. F1 or controller Back/View toggles it.
These controls are reserved for switching and are not gameplay bindings. Key
repeat never toggles focus repeatedly. Call `Input Update Default` each frame
even while the GUI has focus, so switching, device events, and releases are read.

| Command | Arguments | Result |
| --- | --- | --- |
| Set_Focus | Game or GUI | Select an input destination; invalid values raise an error |
| Toggle_Focus | none | Switch between Game and GUI |
| Focus | none | Return the string Game or GUI; use `(Input Focus)` in expressions |

In GUI focus, gameplay callbacks are suppressed and polled keyboard, controller,
mouse position, delta, wheel, and axis getters return neutral values. Raw input
state still updates; returning to Game does not invent a new Pressed event for
buttons already held. The focus-switch frame itself is consumed by the switch.
Gameplay may resume continuous movement for a physically held axis after that.

The SDL2 Graphics backend reads the shared SDL hint `APPEAL_INPUT_FOCUS`: only GUI
focus enables ImGui mouse, keyboard, and gamepad navigation. Game focus disables
GUI input and clears queued/held ImGui state. The GUI is hidden in Game focus and shown in GUI focus when GUI visibility is enabled. In GUI
focus, Escape is available to ImGui; in Game focus, Escape retains the demo's
close-window behavior. Graphics alone remains interactive when Input is absent.

Applications must clear their own latched movement/actions on focus changes.
The character demos demonstrate this with Character_Check_Focus; explicit GUI
buttons may still issue intentional application commands. No new shared Appeal
globals are published; Focus is a getter. This input focus is separate from
`Graphics Set_Focus`, which selects a graphics window.

Validation: `Tests/SDL2_Input_Events.ps1` includes virtual-controller focus tests;
`Tests/Input_Focus_Tests.ps1` checks the example's latched-state reset.


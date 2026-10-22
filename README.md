# Electronics Projects Documentation

A practical project log for two microcontroller builds:

1.  **Arduino Nano Buzzer Music Player with LCD Lyrics** --- plays a
    melody through a buzzer and displays synchronized lyrics on a 16×2
    character LCD.
2.  **TinyJoy --- ESP8266 Retro-Style Gamepad** --- a compact,
    retro-inspired handheld interface built around an ESP8266, an OLED
    display, and physical buttons.

> **Documentation note:** This README is based on the project details
> available from the build discussions. Confirmed wiring is listed as
> such. Where the exact TinyJoy hardware or final firmware was not
> established, the README marks it as a suggested/reference
> configuration rather than presenting it as a completed fact.

------------------------------------------------------------------------

# 1. Arduino Nano Buzzer Music Player with LCD Lyrics

## 1.1 Overview

This project combines a small speaker-style buzzer, an Arduino Nano, and
a 16×2 character LCD to create a simple music player. When the user
presses a button, the Arduino plays a programmed melody and updates the
LCD with the corresponding lyrics. Custom LCD characters can be used for
small visual details, such as heart symbols.

The build is intended as a low-cost embedded-systems project
demonstrating timing, digital input, sound generation, character
displays, and synchronization between two outputs.

## 1.2 Features

-   Plays a programmed melody through a buzzer.
-   Displays song lyrics on a 16×2 LCD.
-   Starts playback when the push-button is pressed.
-   Uses the Arduino's internal pull-up resistor for the button input.
-   Can display custom characters, such as hearts, using the LCD's
    CGRAM.
-   Does not require a potentiometer for LCD contrast in the documented
    wiring; the LCD contrast pin (VO) is connected to GND.

**Song discussed during development:** *Can't Take My Eyes Off You*.
Make sure any melody timings and lyric text in the sketch match one
another.

## 1.3 Hardware and why it is used

  -----------------------------------------------------------------------
  Component                           Purpose
  ----------------------------------- -----------------------------------
  Arduino Nano                        Main controller; runs the melody
                                      timing, reads the button, and
                                      updates the LCD.

  16×2 HD44780-compatible character   Displays two lines of text for the
  LCD                                 lyrics and status.

  Passive buzzer / piezo transducer   Produces musical notes when driven
                                      with different frequencies. A
                                      passive buzzer is required for
                                      playing different pitches.

  Momentary push-button               Provides a simple user input to
                                      start playback.

  Jumper wires and breadboard         Make connections during prototyping
                                      without soldering.

  USB 5 V supply                      Powers the Nano during development
                                      and testing.
  -----------------------------------------------------------------------

**Important:** The exact buzzer type was not confirmed in the available
notes. If it is an *active* buzzer, it may only produce its built-in
tone and will not reproduce a melody as accurately as a passive piezo
buzzer.

## 1.4 Pin connections

### Arduino Nano to 16×2 LCD (4-bit mode)

  -----------------------------------------------------------------------
  LCD pin                 Arduino Nano pin        Notes
  ----------------------- ----------------------- -----------------------
  RS                      D8                      Selects command or
                                                  character-data mode.

  EN                      D9                      Latches data into the
                                                  LCD.

  D4                      D4                      LCD data line.

  D5                      D5                      LCD data line.

  D6                      D6                      LCD data line.

  D7                      D7                      LCD data line.

  RW                      GND                     Keeps the LCD in
                                                  write-only mode.

  VO / V0                 GND                     Contrast connection
                                                  used in the documented
                                                  prototype; contrast may
                                                  vary by LCD module.

  A / LED+                5 V                     Backlight positive. Use
                                                  a current-limited
                                                  module or its onboard
                                                  resistor.

  K / LED−                GND                     Backlight negative.

  VSS                     GND                     Logic ground.

  VDD                     5 V                     LCD logic supply.
  -----------------------------------------------------------------------

### Other connections

  Component                Connection   Notes
  ------------------------ ------------ ----------------------------------------
  Buzzer signal            D12          Output used to generate musical tones.
  Buzzer ground            GND          Shared ground.
  Push-button terminal 1   D2           Configured as `INPUT_PULLUP`.
  Push-button terminal 2   GND          Pressing the button reads LOW.

### Button logic

With `pinMode(2, INPUT_PULLUP)`, the input is normally HIGH and becomes
LOW when the button connects D2 to GND. The sketch should therefore
detect a press using a LOW reading. This arrangement avoids needing a
separate external pull-up resistor.

## 1.5 How it works

1.  **Startup:** The Nano initializes the LCD and configures the button
    input and buzzer output.
2.  **Idle state:** The program waits for the button press.
3.  **Playback:** The sketch outputs note frequencies to D12 for
    specified durations.
4.  **Lyrics:** The LCD changes text at planned points in the song so
    the displayed lyric follows the melody.
5.  **End of song:** The buzzer is silenced and the program returns to
    its idle state, depending on the sketch's implementation.

A reliable implementation stores the melody notes, note durations, and
lyric timing separately or in clearly aligned structures. Their lengths
and indexes must be checked so that a note or lyric is not read beyond
the end of an array.

## 1.6 Software and libraries

-   **Arduino IDE or a compatible Arduino compiler** --- to edit and
    upload the sketch.
-   **`LiquidCrystal`** --- standard Arduino library for common parallel
    character LCDs.
-   **`tone()` / `noTone()`** --- Arduino functions for generating and
    stopping square-wave tones on a digital pin.
-   **`pitches.h` or an equivalent note-definition header** --- optional
    convenience definitions for note frequencies. The file must exist in
    the sketch folder or be installed as part of the chosen library.
-   **Custom LCD characters** --- `lcd.createChar(slot, bitmap)` can
    define up to eight custom characters at slots `0`--`7`. Do not use
    slot `8`; it is outside the valid range.

The build discussions also considered a TonePitch-style note library.
The exact library version is not confirmed, so the simplest portable
approach is the standard `tone()` API with a verified note-frequency
table.

## 1.7 Implementation guidance

-   Keep the melody and duration arrays the same length.
-   Decide whether each duration represents the full note length or the
    gap between notes, and implement that consistently.
-   Use `noTone(12)` when playback should stop.
-   Avoid long blocking delays if the lyrics need to update precisely.
    For a more responsive version, schedule note and lyric changes with
    `millis()`.
-   Debounce the push-button in software if a single press triggers
    playback more than once.
-   Test the LCD independently before debugging the melody.
-   If the LCD is blank, check 5 V, GND, VO contrast, and the
    RS/EN/data-pin mapping first.
-   If the buzzer makes only one fixed sound, confirm that it is a
    passive buzzer and that the sketch sends different frequencies.

## 1.8 Known wiring summary

``` text
Arduino Nano      16x2 LCD
------------      --------
5V          ----> VDD
GND         ----> VSS, RW, VO, K
D8          ----> RS
D9          ----> EN
D4          ----> D4
D5          ----> D5
D6          ----> D6
D7          ----> D7
5V          ----> A / LED+ (module-dependent current limiting)

Arduino Nano      Other parts
------------      -----------
D12         ----> Buzzer signal
GND         ----> Buzzer ground
D2          ----> Push-button terminal 1
GND         ----> Push-button terminal 2
```

## 1.9 Possible future improvements

-   Add pause, resume, and next-song controls.
-   Add a menu to select from multiple melodies.
-   Replace blocking delays with a non-blocking timing state machine.
-   Add a small animation or custom symbols between lyric lines.
-   Use an I²C LCD backpack in a future revision to reduce wiring; this
    is **not** the confirmed wiring for the current parallel-LCD build.

------------------------------------------------------------------------

# 2. TinyJoy --- ESP8266 Retro-Style Gamepad

## 2.1 Overview

**TinyJoy** is the working name for a compact, retro-style handheld
gamepad concept based on an ESP8266 microcontroller, a small OLED
display, and tactile buttons. The design direction is to keep the device
small and self-contained while providing a simple on-device interface
inspired by classic handheld consoles.

The available build notes confirm discussion of an ESP8266-based
standalone circuit with an OLED and buttons. They do **not** establish a
final display model, complete button layout, game list, enclosure, or
finished firmware. Those details are therefore left configurable below.

## 2.2 Intended features

-   Small, retro-inspired handheld form factor.
-   OLED interface for menus, game graphics, or status.
-   Physical buttons for navigation and gameplay.
-   ESP8266-based control logic.
-   Potential battery operation using a single-cell rechargeable
    lithium-ion battery and a suitable regulated supply.

The exact games and user-interface features depend on the firmware
written for the device; no particular game engine or finished game
library is assumed here.

## 2.3 Hardware and why it is used

  -----------------------------------------------------------------------
  Component                           Purpose
  ----------------------------------- -----------------------------------
  ESP8266 module or development board Main microcontroller. Provides GPIO
                                      for buttons and an I²C interface
                                      for the OLED.

  Small I²C OLED display              Displays menus, game elements, and
                                      status using only a few signal
                                      wires. The exact size/resolution is
                                      not confirmed.

  Momentary tactile buttons           Provide directional input and
                                      action/menu controls. The final
                                      number and layout should match the
                                      enclosure and firmware.

  Regulated 3.3 V power supply        Powers the ESP8266 safely and
                                      consistently.

  Single-cell 3.7 V Li-ion/LiPo       Provides portable power when a
  battery (optional)                  battery-powered version is built.

  Battery charging/protection circuit Handles charging and, where
  (if rechargeable)                   supported, battery protection.
                                      Choose a circuit suitable for the
                                      exact cell and charging source.

  Decoupling capacitors (recommended) Help reduce supply dips and noise
                                      near the ESP8266 during Wi-Fi
                                      current bursts.

  Enclosure and button caps           Give the device its handheld,
  (optional)                          retro-console feel and protect the
                                      electronics.
  -----------------------------------------------------------------------

## 2.4 Reference wiring

The following is a **reference configuration discussed during
development**, not a verified final TinyJoy pinout.

### I²C OLED

  ------------------------------------------------------------------------
  OLED signal               ESP8266 GPIO Common NodeMCU   Purpose
                                         label            
  ---------------- --------------------- ---------------- ----------------
  SDA                              GPIO4 D2               I²C data

  SCL                              GPIO5 D1               I²C clock

  VCC                Suitable supply for Depends on       Check the
                         the OLED module module           display board's
                                                          rated voltage
                                                          before
                                                          connecting.

  GND                                GND GND              Shared ground
  ------------------------------------------------------------------------

### Example button input

  Button signal            ESP8266 GPIO Connection
  ---------------------- -------------- ------------------------------
  One side of button             GPIO14 Configure as `INPUT_PULLUP`.
  Other side of button              GND Pressed state reads LOW.

This single-button mapping is an example from the earlier wiring
discussion; it is **not** a complete directional/action-button layout.
Select and document a separate GPIO for each additional button, then
ensure the selected pins do not interfere with ESP8266 boot-mode
requirements.

### ESP8266 boot-pin cautions

On common ESP8266 designs, GPIO0 and GPIO2 normally need to be HIGH and
GPIO15 normally LOW during boot. Avoid button wiring that forces these
pins into the wrong state during reset. Pin labels vary between bare
modules and development boards, so verify the exact board pinout before
wiring.

## 2.5 Power design and safety

The ESP8266 is a **3.3 V logic device**. Do not connect a raw 3.7 V
nominal Li-ion/LiPo cell directly to its 3.3 V supply pin.

For a battery-powered version:

1.  Select a regulator or buck-boost converter that provides a stable
    3.3 V output across the battery's discharge range.
2.  Size the supply for ESP8266 current peaks. A converter rated for at
    least 500 mA is a practical minimum; 800 mA--1 A provides more
    headroom.
3.  Place local decoupling near the ESP8266, for example a 10 µF
    capacitor plus a 100 nF ceramic capacitor, subject to the board's
    existing components and layout.
4.  Use a charger designed for the specific battery chemistry and cell
    configuration.
5.  Do not charge a lithium battery unattended or use a cell with
    damaged insulation. Avoid short circuits and ensure correct
    polarity.
6.  Check whether the development board already includes a regulator and
    USB charging circuit before adding external modules.

A charging board and a voltage regulator have different jobs: a charger
controls battery charging; a regulator supplies the voltage required by
the electronics. One module should not be assumed to do both unless its
specifications explicitly say so.

## 2.6 Firmware architecture

A clean TinyJoy firmware can be organized into the following layers:

-   **Input layer:** Reads each button, applies debouncing, and converts
    electrical states into logical actions.
-   **Menu/state layer:** Tracks whether the device is on the home
    screen, in a game, or showing a pause/settings screen.
-   **Game logic:** Updates positions, collisions, scores, or other
    rules for the selected game.
-   **Rendering layer:** Draws the current menu or game frame to the
    OLED.
-   **Power/status layer (optional):** Shows battery or connection
    information if the required hardware and measurements are available.

A simple main loop should read input, update the current state, and
redraw only what is necessary. Keeping input, game logic, and display
rendering separate makes the code easier to maintain as new games are
added.

## 2.7 Suggested software stack

The exact firmware and library choices have not been confirmed. Typical
options for an ESP8266 Arduino project include:

-   **Arduino IDE / ESP8266 Arduino core** --- compile and upload
    firmware to a supported ESP8266 board.
-   **`Wire`** --- I²C communication.
-   **A compatible OLED library** --- for example, Adafruit SSD1306 with
    Adafruit GFX, or another library matching the actual display
    controller and resolution.
-   **Button-handling code** --- direct GPIO reads with debouncing, or a
    button library if desired.

Install the libraries required by the selected sketch and verify the
OLED controller, resolution, and I²C address before choosing a display
library.

## 2.8 Bring-up and testing checklist

1.  Confirm the exact ESP8266 board/module and its pin labels.
2.  Test a minimal OLED sketch before adding game logic.
3.  Test each button independently and verify its pressed/released
    state.
4.  Check that the device still boots reliably with all buttons
    connected.
5.  Test the 3.3 V supply during startup and Wi-Fi activity, if Wi-Fi is
    used.
6.  Add the menu/state logic, then test one small game before adding
    more.
7.  Confirm battery charging, regulator output, and runtime separately
    before closing the enclosure.

## 2.9 Current documentation gaps

Fill these in when the final hardware is selected:

-   ESP8266 board/module model: **TBD**
-   OLED controller, resolution, and I²C address: **TBD**
-   Number and names of buttons: **TBD**
-   Final GPIO map: **TBD**
-   Battery capacity and power-converter model: **TBD**
-   Firmware repository / version: **TBD**
-   Games implemented and controls for each: **TBD**
-   Enclosure dimensions and fabrication method: **TBD**

------------------------------------------------------------------------

# 3. Shared engineering practices

Both projects are useful for learning the fundamentals of small embedded
systems:

-   **GPIO:** Buttons are digital inputs; the buzzer and LCD/OLED
    interfaces are controlled outputs.
-   **Timing:** Music playback and game loops depend on predictable
    timing.
-   **Human-machine interface:** The LCD and OLED communicate state to
    the user.
-   **Power integrity:** Microcontrollers and displays need stable
    supplies and a shared ground.
-   **Incremental testing:** Verify each component independently before
    integrating the whole system.
-   **Documentation:** Record exact board variants, pin mappings,
    library versions, and any changes made during assembly.

## General safety notes

-   Disconnect power before changing wiring.
-   Never assume two modules use the same logic voltage.
-   Do not drive loads that exceed a microcontroller pin's current
    rating directly.
-   Verify the pinout for the exact board revision; labels can differ
    between modules.
-   Check polarity and supply voltage before powering a newly assembled
    circuit.

------------------------------------------------------------------------

# 4. Project status

  -----------------------------------------------------------------------
  Project                 Details supported by    Details still to
                          the build notes         confirm
  ----------------------- ----------------------- -----------------------
  Arduino Nano LCD Music  Nano, 16×2 parallel     Exact buzzer model,
  Player                  LCD, buzzer on D12,     final sketch/library
                          start button on D2 to   version, exact melody
                          GND using               timings, final lyric
                          `INPUT_PULLUP`, LCD     sequence
                          mapping RS=D8, EN=D9,   
                          data D4--D7, lyrics and 
                          custom-character work   

  TinyJoy ESP8266 Gamepad ESP8266-based concept,  Exact board, OLED
                          OLED and buttons        model, full button map,
                          discussed, reference    final power circuit,
                          I²C wiring GPIO4/GPIO5, firmware, games and
                          example button on       enclosure
                          GPIO14, battery power   
                          requires regulated 3.3  
                          V                       
  -----------------------------------------------------------------------

## 5. Suggested repository layout

``` text
electronics-projects/
├── README.md
├── arduino-nano-lcd-music-player/
│   ├── README.md
│   ├── src/
│   ├── wiring/
│   └── media/
└── tinyjoy-esp8266/
    ├── README.md
    ├── src/
    ├── wiring/
    └── media/
```

Keep the top-level README as the overview and place project-specific
sketches, diagrams, photos, and detailed revisions in each project's
folder. Replace all `TBD` entries once the exact hardware and final code
are available.

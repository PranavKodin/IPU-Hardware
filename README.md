
# Electronics Projects

A collection of electronics projects I've been working on, exploring microcontrollers, embedded programming, hardware interfacing, and DIY device design.

## Projects

### 1. Arduino Nano Music Player with LCD Lyrics

A small music player built using an Arduino Nano, a buzzer, and a 16×2 LCD. The idea was to play a melody through the buzzer while displaying the song lyrics on the LCD in sync with the music.

The project combines sound generation, character display control, button input, and timing logic in a simple embedded system.

**Hardware used**
- Arduino Nano
- 16×2 character LCD
- Passive buzzer
- Push button
- Jumper wires and breadboard

**How it works**
- A button starts the playback.
- The Arduino generates musical notes through the buzzer.
- The LCD displays the corresponding lyrics as the song progresses.
- Custom LCD characters can be used to add small visual details to the display.

**What I learned**
- Interfacing an LCD with an Arduino in 4-bit mode
- Generating musical notes using `tone()`
- Handling button inputs with `INPUT_PULLUP`
- Synchronizing text and sound using timing logic
- Creating custom characters for an LCD

---

### 2. TinyJoy — ESP8266 Retro Gamepad

TinyJoy is a compact, retro-style gamepad project based on the ESP8266. The goal is to build a small handheld device with physical buttons and an OLED screen, inspired by classic portable gaming consoles.

The ESP8266 handles the input and device logic, while the OLED provides a small interface for menus, game graphics, and status information.

**Hardware used**
- ESP8266 development board
- Small OLED display
- Tactile push buttons
- Jumper wires
- Battery and power regulation circuit (for a portable version)

**Planned functionality**
- Compact handheld design
- Button-based controls
- OLED-based interface
- Retro-style menus and game graphics
- Portable battery-powered operation

**What I'm exploring**
- Reading multiple button inputs
- Driving an OLED display over I²C
- Structuring firmware for menus and game logic
- Managing power in a compact embedded device
- Designing a small, practical handheld enclosure

The final button layout, display specifications, and game features depend on the hardware and firmware implementation.

---

## Technologies & Concepts

- Arduino Nano
- ESP8266
- Embedded C/C++
- Arduino IDE
- GPIO and digital input/output
- LCD and OLED interfacing
- I²C communication
- Buzzer tone generation
- Basic power management

## Repository Structure

```text
electronics-projects/
├── README.md
├── arduino-nano-music-player/
│   ├── code/
│   └── images/
└── tinyjoy-esp8266/
    ├── code/
    └── images/
```

## About

These projects are part of my hands-on learning in electronics and embedded systems. I enjoy experimenting with microcontrollers, building things from individual components, and turning simple circuits into functional devices.

More projects and improvements will be added as I continue building and testing.


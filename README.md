# Auto-Helmet-Remainder-Bike-lock-system
How It Works (Step-by-Step)
Step 1: Helmet has a tag
A small RFID tag is fixed inside the helmet
This tag has a unique ID (like a digital identity)
 Step 2: Bike has a reader
The bike has an RFID reader near the handle
It constantly checks:
“Is the helmet nearby?”
 Step 3: Detection
When you wear the helmet and sit on the bike:
RFID reader detects the tag
Confirms helmet is present
 Step 4: Engine control
A microcontroller (like Arduino) is connected to:
RFID reader
Ignition system (through relay)
 Step 5: Final action
If helmet is detected →  Bike starts
If helmet NOT detected →  Bike will NOT start
 Step 1: Helmet Has a Tag
┌─────────────────────────────────────────────────────────────┐
│                        HELMET                                │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                                                      │    │
│  │                    ┌────────────┐                   │    │
│  │                    │  RFID TAG  │                   │    │
│  │                    │  (Inside)  │                   │    │
│  │                    │  ┌──────┐ │                   │    │
│  │                    │  │ UID: │ │                   │    │
│  │                    │  │A3F2  │ │                   │    │
│  │                    │  │B1C9  │ │                   │    │
│  │                    │  └──────┘ │                   │    │
│  │                    └────────────┘                   │    │
│  │                          │                          │    │
│  │                    (13.56 MHz)                    │    │
│  └─────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
What happens:

A passive RFID tag is embedded/sealed inside the helmet
The tag stores a unique 4-byte identification number
No battery needed - tag is powered by the reader's electromagnetic field
Example UID: A3:F2:B1:C9
 Step 2: Bike Has a Reader
┌─────────────────────────────────────────────────────────────┐
│                         BIKE                               │
│                                                              │
│   ┌──────────────┐           ┌──────────────┐            │
│   │   IGNITION   │           │  STARTER     │            │
│   │   SWITCH     │           │  MOTOR       │            │
│   └──────┬───────┘           └──────┬───────┘            │
│          │                          │                     │
│          │    ┌─────────────────┐   │                     │
│          │    │   RELAY         │   │                     │
│          └────┤  (Interlock)   ├───┘                     │
│               └─────────────────┘                         │
│                      ▲                                     │
│                      │                                     │
│               ┌──────┴──────┐                            │
│               │  MAIN PCB   │                            │
│               │  ┌────────┐ │                            │
│               │  │ARDUINO │ │                            │
│               │  │  MCU   │ │                            │
│               │  └────────┘ │                            │
│               │      │      │                            │
│               └──────┼──────┘                            │
│                      │                                     │
│               ┌──────┴──────┐                            │
│               │  RFID READER │                            │
│               │  (MFRC522)  │ ◄── Antenna               │
│               │     📡       │                            │
│               └─────────────┘                            │
│                      │                                     │
│              (Near Handlebar)                             │
└─────────────────────────────────────────────────────────────┘
What happens:

RFID reader module (MFRC522) is mounted near the handlebar/seat area
Reader constantly transmits electromagnetic waves at 13.56 MHz
MCU continuously monitors the reader for tag detection
 Step 3: Detection Process
┌─────────────────────────────────────────────────────────────┐
│                    DETECTION SEQUENCE                       │
│                                                              │
│  T=0ms   ┌─────────────────────────────────────┐            │
│          │ Rider approaches bike                │            │
│          │ (No helmet = No detection)           │            │
│          │  🔴 RED LED ON                      │            │
│          └─────────────────────────────────────┘            │
│                      │                                      │
│                      ▼                                      │
│  T=100ms  ┌─────────────────────────────────────┐            │
│           │ Rider puts on helmet                 │            │
│           │ Tag enters reader's electromagnetic   │            │
│           │ field (range: 0-5cm)                │            │
│           └─────────────────────────────────────┘            │
│                      │                                      │
│                      ▼                                      │
│  T=200ms  ┌─────────────────────────────────────┐            │
│           │ RFID Reader detects tag:             │            │
│           │                                      │            │
│           │   Reader: "I see something!"         │            │
│           │   Reader: "Let me read the UID..."   │            │
│           │   Reader: "Got it! UID = A3F2B1C9"   │            │
│           │                                      │            │
│           │  🟡 YELLOW LED BLINKS               │            │
│           └─────────────────────────────────────┘            │
│                      │                                      │
│                      ▼                                      │
│  T=400ms  ┌─────────────────────────────────────┐            │
│           │ Proximity sensor confirms helmet      │            │
│           │ is WORN (IR sensor detects face)     │            │
│           │                                      │            │
│           │   IR Sensor: "Something is close!"   │            │
│           │   ADC Value: 350 (< threshold 500)   │            │
│           │   Result: HELMET CONFIRMED WORN      │            │
│           │                                      │            │
│           │  🟢 GREEN LED SOLID                 │            │
│           └─────────────────────────────────────┘            │
│                                                              │
└─────────────────────────────────────────────────────────────┘
Detection Details:

Stage	Time	Event	Output
1	0ms	System powered	Green blinks (ready)
2	100ms	Helmet worn, tag in range	Yellow blinks (tag detected)
3	400ms	Proximity confirmed	Green solid (verified)
4	500ms	Ready to start	Fast green blink
 Step 4: Microcontroller Processing
┌─────────────────────────────────────────────────────────────┐
│                    SYSTEM ARCHITECTURE                      │
│                                                              │
│                    ┌─────────────────┐                      │
│                    │   ATmega328P    │                      │
│                    │      MCU        │                      │
│                    │                 │                      │
│                    │  ┌───────────┐  │                      │
│                    │  │ CPU CORE  │  │                      │
│                    │  └───────────┘  │                      │
│                    │        │        │                      │
│                    │  ┌───────────┐  │                      │
│                    │  │ STATE     │  │                      │
│                    │  │ MACHINE   │  │                      │
│                    │  └───────────┘  │                      │
│                    │        │        │                      │
│                    │  ┌───────────┐  │                      │
│                    │  │ WATCHDOG │  │                      │
│                    │  └───────────┘  │                      │
│                    └────────┬─────────┘                      │
│                             │                               │
│         ┌──────────────────┼──────────────────┐            │
│         │                  │                  │            │
│         ▼                  ▼                  ▼            │
│  ┌─────────────┐   ┌─────────────┐   ┌─────────────┐       │
│  │ RFID MODULE │   │ PROXIMITY  │   │ START BTN   │       │
│  │ (MFRC522)   │   │ SENSOR     │   │             │       │
│  │             │   │            │   │             │       │
│  │ Input: Tag  │   │ Input: IR  │   │ Input: Press│       │
│  │ UID data    │   │ reflection │   │ signal      │       │
│  └──────┬──────┘   └──────┬──────┘   └──────┬──────┘       │
│         │                  │                  │              │
│         │    ┌─────────────┼─────────────┐   │              │
│         │    │             │             │   │              │
│         │    │   DECISION  │   LOGIC     │   │              │
│         │    │             │             │   │              │
│         │    │ RFID OK? ───┼── PROX OK? ─┘   │              │
│         │    │      │      │      │           │              │
│         │    │      ▼      │      ▼           │              │
│         │    │  ┌───────┐  │  ┌───────┐       │              │
│         │    │  │ AND   │──┼──│ AND   │       │              │
│         │    │  │ GATE  │  │  │ GATE  │       │              │
│         │    │  └───┬───┘  │  └───┬───┘       │              │
│         │    │      │      │      │           │              │
│         │    │      └──────┼──────┘           │              │
│         │    │             ▼                  │              │
│         │    │       ┌──────────┐              │              │
│         │    │       │ RELAY    │              │              │
│         │    │       │ CONTROL  │              │              │
│         │    │       └────┬─────┘              │              │
│         │    │            │                   │              │
│         │    │            ▼                   │              │
│         │    │      ┌──────────┐              │              │
│         │    │      │  RELAY   │              │              │
│         │    │      │ ACTIVATED│              │              │
│         │    │      └──────────┘              │              │
│         │    │             │                  │              │
│         │    │             ▼                  │              │
│         │    │       ┌──────────┐              │              │
│         │    │       │ STARTER  │              │              │
│         │    │       │ ENGAGES  │              │              │
│         │    │       └──────────┘              │              │
│         │    │                                 │              │
│         └────┼─────────────────────────────────┘              │
│              │                                              │
└──────────────┼──────────────────────────────────────────────┘
 Step 5: Final Action (Relay Control)
┌─────────────────────────────────────────────────────────────┐
│                    RELAY CONTROL SEQUENCE                    │
│                                                              │
│  CONDITION CHECK:                                            │
│  ┌─────────────────────────────────────────────────────┐    │
│  │  ┌─────────┐     ┌─────────┐     ┌─────────────┐  │    │
│  │  │RFID TAG │ AND │PROXIMITY│ AND │START BUTTON │  │    │
│  │  │ DETECTED│     │CONFIRMED│     │   PRESSED   │  │    │
│  │  │  ✓ OK   │     │  ✓ OK   │     │    ✓ OK     │  │    │
│  │  └────┬────┘     └────┬────┘     └──────┬──────┘  │    │
│  │       │                │                  │          │    │
│  │       └────────────────┼──────────────────┘          │    │
│  │                        │                               │    │
│  │                        ▼                               │    │
│  │              ┌─────────────────┐                       │    │
│  │              │   ALL CONDITIONS  │                    │    │
│  │              │      MET ✓       │                    │    │
│  │              └────────┬────────┘                       │    │
│  │                       │                                │    │
│  │                       ▼                                │    │
│  │              ┌─────────────────┐                       │    │
│  │              │  RELAY ENGAGES  │                       │    │
│  │              │  (Door closes)  │                       │    │
│  │              └────────┬────────┘                       │    │
│  │                       │                                │    │
│  │                       ▼                                │    │
│  │              ┌─────────────────┐                       │    │
│  │              │  STARTER MOTOR  │                       │    │
│  │              │    ACTIVE       │                       │    │
│  │              │    🏍️ ENGINE   │                       │    │
│  │              │      STARTS     │                       │    │
│  │              └─────────────────┘                       │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                              │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                    RELAY WIRING DIAGRAM                      │
│                                                              │
│  BIKE BATTERY (+12V) ──────┐                               │
│                              │                               │
│                              ▼                               │
│                    ┌───────────────┐                        │
│                    │    FUSE      │ 2A                     │
│                    └───────┬───────┘                        │
│                            │                                 │
│                            ▼                                 │
│                    ┌───────────────┐                        │
│                    │   RELAY COIL  │                        │
│                    │   (Control)   │                        │
│                    └───────┬───────┘                        │
│                            │                                 │
│              ┌─────────────┼─────────────┐                 │
│              │             │             │                 │
│              │    MCU Signal              │                 │
│              │    (D4 pin)    │          │                 │
│              │       │        │          │                 │
│              │       ▼        │          │                 │
│              │   ┌────────┐   │          │                 │
│              │   │ RELAY  │   │          │                 │
│              │   │ COM    │───┼──────────┘                 │
│              │   └────────┘   │                 │           │
│              │       │       │                 │           │
│              │       ▼       │                 │           │
│              │   ┌────────┐   │    WHEN RELAY  │           │
│              │   │  NO    │───┼──── ENERGIZED: │           │
│              │   └────────┘   │    Path completes          │
│              │       │       │                 │           │
│              │       └───────┼─────────────────┘           │
│              │               ▼                               │
│              │        ┌───────────┐                        │
│              │        │  STARTER  │                        │
│              │        │  MOTOR    │                        │
│              │        └───────────┘                        │
│              │               │                             │
│              │               ▼                             │
│              │        ┌───────────┐                        │
│              │        │   ENGINE  │                        │
│              │        │   CRANKS  │                        │
│              │        └───────────┘                        │
│              │                                             │
│              └─────────────────────────────────────────────┘
│                                                              │
└─────────────────────────────────────────────────────────────┘
 What Happens If Helmet NOT Detected
┌─────────────────────────────────────────────────────────────┐
│                    REJECTION SEQUENCE                        │
│                                                              │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                                                      │    │
│  │   SCENARIO: Rider sits on bike WITHOUT helmet        │    │
│  │                                                      │    │
│  │   1. Ignition ON                                    │    │
│  │      └─► System powers up, Green blinks              │    │
│  │                                                      │    │
│  │   2. RFID Scanner sweeps...                          │    │
│  │      └─► No tag found!                               │    │
│  │      └─► 🔴 RED LED ON                              │    │
│  │      └─► 🔊 SHORT BEEP (warning)                     │    │
│  │                                                      │    │
│  │   3. Rider presses START button                     │    │
│  │      └─► MCU checks conditions...                    │    │
│  │      └─► RFID: FAIL ❌                               │    │
│  │      └─► PROXIMITY: FAIL ❌                          │    │
│  │                                                      │    │
│  │   4. RELAY REMAINS OPEN ⚡                          │    │
│  │      └─► Circuit NOT completed                      │    │
│  │      └─► Starter motor: NO POWER                     │    │
│  │                                                      │    │
│  │   5. Result:                                        │    │
│  │      ┌─────────────────────────────────────────┐   │    │
│  │      │                                          │   │    │
│  │      │     ❌ BIKE WILL NOT START               │   │    │
│  │      │                                          │   │    │
│  │      │     Message: "Wear your helmet!"          │   │    │
│  │      │                                          │   │    │
│  │      └─────────────────────────────────────────┘   │    │
│  │                                                      │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                              │
└─────────────────────────────────────────────────────────────┘
 Complete Flow Summary
                    ┌─────────────────┐
                    │   BIKE POWER   │
                    │     ON (12V)   │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │   MCU STARTS    │
                    │   INITIALIZING  │
                    └────────┬────────┘
                             │
              ┌───────────────┴───────────────┐
              │                               │
              ▼                               ▼
     ┌─────────────────┐            ┌─────────────────┐
     │ HELMET TAG FOUND │            │  NO TAG FOUND  │
     │    BY RFID       │            │    BY RFID     │
     └────────┬────────┘            └────────┬────────┘
              │                               │
              ▼                               ▼
     ┌─────────────────┐            ┌─────────────────┐
     │ RFID AUTH OK?   │            │  🔴 RED LED ON │
     │ Compare UID     │            │  WAIT...        │
     └────────┬────────┘            └─────────────────┘
              │
       ┌──────┴──────┐
       │             │
       ▼             ▼
┌──────────┐   ┌──────────┐
│  MATCH   │   │  MISMATCH│
│   ✓ OK   │   │    ✗     │
└────┬─────┘   └────┬─────┘
     │              │
     ▼              ▼
┌──────────┐   ┌──────────┐
│🟡 YELLOW │   │🔴 RED ON │
│  BLINK   │   │ BEEP x1  │
└────┬─────┘   └──────────┘
     │
     ▼
┌─────────────────┐
│ PROXIMITY CHECK │
│ IR Sensor OK?   │
└────────┬────────┘
         │
  ┌──────┴──────┐
  │             │
  ▼             ▼
┌────────┐ ┌────────┐
│  YES   │ │   NO   │
│ DETECT │ │DETECTED│
└───┬────┘ └───┬────┘
    │          │
    ▼          ▼
┌────────┐ ┌────────┐
│🟢 GREEN│ │ KEEP   │
│ SOLID  │ │ WAITING│
└────┬───┘ └───┬───┘
     │          │
     ▼          │
┌─────────────────┐
│START BUTTON     │
│PRESSED?        │
└────────┬────────┘
         │
  ┌──────┴──────┐
  │             │
  ▼             ▼
┌────────┐ ┌────────┐
│  YES   │ │   NO   │
│ PRESSED│ │PRESSED │
└───┬────┘ └───┬────┘
    │          │
    ▼          ▼
┌────────┐ ┌────────┐
│RELAY   │ │  IDLE  │
│ENGAGES │ │  🟢    │
│1 SECOND│ │ BLINK  │
└───┬────┘ └───┬────┘
    │          │
    ▼          │
┌────────┐     │
│STARTER │     │
│MOTOR   │     │
│CRANKS  │     │
└───┬────┘     │
    │          │
    ▼          ▼
┌────────┐ ┌────────┐
│  🚀    │ │BIKE IS │
│ENGINE  │ │ RUNNING│
│STARTED │ │ NORMALLY│
└────────┘ └────────┘
Key Safety Features
Feature	Protection
RFID Authentication	Only registered helmet tags work
Proximity Verification	Tag must be worn, not just nearby
Fail-Safe Design	System fails → Bike won't start
No Override	Cannot bypass without physical modification
Watchdog Timer	MCU crash → Auto-reset
⚡ Power Flow Diagram
    ┌─────────────────────────────────────────────────────────────┐
    │                    POWER DISTRIBUTION                        │
    │                                                              │
    │   BIKE BATTERY                                              │
    │   (+12V) ──────┐                                            │
    │                │                                            │
    │                ▼                                            │
    │   ┌─────────────────────┐                                   │
    │   │    FUSE (2A)        │  Protection                      │
    │   └──────────┬──────────┘                                   │
    │              │                                               │
    │              ▼                                               │
    │   ┌─────────────────────┐                                   │
    │   │   DIODE (1N4007)    │  Reverse polarity protection      │
    │   │   ◄──┤├             │                                   │
    │   └──────────┬──────────┘                                   │
    │              │                                               │
    │              ▼                                               │
    │   ┌─────────────────────┐                                   │
    │   │   CAPACITOR (1000µF)│  Filter/smoothing                 │
    │   │   ━━━━━━           │                                   │
    │   └──────────┬──────────┘                                   │
    │              │                                               │
    │              ▼                                               │
    │   ┌─────────────────────┐                                   │
    │   │   LM7805 VOLTAGE    │                                   │
    │   │   REGULATOR          │  12V → 5V                        │
    │   │   ┌───────────────┐ │                                   │
    │   │   │  IN    OUT    │ │                                   │
    │   │   │  ━━━━━   ═══  │ │                                   │
    │   │   │  GND          │ │                                   │
    │   │   └───────────────┘ │                                   │
    │   └──────────┬──────────┘                                   │
    │              │                                               │
    │              ▼ +5V                                           │
    │   ┌──────────┼──────────┬──────────┐                        │
    │   │          │          │          │                        │
    │   ▼          ▼          ▼          ▼                        │
    │ ┌────┐   ┌──────┐  ┌──────┐  ┌──────┐                     │
    │ │ MCU│   │ RFID │  │ RELAY│  │ LEDS │                     │
    │ │328P│   │MFRC52│  │DRIVER│  │+BUZZ│                     │
    │ └──┬─┘   └──┬───┘  └──┬───┘  └──┬───┘                     │
    │    │        │         │         │                          │
    │    └────────┴─────────┴─────────┘                          │
    │              GROUND (GND)                                    │
    │                                                              │
    └─────────────────────────────────────────────────────────────┘
How to Use:
1. Install Library:
Sketch → Include Library → Manage Libraries
Search: MFRC522 → Install
2. Wire Hardware:
Arduino    →    Module
D2         →    MFRC522 RST
D10        →    MFRC522 SDA
D11-D13    →    SPI pins
A0         →    IR Sensor
D4         →    Relay IN
D5-D7      →    LEDs
D3         →    Buzzer
3. Upload Code:
Open HelmetLockSystem.ino → Upload to Arduino
4. Get Your Tag UID:
Open Serial Monitor (9600 baud)
Tap your RFID tag
Note the UID (e.g., A3:F2:B1:C9)
5. Register Your Helmet:
// Edit line in HelmetLockSystem.ino:
#define REGISTERED_TAG {0xA3, 0xF2, 0xB1, 0xC9}  // Change to your UID
6. Upload & Test!
Sections Implemented
Section	Content
Section 1	Configuration parameters (pins, timing, thresholds)
Section 2	State machine definitions (INIT, IDLE, TAG_FOUND, AUTH_OK, PROX_OK, READY, STARTING, FAULT)
Section 3	Error codes (RFID, proximity, relay, watchdog)
Section 4	Global variables and objects
Section 5	Setup function
Section 6	Initialization functions
Section 7	Main loop
Section 8	RFID detection & authentication
Section 9	Proximity sensor reading
Section 10	State machine logic
Section 11	Starter relay control
Section 12	LED indicators
Section 13	Audio feedback
Section 14	Utility functions & debug
State Machine (8 States)
INIT → IDLE → TAG_FOUND → AUTH_OK → PROX_OK → READY → STARTING
         ↓
      FAULT
Safety Features (Per Specification)
Feature	Implementation
Fail-Safe	Relay OPEN when powered off
Watchdog	4-second auto-reset
Low Voltage Protection	Battery monitoring
No Override	Code modification required
Tamper Resistant	Authentication required
 Quick Start
// 1. Install MFRC522 library
//    Sketch → Include Library → Manage Libraries → Search "MFRC522"

// 2. Wire hardware (see code header)

// 3. Upload code to Arduino

// 4. Open Serial Monitor (9600 baud)
//    Scan your tag - note the UID

// 5. Edit line 73:
const byte REGISTERED_UID[4] = {0xA3, 0xF2, 0xB1, 0xC9}; // Your UID

// 6. Upload again
LED Status CodesState	Green	Yellow	Red
                IDLE	 Blink	-	-
                TAG_FOUND	-	 Blink	-
                AUTH_OK	-	 Slow	-
                PROX_OK	-Solid	-	-
                READY- Fast	-	-
                FAULT	- -	-	 Fast

# Hardware
For any useful application, interfacing with some external hardware is
necessary.  This requires an asynchronous interface at some level, even if it is
wrapped by blocking APIs by the OS.  Without worrying about external hardware it
is possible to write code that's purely functional and deterministic without
side-effects or global state.

So now we come to the question; How should hardware APIs be designed?  There are
many different currently existing APIs, but we want something that (almost)
everyone can agree on - something simple and functional.  The APIs should be
similar to each other so it's easier to learn others once you learn one.

## Proposed Design
The APIs shall all be asynchronous (can be wrapped by to become synchronous).
The main type provided by the API is called `Connector`.  Connectors can only be
initialized once, and not copied.  These decisions are made for the best runtime
performance and safety.  All of the APIs shall be written as WAT (WebAssembly
Text Format) because the APIs must be cross-platform and not specific to
language.

All APIs are always available, although if the container doesn't want to expose
specific hardware capabilities - it doesn't have to provide them through the
connector.  This could be useful for plugin techonlogy.  The OS/DE should always
expose all hardware capabilities to the programs it runs.

All `Connector`s should have a default connection that is either the composite
of all devices (for `Keys` - combines all keyboard and mouse buttons), or the
preferred user-selected device (for `Speakers` - changes to headset if plugged
in, otherwise built-in speakers).

## API
The "syscall" API consists of only 2 simple functions.

### Connector
This function returns future type `Connector`.

```wat
(import "ardaku" "connector" (func $connector
    (param $hardware_id i32)
    (result i32)
))
```

This is a simple function that requests a connector for a specific type of
hardware.  The available hardware IDs are:

 - `0x00000000`: Text (Keyboard UTF-8 Input / stdin or GUI)
 - `0x00000001`: Pointer (Mouse or Touchscreen)
 - `0x00000002`: Gamepad (W3 Standard Gamepad Compatible Device)
 - `0x00000003`: Flightstick (Flight Controller Device)
 - `0x00000004`: Wheel (XY Scroll-Wheel or XY Track-Wheel)
 - `0x00000005`: Keys (Mouse Buttons, Keyboard Keys)
 - `0x00000006`: Instrument (MIDI Device - Musical Keyboard, etc.)
 - `0x00000007`: Controller (Specialized Hardware Device - Remote, etc.)
 - `0x00000008`: Screen (Video Output - Monitor, Phone/Laptop/Tablet Screen)
 - `0x00000009`: Camera (Video Input - Webcam, Selfie-Camera etc.)
 - `0x0000000A`: Speaker (Audio Output)
 - `0x0000000B`: Microphone (Audio Input)
 - `0x0000000C`: BumpMap (Textural Output)
 - `0x0000000D`: FingerprintReader (Textural Input)
 - `0x0000000E`: Rumble (Haptic Output)
 - `0x0000000F`: Motion (Haptic Input - Accelerometer)
 - `0x00000010`: Printer (Image Output)
 - `0x00000011`: Scanner (Image Input)
 - `0x00000012`: Flash (Output: Single Light)
 - `0x00000013`: Light (Input: Light Level Sensor)
 - `0x00000014`: Display (Output: Alternative Display/Lights)
 - `0x00000015`: Location (Input: GPS)
 - `0x00000016`: Eject (Output: Drive Eject)
 - `0x00000017`: Position (Input: Angle)
 - `0x00000018`: Filesystem (Hard-Drive, Solid-State, Flash Drive, CD/DVD, etc.)
 - `0x00000019`: Pins (GPIO)
 - `0x0000001A`: Timer (Real-time timer)
 - `0x0000001B`: Clock (Date-Time-Time of Day)
 - `0x0000001C`: Debug Log (Standard Out)
 - `0x0000001D`: Hardware Info
 - `0x0000001E`: GUI HeaderBar (Out: Set HeaderBar widgets)
 - `0x0000001F`: Preferences (In: Get User Preferences - Language, Theme, etc.)
 - `0x00000020`: Broadcast (Accept Clients - HTTP/TCP)
 - `0x00000021`: Request (Connect To Server - HTTP/TCP)
 - `0x00000022`: Broadcast (Accept Clients - WebSocket/TCP)
 - `0x00000023`: Request (Connect To Server - WebSocket/TCP)
 - `0x00000024`: Broadcast (Accept Clients - RTCDataChannel/UDP)
 - `0x00000025`: Request (Connect To Server - RTCDataChannel/UDP)
 - `0x00000026`: Broadcast (Accept Clients - Bluetooth)
 - `0x00000027`: Request (Connect To Server - Bluetooth)

If you try to make more than one connector of the same type, the program shall
abort.  Make sure to verify this is not the case before running.  Since there is
a finite amount of hardware, it is never free'd; if you unplug something and
plug it back in it shall use the same future ID.

### Event
Change the I/O Buffer location for a future.

```wat
(import "ardaku" "event" (func $event
    (param $future i32)
    (param $bufref i32)
))
```

This function is required to actually receive or send data from/to hardware.  It
only needs to be called once, but must be called once.  This function forces a
poll, and may be necessary if the future has been suspended (for instance in the
log future, setting length to 0 suspends the almost-always-ready future).

## Example
Get textual input connector.  In Ardaku, no items are exported - everything is
an import.

```wat
(module
  ;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
  ;; Imports From The Ardaku Operating System ;;
  ;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

  ;; Import memory pages.
  (import "ardaku" "memory" (memory 1))

  ;; Import funcref table
  (import "ardaku" "table" (table 1 funcref))

  ;; Import function: ardaku.connector(i32)
  ;;
  ;; Takes hardware id and returns non-zero 32-bit future id.  Future ids are
  ;; guaranteed to be consecutive, execept that discarded future ids may be
  ;; re-used.
  (import "ardaku" "connector" (func $connector
    (param $hardware_id i32)
    (result i32)
  ))

  ;; Import function: ardaku.event(i32, i32)
  ;;
  ;; Enable event callback for a future with a reference to the scratch buffer.
  ;; You can pass around user data by using a negative offset of the $bufref.
  (import "ardaku" "event" (func $event
    (param $future i32)
    (param $bufref i32)
    (result i32)
  ))

  ;;;;;;;;;;;;;;;;;;;;;;;;;;
  ;; Set Future Callbacks ;;
  ;;;;;;;;;;;;;;;;;;;;;;;;;;

  ;; Set future callbacks
  (elem (i32.const 0)
    $init ;; 0 Initialization (application start future)
    $text ;; 1 Text
    $log ;; 2 Log
  )

  ;; Define initialization callback function, $user_data will always be 0 for
  ;; initialization
  (func $init (param $user_data i32)
    ;; @1: Create connector for text input.
    (call $connector (i32.const 0x00))

    ;; @2: Create connector for log output.
    (call $connector (i32.const 0x1C))

    ;; Set input buffer to @0
    (call $event (i32.const 1) (i32.const 0))
  )

  ;; Define text callback function
  (func $text (param $user_data i32)
    ;; Write output buffer to the log (same as input buffer @0).
    (call $event (i32.const 2) (i32.const 0))
  )

  ;; Once the log is ready (finished logging or never logged), then it must be
  ;; stopped to avoid an infinite loop.
  (func $log (param $bufref i32)
    (i32.store 0 (i32.const 0)) ;; Set text length to 0
  )
)
```

## Speakers
```
len: u32
frames: [[f32; 8]; len]
```

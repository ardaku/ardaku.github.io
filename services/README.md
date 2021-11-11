# Services
Rather than use shared object libraries, Ardaku uses services that can be
communicated to with sockets.  Services should only be used for large complex
tasks that are difficult to implement and needed by multiple programs.  Simple
tasks that don't take up much space should be statically linked.

## Usage
```kuru
import ardaku; connect request Service Voice Speech
import audio; Audio

fn synthesize_audio(text Text) -> Audio {
    let voice: request(Service.TextToSpeech, Voice().pitch(440).speed(0.2))
        .await()
    request(Service.TextToSpeech, Speech(voice).say(text))
        .await()
}

fn() {
    let audio: synthesize_audio("Hello, world")
    # FIXME: Play the audio
}
```

Internally, the two "syscalls" are
 - `request(message List[IntU32], future_idx IntU32)`
 - `await() -> (future_idx IntU32, message List[IntU32])`

Requests / futures are completion-based (like Windows), and can not be canceled.
This is done to keep the API simple.  The actual "completion" might not involve
actual rendering of graphics or playback of audio, because double-buffering will
be used.  It's up to the application to keep track of the list of function
pointers to call by index (alternatively, integer comparison may be used).

 - Request with empty message (0 length, null pointer), and future_idx set to 0
   exits
 - Request with empty message (0 length, null pointer), and future_idx set to 1
   suspends (for debugger)
 - Request with message at least 1 length:
   0. WASM Dynamic Plugin service
   1. Hardware/Logging/File I/O service
   2. Audio/Voice Synthesis service
   3. Audio/Voice Recognition service
   4. Translation/Localization service
   5. User Interface service
   6. Document Rendering service
   7. Physics/Simulation/Game Engine service
   8. Neural Network Engine service
   9. Compression service
   10. Multimedia Transcoder service
   11. SSL/Encryption service
   12. 2D/3D Video Special Effects service
   13. Audio Special Effects service
   14. Visual Recognition service
   15. Service Configuration service

   Services 16+ are user-defined services starting with web server.  The top 4
   bits are Ardaku version #, currently must be zero.

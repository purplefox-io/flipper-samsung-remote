# Flipper Zero Samsung TV Remote

This is a custom Samsung TV remote application for the Flipper Zero, designed to demonstrate how to build a custom user interface and send specific, hard-coded infrared (IR) commands.

The application features a unique vertical layout, intended for use when holding the Flipper sideways so the IR port can be pointed at a TV like a traditional remote.

![Flipper Samsung Remote](https://purplefox.io/images/flipper_samsung_remote.png)

## Features

* **Custom Graphical UI:** A complete, custom-built user interface with buttons, icons, and a selection highlight.
* **Rotated Vertical Layout:** The UI is rotated +90 degrees to be used like a conventional remote control.
* **Remapped Directional Input:** The physical D-pad is re-mapped to provide an intuitive navigation experience in the rotated orientation.
* **Visual & Haptic Feedback:** The Flipper's LED blinks purple and the device vibrates briefly every time an IR command is sent.
* **Hard-coded IR Signals:** The application does not rely on `.ir` files. Instead, it sends specific Samsung IR protocols directly from the code, serving as a clear example for developers.

## Example: Sending a Custom IR Command

This application is a great example of how to send specific, known IR commands directly in your C code without needing to load external `.ir` files. The core logic is handled in the `send_ir_code` function.

Here's a breakdown of how the "Power" button command is sent:

```c
static void send_ir_code(TvButton button, NotificationApp* notifications) {
    // ... (feedback notifications) ...

    uint8_t address = 0x07; // Samsung address is typically 0x07
    uint8_t command;

    switch(button) {
    case Button_Power:
        command = 0x02; // The specific hex code for the Power command
        break;
    // ... other cases ...
    }

    // 1. Allocate memory for an InfraredSignal
    InfraredSignal* signal = infrared_signal_alloc();
    furi_check(signal);

    // 2. Create an InfraredMessage with the correct protocol, address, and command
    InfraredMessage message = {
        .protocol = InfraredProtocolSamsung32, // The protocol for Samsung TVs
        .address = address,
        .command = command,
        .repeat = false
    };

    // 3. Assign the message to the signal structure
    infrared_signal_set_message(signal, &message);

    // 4. Transmit the signal
    infrared_signal_transmit(signal);

    // 5. Free the allocated memory
    infrared_signal_free(signal);

    // ... (delay and LED reset) ...
}
```
This demonstrates the fundamental process: define the protocol, address, and command, then use the `infrared_signal` library to build and transmit the signal.

## Building the Application

**Place Files**: Place the application source code (`samsung.c`, `infrared_signal.c`, `infrared_signal.h`, etc.) and the images folder into a directory within your Flipper Zero firmware's applications_user folder.

**Build**: Navigate to the root of the firmware directory and run the build command:

```bash
./ufbt launch
```

Author
Tyler Berndt

Website: https://purplefox.io/blog/flipper-samsung-remote

GitHub: @purplefox-io

License
This project is licensed under the MIT License - see the LICENSE.md file for details.

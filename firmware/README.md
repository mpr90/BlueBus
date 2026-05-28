## BlueBus Firmware Application Breakdown

**BlueBus** is a Bluetooth interface module for BMW vehicles (E38-E85 platforms) that emulates a CD changer on the vehicle's I-Bus to provide seamless Bluetooth audio, hands-free telephony, and vehicle integration.

### **Architecture Overview**

The firmware runs on a **PIC24FJ MCU** with three main communication interfaces:

1. **Bluetooth Module (BM83)** - Handles device pairing, A2DP audio streaming, HFP telephony
2. **I-Bus** - Vehicle diagnostic/control network via TH3122 transceiver
3. **System UART** - Debug/diagnostic console

### **Core Components**

**Main Loop (`main.c`)**
- Initializes PIC24 hardware (GPIO ports, peripherals)
- Sets up three UART modules for Bluetooth, I-Bus, and System communications
- Initializes audio components (PCM5122 DAC, WM8804 receiver)
- Runs event-driven processing loop: `BTProcess()`, `IBusProcess()`, timers, CLI

**Handler Layer (`handler/`)**
- **handler_bt.c** - Processes Bluetooth events (connections, metadata, playback)
- **handler_ibus.c** - Processes I-Bus messages (vehicle buttons, diagnostics, climate)
- **handler_common.c** - Shared state machine logic for both interfaces

**Libraries (`lib/`)**
- **bt/** - Bluetooth abstraction layer (BM83 & BC127 support)
- **ibus.c** - I-Bus protocol parser (41+ device types, message routing)
- **uart.c** - UART management with interrupt-driven buffering
- **i2c.c** - I2C bus for audio chip configuration
- **timer.c** - Millisecond timer with scheduled task support
- **config.c** - EEPROM settings management
- **event.c** - Simple publish-subscribe event system

**UI Layer (`ui/`)**
- **bmbt.c** - Graphical menu interface (on-board navigation display)
- **mid.c** - Single-line text display (multi-info display)
- **cd53.c** - CD radio emulation (11-char single line)
- **cli.c** - Serial debug console

### **Key Features**

✅ **Bluetooth Audio** - A2DP streaming with AVRCP metadata display  
✅ **Hands-Free Calls** - HFP profile, caller ID, voice commands  
✅ **Vehicle Control** - Steering wheel button integration, comfort features  
✅ **Diagnostics** - On-board computer data display, temperature sensors  
✅ **Firmware Updates** - USB-based upgrade process with version checking  

### **Data Flow**

```
Vehicle Buttons (I-Bus) ──→ Handler (IBus) ──→ Bluetooth Commands
                              ↑
                         Handler State
                              ↓
Bluetooth Events ────────→ Handler (BT) ──→ I-Bus Display Updates
```

The handler maintains a shared context with device status, connection state, volume levels, and UI mode preferences, coordinating bidirectional communication between the vehicle and connected Bluetooth devices.
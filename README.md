# Sa7SA7: ESP32 Wake-Up Alarm

An ESP32 alarm clock that is hard to ignore. When the alarm goes off, you have 30 seconds to wake up and turn a rotary encoder 10 full turns to disarm it. If you don't, a pump kicks in.

## How it works

The firmware is a small state machine:

| State | What happens |
|-------|--------------|
| `SET_ALARM` | Turn the encoder to change the alarm time. Click to switch between hour and minute. Hold for 1.5 s to arm. |
| `ARMED` | Waits for the RTC to reach the alarm time. Click to cancel and go back to setting. |
| `ALARM` | 30 s countdown. The buzzer and the OLED flash speed up as time runs out. The servo follows your distance from the ultrasonic sensor. Turn the encoder 10 turns (either direction) to disarm. |
| `PUMP` | Countdown expired: the buzzer stops, the OLED turns off, and the relay runs the pump for 5 s. |
| `COMPLETE` | Shows "DONE" for 3 s, then returns to `SET_ALARM`. |

## Hardware

- ESP32 dev board
- DS3231 RTC module (I2C)
- SSD1306 128x64 OLED (I2C, address `0x3C`)
- Rotary encoder with push button
- Active buzzer
- Relay module + pump
- HC-SR04 ultrasonic sensor
- Servo motor

### Pinout

| Component | ESP32 pin |
|-----------|-----------|
| Encoder A | 18 |
| Encoder B | 19 |
| Encoder button | 5 |
| Buzzer | 27 |
| Relay (pump) | 25 |
| Ultrasonic TRIG | 16 |
| Ultrasonic ECHO | 17 |
| Servo signal | 13 |
| I2C SDA (OLED + RTC) | 21 |
| I2C SCL (OLED + RTC) | 22 |

## Software setup

1. Install the [Arduino IDE](https://www.arduino.cc/en/software) with ESP32 board support.
2. Install these libraries from the Library Manager:
   - `RTClib` (Adafruit)
   - `Adafruit GFX Library`
   - `Adafruit SSD1306`
   - `ESP32Servo`
3. Open `FinalProjectCode/FinalProjectCode.ino`, select your ESP32 board and port, and upload.
4. Open the Serial Monitor at 115200 baud to see status messages.

**Note:** if the RTC has lost power, the sketch sets it to the compile time on boot. Upload right before use, or the alarm will be based on a wrong clock.

## Tuning

Constants at the top of the sketch control the behavior:

- `ALARM_TIME`: how long you have to disarm (ms)
- `PUMP_TIME`: how long the pump runs (ms)
- `TURNS_REQUIRED`: encoder turns needed to disarm
- `MIN_DISTANCE` / `MAX_DISTANCE` and `MIN_ANGLE` / `MAX_ANGLE`: ultrasonic range to servo angle mapping

## Photos / wiring diagram

_Add pictures of the build here._

ESP32 Bluetooth Audio Sink with 2004 LCD
========================================

Based on esp-idf bluetooth A2DP audio sink example

### Hardware Required

To play the sound, there is a need of loudspeaker and possibly an external I2S codec. Otherwise the example will only show a count of audio data packets received silently. Internal DAC can be selected and in this case external I2S codec may not be needed.

For the I2S codec, pick whatever chip or board works for you; this code was written using a MAX98357A, but other I2S boards and chips will probably work as well. The default connections are shown below, but these can be changed in menuconfig:

| ESP pin   | signal   | 
| :-------- | :------- | 
| GPIO22    | LRCK     | DAC
| GPIO25    | DATA     | DAC
| GPIO26    | BCK      | DAC
| GPIO18    | SDA      | LCD 
| GPIO19    | SCL      | LCD

If the internal DAC is selected, analog audio will be available on GPIO25 and GPIO26. The output resolution on these pins will always be limited to 8 bit because of the internal structure of the DACs.

The DAC can be connected to 3.3V or 5V but the LCD must be powered by 5V.

Example with PCM5102A stereo DAC:
![PCM5102A example](https://github.com/NickTulett/esp32_bluetooth_2004/blob/master/bluetooth2004.jpg)

### Configure the project

```
make menuconfig
```

* Set serial port under Serial Flasher Options.

* Set the use of external I2S codec or internal DAC for audio output, and configure the output PINs under A2DP Example Configuration

* Enable Classic Bluetooth and A2DP under Component config --> Bluetooth --> Bluedroid Enable

* set the LCD row (default 4) and column (default 20) counts under "esp32-i2c-lcd1602-example Configuration"

### Build and Flash

Build the project and flash it to the board, then run monitor tool to view serial output.

```
make -j4 flash monitor
```

(To exit the serial monitor, type ``Ctrl-]``.)

## Example Output

After the program is started, the example starts inquiry scan and page scan, awaiting being discovered and connected. Other bluetooth devices such as smart phones can discover a device named "ESP_SPEAKER". A smartphone or another ESP-IDF example of A2DP source can be used to connect to the local device.

Once A2DP connection is set up, there will be a notification message with the remote device's bluetooth MAC address like the following:

```
I (106427) BT_AV: A2DP connection state: Connected, [64:a2:f9:69:57:a4]
```

If a smartphone is used to connect to local device, starting to play music with an APP will result in the transmission of audio stream. The transmitting of audio stream will be visible in the application log including a count of audio data packets, like this:

```
I (120627) BT_AV: A2DP audio state: Started
I (122697) BT_AV: Audio packet count 100
I (124697) BT_AV: Audio packet count 200
I (126697) BT_AV: Audio packet count 300
I (128697) BT_AV: Audio packet count 400

```

Also, the sound will be heard if a loudspeaker is connected and possible external I2S codec is correctly configured. For ESP32 A2DP source example, the sound is noise as the audio source generates the samples with a random sequence.

The LCD will display the artist, album and track title if available with play and stop icons to show the state of the player.

## Troubleshooting
* For current stage, the supported audio codec in ESP32 A2DP is SBC. SBC data stream is transmitted to A2DP sink and then decoded into PCM samples as output. The PCM data format is normally of 44.1kHz sampling rate, two-channel 16-bit sample stream. Other decoder configurations in ESP32 A2DP sink is supported but need additional modifications of protocol stack settings.
* As a usage limitation, ESP32 A2DP sink can support at most one connection with remote A2DP source devices. Also, A2DP sink cannot be used together with A2DP source at the same time, but can be used with other profiles such as SPP and HFP.

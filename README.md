# ESP32 and ESP8266 Arduino as (X)SVF JTAG programmer

Works as standalone SVF or XSVF JTAG programmer
for FPGA devices. This one should be better than my 
[wifi_jtag](https://github.com/emard/wifi_jtag). 
Reads file from onboard SPI flash chip at ESP8266
(it can hold 3MB) to program the target JTAG device.
I tested it on my Lattice XP2.

It is small fork of Clifford Wolf's [Lib(X)SVF](http://www.clifford.at/libxsvf/),
with minimal adaptation to get it working with ESP8266.
Arduino ESP8266 Library wrapper is added with proper SPIFFS
buffering and a small bugfix to original "svf.c" is done
to accept decimal floats, e.g. it accepts now numbers
like 1.00E-02 while original library would throw syntax error or
accept only 1E-02

# Install

Close arduino

    cd ~/Arduino/libraries
    git clone https://github.com/emard/LibXSVF

Some additional dependencies are need, this list may not 
be complete or some apply for ESP32 or ESP8266, so if
you see error, find some solution....

    AsyncTCP
    ESPAsyncTCP
    ESPAsyncWebServer (ESP32)
    FSBrowserNG (ESP8266)
    NtpClient (ESP8266)
    Time (ESP8266)

Start arduino, open examples->LibXSVF

# Upload bitstream file to SPI flash

ESP8266 (examples/prog)

In the sketch example, you can place file in 
"/data/bitstream.svf" and click 
Tools->[ESP8266 Sketch Data Upload](https://github.com/esp8266/arduino-esp8266fs-plugin),
or get some cool Web server like [FSBrowserNG](https://github.com/gmag11/FSBrowserNG) which can already
upload any file to "/bitstream.svf" using web browser.

ESP32 (examples/websvf)

Process packetized SVF stream from web file upload.
Packets need to come in sequential order.
Open page esp32.lan/upload.htm
navigate to SVF file and click upload. 2MB SVF file
uploads in 12 seoonds.
ESP32 needs to buffer each command, remember to
limit command size at SVF file generation.
Lattice example:

    ddtcmd  -oft -svfsingle -revd -maxdata 8 -if bitstream.xcf -of bitstream.svf

# JTAG PINOUT

It's currently "hidden" in xsvftool-esp8266.c
You can edit this file and recompile sketch 
to change pinout.

    #if ESP9266
    #define TCK 14 // NodeMCU D5
    #define TMS  5 // NodeMCU D1
    #define TDI 13 // NodeMCU D7
    #define TDO 12 // NodeMCU D6
    #endif

    #if ESP32
    #define TCK 18
    #define TMS 21
    #define TDI 23
    #define TDO 19
    #endif

# TODO

    [ ] fix memory leaks, if WEB TCP connection is broken,
        it won't free() what it had malloc()'d
    [ ] make first page be aupload page
    [ ] SD card support
    [ ] OLED and buttons support

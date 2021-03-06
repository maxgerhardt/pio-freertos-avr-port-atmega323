# pio-freertos-avr-port-atmega323

## Description

This is the PlatformIO-compilable version of https://github.com/FreeRTOS/FreeRTOS/tree/main/FreeRTOS/Demo/AVR_ATMega323_WinAVR.

## How was this project created?

* Created lib/FreeRTOS folder
* downloaded current version of https://github.com/FreeRTOS/FreeRTOS-Kernel
* put all *.c files in the top-level directory of FreeRTOS-Kernel (croutine.c, tasks.c, ..) in lib/FreeRTOS
* out all *.h files from FreeRTOS-Kernel/include in lib/FreeRTOS
* put FreeRTOS-Kernel/portable/MemMang/heap_1.c in lib/FreeRTOS since that is what is referenced in the demo
* chose a FreeRTOS port as https://github.com/FreeRTOS/FreeRTOS-Kernel/tree/main/portable/GCC/ATMega323
* copy files from generic ATMega port (https://github.com/FreeRTOS/FreeRTOS-Kernel/tree/main/portable/GCC/ATMega323) into lib/FreeRTOS
* copied the ATMega323 demo code https://github.com/FreeRTOS/FreeRTOS/tree/main/FreeRTOS/Demo/AVR_ATMega323_WinAVR into `src/`
* copied the common demo code include files and select source files (which are referenced in the `makefile`) from https://github.com/FreeRTOS/FreeRTOS/tree/main/FreeRTOS/Demo/Common/include and https://github.com/FreeRTOS/FreeRTOS/tree/main/FreeRTOS/Demo/Common/Minimal into `lib/FreeRTOS-Demo-Common`
* adapted the `platformio.ini` regarding the critical parameters
     * dynamically changed microcontroller to the one supported by the code: ATMega323.
        * the serial etc. code will *not* work for an Uno or Mega2560 due to referencing the wrong registers.
     * added `src/` to the global include path. This is where `FreeRTOSConfig.h` lives and it should always be found.
     * added `lib_archive = no` just to be sure that interrupt-handlers are linked correctly. This is mostly an issue on ARM-type devices, but it's never bad to make sure.
* fixed `lib/FreeRTOS/port.c`:
    * interrupt vectors must be marked as `used`, otherwise they are discarded and a linking error occurs.
    ```c
        //added ", used"
    	void TIMER1_COMPA_vect( void ) __attribute__ ( ( signal, naked, used ) );
    ```
* fixed `src/regtest.c`: prefixed `portBASE_TYPE xRegTestError = pdFALSE;` as `volatile` to prevent linking error 

## Does this work?

The project was verified to be compiling, but I don't have ATMega323 to test, nor did I want to port the code to an ATMega328P. 
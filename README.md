# WireNoArduino
I wanted to use the Wire library without the other arduino core libraries to save memory and to reduce my code hinging on pins_arduino.h and Arduino.h. Basically I brute force moved things that were needed into the twi.c file. Also I removed the Stream usages. So if your code needs that this won't work.

To adjust for your application:
in twi.c -> twi_init()

You may need to set timer 0 prescale factor to 64,
for my application (ATMEGA644pa) I used this:

    sbi(TCCR0B, CS01);
    sbi(TCCR0B, CS00);

For other applications: Arduino uses these protocols in its init() routine to set the prescaler:

    // set timer 0 prescale factor to 64
    #if defined(__AVR_ATmega128__)
      // CPU specific: different values for the ATmega128
      sbi(TCCR0, CS02);
    #elif defined(TCCR0) && defined(CS01) && defined(CS00)
      // this combination is for the standard atmega8
      sbi(TCCR0, CS01);
      sbi(TCCR0, CS00);
    #elif defined(TCCR0B) && defined(CS01) && defined(CS00)
      // this combination is for the standard 168/328/1280/2560
      sbi(TCCR0B, CS01);
      sbi(TCCR0B, CS00);
    #elif defined(TCCR0A) && defined(CS01) && defined(CS00)
      // this combination is for the __AVR_ATmega645__ series
      sbi(TCCR0A, CS01);
      sbi(TCCR0A, CS00);
    #else
      #error Timer 0 prescale factor 64 not set correctly
    #endif


Additionally you may need to adjust the I2C ports. This can usually be found in your avr's data sheet at the pinout.
You can edit the ports right above the twi_init() routine.

    //edit your I2C ports
    volatile uint8_t* _twiDDR = &DDRC;
    volatile uint8_t* _twiPORT = &PORTC;
    volatile uint8_t sda_PIN = PINC1;
    volatile uint8_t scl_PIN = PINC0;  

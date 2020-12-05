# From Arduino to STM32: Getting started with ARM Embedded Development

![Alt text](resources/images/title.png)

Do you already have some experience with Arduino, and want to step up the game into proper ARM embedded system developments? If so, you're in the right place!

We're going to take a look how to get started with STM32 microcontrollers using STM32Cube and Keil MDK-ARM, which is considered "canon", and only $4 worth of hardwares.

This tutorial is designed to be detailed and easy to follow. However, it is **NOT** meant to be a comprehensive guide. Instead it aims to cover the basics to get you going quickly, and teaches you how to figure things out yourself.

## Prerequisites

#### Experience with Arduino or other embedded systems

As the purpose of this guide is to help you step up from Arduino to STM32, it is best that you have an understanding of basic microcontroller peripherals like GPIO, UART, etc, and simple digital circuits.

#### Programming in C

We'll be using plain C as well, so it's nice to have some experience with that.

## What you need

You do need to buy some hardwares, luckily they are dirt cheap, and you only need 1 of each to get started. Although I do recommend getting a few more for spares. They usually take 1 or 2 weeks to arrive, and you can pay more for faster shipping. 

### ST-Link v2 Programmer

You need this to upload programs into the STM32 chip. Fortunately they are all over ebay and dirt cheap (usually less than $3 including shipping). Just search [ST Link v2 on ebay](https://www.ebay.com/sch/i.html?_from=R40&_nkw=st+link+v2) and it should look like this:

![Alt text](resources/images/stlink.jpg)

### STM32F030F4P6 development board

This is the board we'll be using throughout this tutorial. You can find it by [searching "STM32F030F4P6"](https://www.ebay.com/sch/i.html?_from=R40&_nkw=stm32f030f4p6). They are even cheaper at less than 2 dollars, and should look like this:

![Alt text](resources/images/ebayboard.jpg)

We'll get into details about this board in the next lesson.

### USB Serial adapter

You probably already have one if you've been playing with Arduinos before. If you don't, go on ebay and search [CP2102](https://www.ebay.com/sch/i.html?_from=R40&_nkw=cp2102). Of course other chips like CH340 or FTDI also works. It's just a matter of preference.

### Get the files

Click "Download ZIP" on the top right corner to get lesson files, then just follow along on this webpage.

![Alt text](resources/images/dl.png)

## What now?

If you can't wait to get going, click a lesson below and get started!

[Lesson 0: Setup and Blinking LED](./lesson0_intro_blinkLED/README.md)

[Lesson 1: UART Transmit](./lesson1_serial_helloworld/README.md)

[Lesson 2: External GPIO Interrupts](./lesson2_external_interrupt/README.md)

[Lesson 3: UART Receive and External Files](./lesson3_serial_recv_interrupt)

[Lesson 4: Timers, PWM and Watchdog](./lesson4_timers_and_pwm/README.md)

[Lesson 5: SPI and I2C](./lesson5_spi_i2c/README.md)

[Lesson 6: RTOS](./lesson6_rtos/README.md)

[Lesson 99: Miscellaneous](./lesson99_miscellaneous/README.md)

If you're not in a hurry, I suggest keep reading while I talk about the issue of Arduino, the upcoming trend of 32-bit microcontrollers, and the naming scheme of STM32 parts.

## The case of Arduino

Now there are many good reasons to stay inside the Arduino ecosystem. After all, it kickstarted the "maker" revolution in the first place. The consistent hardware, simple IDE, and the excellent community support meant it's easier than ever for people to start creating what they want, and the abstraction layer shielded them from the low-level nitty-gritties like peripheral configuration and fuse settings.

It's all well and good, and most people are perfectly content with that. However, when you look at the actual chip on most Arduinos, you'll find an aging ATmega328P, a decade old 8-bit chip with a tiny amount of RAM, limited peripherals, and a leisurely clock speed. As a result, in commercial situations 8-bitters like those have been mostly relegated to cost-sensitive applications like cheap toys. And the world has largely moved on to 32-bit microcontrollers.

Now I'm not saying 8-bit Arduinos have gone completely pointless, in fact they are excellent learning tools and more than adequate for most hobby projects. It's just **if you want to go further down the path of embedded development, plain old Arduino just simply isn't going to cut it.**

## Why 32-bit?

There are a huge number of 32-bit microcontrollers on the market today, most of which use ARM Cortex-M architecture. ARM licenses it out to interested companies, on which they add their own special features and peripherals. This eliminates the cost of designing their own architectures from scratch, and is one of the reasons why ARM is so prevalent in mobile computing today.

Popular examples include STMicroelectronics' STM32 line, NXP's LPC line, and Atmel's own SAM line. I'm using STM32 in this guide because of its relative large community, nice graphical configuration tool, low-cost of dev-board, and abundance of documentations.

STM32s are also widely used in real world customer electronics, examples include [Amazon Dash Button](https://learn.adafruit.com/dash-hacking-bare-metal-stm32-programming/overview), [3D printer drivers](http://www.st.com/en/evaluation-tools/steval-3dp001v1.html), [Apple Watch](http://blog.atollic.com/is-the-apple-watch-developed-using-atollic-truestudio), [Fitbit](http://www.techinsights.com/about-techinsights/overview/blog/fitbit-charge-2-teardown/), [BB-8 robot](https://www.cnet.com/news/sphero-bb-8-teardown-reveals-the-cool-robot-tech-inside-this-fun-star-wars-toy/), [Nintendo Switch](http://www.st.com/content/st_com/en/about/media-center/press-item.html/t3934.html) has 3 of them!.

Those 32-bit chips tends to run faster, have great deal of more memory and peripherals, and even cheaper than the comparable 8-bit chips. To demonstrate, here is a comparison between Arduino and my go-to STM32 chip, STM32F072C8T6:

![Alt text](resources/images/intro_comp.png)

The result speaks for itself even for a low-end STM32 chip. Here's a brief summary:

### Advantages:

#### High performance 

Even the cost-oriented F0 series beats the Arduino in terms of power and peripherals. As a result, you'll be able to develop larger and more sophisticated programs, with better performance and more connectivity options. RTOS is also no longer a pipe dream like it is on Arduinos.

#### Full debugging support

Hardware debugging is supported on all STM32 chips, that means you have single stepping, breakpoints, memory viewer, all the usual stuff. No need to rely on ```println()``` like on Arduino anymore.

#### Versatile, Reusable and Future-proof:

There are **hundreds** of STM32 variants to choose from. You can go from 50 cents STM32F0 up to 400MHz STM32H7. All of them uses the same tool and code structure so it's trivially easy to port between them. They also mostly have the same pinout for a given package type, so you can simply drop in a more powerful chip without changing the circuit design should the need arises.

#### Integrated Bootloader

All STM32s have built-in bootloader for uploading firmware without using a programmer. This can be done through Serial, USB or even I2C, SPI and CAN bus, all without taking up user flash memory like Arduino does.

#### 5V tolerant

All digital pins are 5V tolerant, so you can safely hook them up to legacy systems.

#### Cost effective

If you're designing a product and making own circuit board, using a STM32 chip is much cheaper, compact, and clean than plopping a whole Arduino on it, especially if you're doing a production run.

### Disadvantages:

Of course there are reasons that STM32 isn't as popular as Arduinos in the makers crowd, and to name a few:

#### Learning curve

Programming on STM32 is a bit more involved than Arduino, but you do get much more control over peripherals as a result. Also, there aren't many guides that give a concise and clear instruction on *just how to get started*. I hope this one changes that.

#### Board choices

You'll want to get a dev board, and there simply aren't many around. ST makes a few themselves, and there are some cheap ones on eBay. We'll touch on this later.

#### Community and library support

It's obvious that STM32's community isn't as vibrant as Arduino's, and as a result there aren't as many ready-to-use libraries available. I hope this guide will help with the situation, and we'll take a look at writing your own libraries and converting existing Arduino libraries as well.

### Conclusion

32-bit microcontrollers like STM32 is much powerful and versatile than 8-bit chips, and is where the industry currently heading towards. It is the logical next step if you want to go beyond the limitation of Arduino.

## STM32 naming schemes

Since there are hundreds of STM32 chips out there, I think it would be a good idea to know how they are named. Here is the official naming convention: 

![Alt text](resources/images/naming.png)

By looking at the 2 characters just after ```STM32```, you can also tell what ARM processor core a chip is using:

| STM32 Series |   ARM Core   |
|--------------|--------------|
| F7, H7       | Cortex-M7F   |
| F4, F3, L4   | Cortex-M4F   |
| F2, F1, L1   | Cortex-M3    |
| L0           | Cortex-M0+   |
| F0           | Cortex-M0    |

Note how you can eyeball the capability by just looking at the feature numbers. Generally speaking, the higher the number, the better the performance and the variety of peripherals, but also more expensive.

[Click here](resources/datasheets/STM32_product_selection_guide.pdf) for a spec sheet for all STM32 chips.

## What's wrong with Blue Pill?

The [Blue Pill](https://wiki.stm32duino.com/index.php?title=Blue_Pill) is another very popular dev board with a STM32F103 chip. However personally I don't really like it for a number of reasons:

* STM32F1 is a very old design (more than 10 years old in 2018), so a number of convenient features from later chips are missing, such as TX/RX pin inversion, build-in USB pullups, RTC date backup, and more.

* Furthermore, lots of peripherals are simply missing when compared to the newer STM32F0 chips, such as analog comparators, I2S, HDMI_CEC, and so on. 

* STM32F1 has some [nasty hardware design errors](https://www.st.com/content/ccc/resource/technical/document/errata_sheet/7d/02/75/64/17/fc/4d/fd/CD00190234.pdf/files/CD00190234.pdf/jcr:content/translations/en.CD00190234.pdf) that needs looking out for when using certain peripherals.

* There are tons of [fake STM32F103 chips!](https://hackaday.com/2020/10/22/stm32-clones-the-good-the-bad-and-the-ugly/)

* STM32F0 series is cheaper.

* STM32F0 series has free Keil MDK license with no code size limit.

Therefore, I recommend starting with newer STM32F0 chips at the beginning.

## Next steps

That's pretty much it for the introduction! For now you should have an understanding about the advantages of 32-bit microprocessors, and ordered the required hardwares from ebay. 

We'll take a look at the details of the dev board, the softwares you need, how to hook everything up, and write your own blink program in the next lesson.

[CLICK ME TO GO TO NEXT LESSON](lesson0_intro_blinkLED/README.md)

















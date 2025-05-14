# Lab Notebook

## 1st Week
This week, we focused on deploying the VOSK speech recognition model on the Raspberry Pi 5, which serves as the central processor for our project. Our goal was to ensure the model could run in near real-time with reasonable accuracy. We followed several online tutorials and documentation for setting up VOSK on ARM architecture. We were particularly inspired by this YouTube video, which provided clear guidance and troubleshooting tips for running VOSK on the Pi:  
[https://www.youtube.com/watch?v=-0W_AxSD_t8](https://www.youtube.com/watch?v=-0W_AxSD_t8)

By the end of the week, we successfully installed the dependencies, configured the model, and ran basic tests with short audio clips. The preliminary transcription results were promising, though we noted latency and memory limitations on the Pi. We planned to optimize the model in future weeks.

## 2nd Week
Our main focus was preparing for the breadboard demo. Unfortunately, our ordered ILI9341 LCD screen was delayed and would not arrive before our scheduled presentation. As a workaround, we switched to using a gme12864 OLED display, paired with an Arduino Uno, to demonstrate the basic display of transcribed text.

The Arduino + OLED setup was surprisingly smooth. We were able to display sample sentences and characters with good clarity. Our next goal became connecting this system to the Raspberry Pi and displaying received text from UART transmission.

We followed this online tutorial to set up and program the display:  
[https://www.instructables.com/Monochrome-096-i2c-OLED-display-with-arduino-SSD13/](https://www.instructables.com/Monochrome-096-i2c-OLED-display-with-arduino-SSD13/)

![OLED Display](oled_display_gme12864.png)

This helped us gain confidence in the display portion of our project.

## 3rd Week
We attempted to set up UART communication between the Raspberry Pi and Arduino by connecting Pi TX/RX to Arduino RX/TX. However, no data was received. After multiple rewiring attempts and troubleshooting, we concluded that our approach was flawed.

![Arduino Uno R3](arduino_uno_r3.png)

We also discovered a critical design flaw overlooked in our proposal phase: the Raspberry Pi 5 requires a stable 5V 5A power supply to fully support peripherals. Our initial buck converter setup could not supply this, causing severe instability. We explored DC-DC converters but found that high-current converters are expensive and require specialized PCBs. We looked into using power management ICs, but they required complex battery circuitry and design skills we lacked.

We then discovered a promising alternative: integrating an external power bank. Inspired by a DIY video:  
[https://www.bilibili.com/video/BV1Ft421j7j6?spm_id_from=333.788.recommend_more_video.-1&vd_source=d8237448c0989c98c18ea372f881b1c2](https://www.bilibili.com/video/BV1Ft421j7j6?spm_id_from=333.788.recommend_more_video.-1&vd_source=d8237448c0989c98c18ea372f881b1c2)

We decided to avoid designing our own battery management system and instead use an off-the-shelf power bank to ensure safe and stable operation.

## 4th Week
We successfully wired the Raspberry Pi’s RX and TX ports for UART communication. We used *Minicom*, a terminal emulator, to verify that UART communication was working properly on the Pi side.

![Minicom Setup](minicom_modem_setup.png)

We then learned that the Arduino Uno uses its onboard USB-to-UART chip to communicate with a PC, which monopolizes the UART pins and prevents external devices from using them effectively. We concluded that Arduino was not suitable for our final design.

We ordered an STM32 Nucleo development board to replace the Arduino. After receiving the board, we tested UART communication and confirmed that text could be reliably transmitted from the Raspberry Pi to the STM32 via UART.

Next, we attempted to run an audio demo with our analog microphone connected to the STM32. The quality was poor, and the Raspberry Pi could not process the audio properly. We explored ADC sampling with STM32 but realized it would be insufficient for our needs. Our final decision was to switch to a PDM microphone, which outputs digital data and should interface more reliably with the Pi.

## 5th Week
The PDM microphone we ordered was unfortunately lost during shipping. Given the tight schedule and PCB fabrication deadline, we had to quickly adapt. We decided to use a USB microphone connected directly to the Raspberry Pi. This allowed us to proceed with testing the VOSK model in real-time.

We also tested the display functionality. All individual components—Raspberry Pi, USB mic, speech recognition, STM32 UART transmission, and OLED display—were now working. We successfully displayed transcribed text, but noticed that the display updates were quite slow, which we planned to optimize.

![Project Demo](Demo.jpg)

We considered code-level optimizations and improving our UART packet protocol to reduce display latency.

## 6th Week
This was a big milestone week. We received our custom-designed PCB along with a stencil for surface mount soldering. The PCB combined connections for the Raspberry Pi, STM32, OLED display, and external power connections into a compact form factor.

We thoroughly inspected the PCB for design flaws, cold joints, and continuity issues. Assembly went smoothly, and initial power-up tests were successful. All subsystems powered on as expected without short circuits.

## 7th Week
I worked heavily on the Python code running on the Raspberry Pi. Initially, I had each transcribed sentence split into individual characters, with each character sent as a separate UART packet to the STM32 display driver.

We used *Minicom* to test the integrity of UART packets and confirmed that the packets were formatted and sent correctly.

We then faced a frustrating issue: text overlap and garbled lines on the display. After debugging, we realized the display driver already had line switching functionality, which conflicted with our own line management code. I decided to modify the display driver directly to disable its auto-line-switching, which solved the problem.

The final system reliably displayed sentences on the OLED display without overlapping or flickering. We documented all changes to the firmware and software in our Git repository.

## 8th Week
The final integration week! We met as a team and assembled all components into our project enclosure. We conducted extensive end-to-end verification tests to ensure our system met all design and functional requirements.

The accuracy of the speech recognition system was excellent under normal speaking conditions. The display speed was finally within acceptable limits after our software optimizations.

We demonstrated the system using a poem by Langston Hughes, which the VOSK model transcribed and sent to the display.

[Watch the Poem Demo](project_demo_video.mp4)

We reflected on the tremendous progress and challenges overcome across these eight weeks. Our system is now fully functional and ready for final presentation.
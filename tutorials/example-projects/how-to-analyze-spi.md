# How to Analyze SPI

## How to Analyze SPI

Serial Peripheral Interface \(SPI\) is a communication protocol often used between processors and devices that require a relatively fast interface, such as shift registers, flash memory, and some sensors. It is a _synchronous_ protocol, which means it requires a separate clock line so that the transmitter can tell the receiver when to sample the data line.

Interestingly, SPI connections often rely on two data lines: MISO and MOSI. Because of this, data can be sent between the master and device at the same time \(_full duplex_\).

Example code is provided below for [Arduino](https://www.arduino.cc/), [mbed](https://os.mbed.com/), or [AC6 System Workbench for STM32 \(SW4STM32\)](http://www.openstm32.org/).

**Required Materials**

| Item |
| :--- |
| One of the Saleae logic analyzers: [Saleae Logic 8](https://usd.saleae.com/products/saleae-logic-8), [Saleae Logic Pro 8](https://usd.saleae.com/products/saleae-logic-pro-8), or [Saleae Logic Pro 16](https://usd.saleae.com/products/saleae-logic-pro-16)​ |
| ​[Nucleo-F446RE](https://www.digikey.com/product-detail/en/stmicroelectronics/NUCLEO-F446RE/497-15882-ND/5347712)​ |
| ​[MCP3002 10-bit analog-to-digital converter \(ADC\)](https://www.digikey.com/product-detail/en/microchip-technology/MCP3002-I-P/MCP3002-I-P-ND/319412)​ |
| ​[10k potentiometer](https://www.digikey.com/product-detail/en/sparkfun-electronics/COM-09806/1568-1616-ND/7319606)​ |
| ​[Breadboard](https://www.digikey.com/product-detail/en/bud-industries/BB-32621/377-2094-ND/4156445)​ |
| ​[Jumper wires](https://www.digikey.com/product-detail/en/sparkfun-electronics/PRT-12795/1568-1512-ND/5993860)​ |

#### Connect Hardware <a id="connect-hardware-3"></a>

Even though the Nucleo-F446RE has an onboard analog-to-digital converter \(ADC\), the MCP3002 is an easy-to-use and inexpensive ADC that communicates over SPI, which makes it perfect for demonstrating the protocol.

Connect the Nucleo to the MCP3002 and the MCP3002 to a 10k potentiometer as shown. Note that the SPI pins are also broken out to the male pins just to the right of the female Arduino headers on the Nucleo. This allows us to attach the Saleae Logic Analyzer wires.

![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LJyR1KEnjYBK2_cUw23%2F-LK-cRuCDRBtYSjHV9yh%2F-LK-clHxbABXQ7_MQnxY%2Fspi_circuit_fritzing.png?alt=media&token=db47e451-4b5b-4957-8af8-2ae220085539)

#### Run Demo Application <a id="run-demo-application-3"></a>

Download the example code for your IDE:

{% file src="../../.gitbook/assets/spi\_example\_arduino.zip" caption="SPI Example - Arduino" %}

{% file src="../../.gitbook/assets/spi\_example\_mbed.zip" caption="SPI Example - mbed" %}

{% file src="../../.gitbook/assets/spi\_example\_sw4stm32.zip" caption="SPI Example - SW4STM32" %}

Open the demo in your chosen IDE. Compile the program, and upload it to the Nucleo-F446RE development board.

Open the serial terminal program that you downloaded from the UART example.

Connect to the Nucleo board over the assigned serial port with a baud rate of **115200**, 8 data bits, no parity bit, and 1 stop bit \(**8-N-1**\). Open the connection, and you should see the voltage from the MCP3002 being reported to you in regular intervals. Try turning the knob on the potentiometer to see the voltage change.

![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LJyR1KEnjYBK2_cUw23%2F-LK-cRuCDRBtYSjHV9yh%2F-LK-clHkGZBA5SaR90lF%2Fscreen_21.png?alt=media&token=91b62e23-b093-4b0b-924c-d02b1e0ce776)

#### Measure the Signal <a id="measure-the-signal-2"></a>

Open the Logic software with the Logic Analyzer plugged in. Click on the **Device Settings Button**.

In the device settings window, set the speed to **at least 50 MS/s** and the duration to **1 second**. Click both **Clear** buttons to disable all channels, leaving only the digital Channel 0 enabled. Click **digital Channels 1-3** to enable them.

![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LJyR1KEnjYBK2_cUw23%2F-LK-cRuCDRBtYSjHV9yh%2F-LK-clHlwy1OsDUmZZHu%2Fscreen_22.png?alt=media&token=f1f10c37-a0d1-48f8-9cf9-c48d1a0756a4)

We can set up the analyzer before we capture so that we can choose the appropriate signal as the trigger. Click on the plus button \(**+**\) next to _Analyzers_ on the right side. Select **SPI** to bring up the settings window. Leave everything as default and click **Save**.

![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LJyR1KEnjYBK2_cUw23%2F-LK-cRuCDRBtYSjHV9yh%2F-LK-clHmzFdReFvURwBQ%2Fscreen_23.png?alt=media&token=3935dbaf-6a9c-4b5b-9c32-e9e6757ffb11)

The _Enable_ line \(also known as _CS_ or _SS_\) is often the first signal to change prior to data being transmitted on a SPI bus. As a result, we’ll set our trigger on that line. Click on the **Trigger Button** next to _Channel 3 \(SPI - ENABLE\)_, and select the **Trigger on Falling Edge** option.

![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LJyR1KEnjYBK2_cUw23%2F-LK-cRuCDRBtYSjHV9yh%2F-LK-clHpweyoDAJwijrw%2Fscreen_24.png?alt=media&token=5e39c473-a4fa-4691-ad9d-9c6da94df9b6)

Click on the **Trigger Button** again to close the pop-up. Click **Start** to begin collecting data. Because our code is set to send SPI data every 0.2 seconds, data collection should begin almost immediately.

Zoom in around the _0 s : 0 ms : 0 μs_ mark, and you should see the waveforms that traveled across the SPI bus at that moment. Click on the _gear icon_ next to _SPI_under _Analyzers_. Click on **Bin** under _Display Radix_ to show the interpreted data as binary.

Above Channel 0 \(MOSI\), you should see the data that we are transmitting out from the Nucleo board: 0b11000000 for the first byte, and all 0s for the second byte. At the same time, data comes back in from the MCP3002 on Channel 1 \(MISO\). In this instance, only bits 1-10 carry information about the measured voltage.

![](https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%2F-LJyR1KEnjYBK2_cUw23%2F-LK-cRuCDRBtYSjHV9yh%2F-LK-clHreXVbubZkXxgD%2Fscreen_25.png?alt=media&token=f9f59b7f-d49a-4b8d-b9f3-c29611237b9b)

Adjust the potentiometer and see if you get different data on the MISO line when you sample again.


This is a re-working of the original source code to increase SPI throughput on the ESP32 by using DMA with SPI. It does this by both batching SPI transactions as efficiently as possible as well as also using a more generalized DMA strategy, which should be enable this to be ported to other platforms more easily. 

These changes also add some additional features such as support for very large SPI memory transfers directly to the FT81x memory. This can be used to directly stream uncompressed bitmaps for faster dynamic updates than by going through the CMD buffer, such as treating the whole display (or just a region, depending on the DL) as a memory mapped bitmap display. To do this, you would set up a bitmap in the FT81x's memory and then use SPI to directly update the bitmap and the FT81x will show those changes in real-time with no need to refresh the DL. You can set up either the entire screen or multiple rectangular regions of the display for direct memory mapped access along with the appropriate display list that assigns those bitmaps to screen regions. 

While that is not demoed here, these changes are also the basis for another version of this code (a display driver) that can enable the FT81x to be used with the LittlevGL GUI library, which does update the display using these methods. However, due to using a larger 800x480 16-bit display with a serial data protocol, full screen refreshes do have to push over 6 mbits over SPI for a full refresh. While it runs close to the theoretical max speeds of the FT81x for direct memory transfers (stable at 30Mhz and successfully pushed to 32Mhz), that translates to only 4-5 FPS using 2-wire SPI mode. By using Quad SPI (not quite supported yet) it should be around 4x faster.

Just keep in mind that physical SPI wiring is the largest determiner on stability so if it is unstable, try a slower SPI clock speed or shorten/improve the SPI wiring.

Note that all the EVE code files in this demo project are unique to this demo project - no changes have not been propagated to the other projects or main source files. 

Also note that I left my platformio.ini file in place for my particular ESP32 board configurations.
  
Here are some stats on the performance improvements to this demo program when using the Arduino framework:

Original FT81x demo			- stable for me at 16Mhz
- time1: 1586us
- time2: 94us
	
New FT81x-Arduino			- stable for me at 16Mhz
- time1: 191us
- time2: 45us

New FT81x-ESP32				- stable for me at 30Mhz
- time1: 147us
- time2: 27us
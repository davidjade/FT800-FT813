This is a re-working of the original source code to increase SPI throughput on Arduino. It does this by batching SPI transactions as efficiently as possible while also laying the groundwork for a more generalized DMA capability. While these changes support DMA access, since Arduino SPI doesn't expose DMA it is not activated. See the new ESP32 native demo for using DMA mode.

These changes also add some additional features such as support for very large SPI memory transfer directly to the FT81x memory. This can be used to directly stream uncompressed bitmaps for faster dynamic updates than by going through the CMD buffer, such as treating the whole display (or just a region, depending on the DL) as a memory mapped bitmap display. To do this, you would set up a bitmap in the FT81x's memory and then use SPI to directly update the bitmap and the FT81x will show those changes in real-time with no need to refresh the DL. 

While that is not demoed here, these changes are also the basis for another version of this code that can enable the FT81x to be used with the LittlevGL GUI library.  Note that using Arduino for SPI is significantly slower than using the native ESP32 SPI API so you if want to use these new features I would suggest using the native SPI version.

At this point it has only been tested on ESP32 boards and it is probable that it may be broken on some Arduino platforms, particularly AVR which uses
special PROGMEM data access. There is code written to handle that but I had nothing to test it on to ensure that it still worked.

Another thing that is broken for sure is the old DMA code for other boards. I don't have these board so I could not port to the new DMA method. I believe it should be possible though to port to the new DMA methods as I tried to make it as generalized as possible.

Note that all the EVE code files in this demo project are unique to this demo project - no changes have not been propagated to the other projects or main source files. 

Also note that I left my platformio.ini file in place for my particular ESP32 board configurations.
  
Here are some stats on the performance improvements to this demo program when using the Arduino framework:

Original FT81x demo			- stable for me at 16Mhz
- time1: 1586us
- time2: 94us
	
New FT81x-Arduino			- stable for me at 16Mhz
- time1: 191us
- time2: 45us


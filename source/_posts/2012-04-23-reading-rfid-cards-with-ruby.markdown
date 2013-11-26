---
layout: post
title: "Reading RFID Cards With Ruby and the Mac"
date: 2012-04-23 09:18
comments: true
categories: [RFID, Ruby]
---

This weekend I fooled around with the Sparkfun "RFID Starter Kit". I purchased it from my local technology barn for around $50.00. I am teaching myself physical computing, and projects like that can be completed in an afternoon, are a great way to learn "just enough" to keep a junior hardware hacker like myself from getting frustrated.

I was thrilled to find out that getting this board to talk to Ruby took virtually no effort. In fact, it was so simple it almost felt like I was cheating some how. Here are the steps that I followed to get the ID-12 RFID Scanner and Reader talking to Ruby.

1. Download the most recent drivers from Future Technology Devices International:
    [http://www.ftdichip.com/Drivers/VCP/MacOSX/FTDIUSBSerialDriver_v2_2_14.dmg](http://www.ftdichip.com/Drivers/VCP/MacOSX/FTDIUSBSerialDriver_v2_2_14.dmg)
    The package contains two sets of drivers. Make sure that you install the version that is right for your operating system.

2. Install the serialport gem for Ruby
    gem install serialport (I used version 1.0.4)

3. Mount the scanner on top of the reader by lining up the prongs in the appropriate slot. Then plug the reader into the Mini-USB port of the mac.

4. From the console, locate the virtual com port the drivers created when you plugged in your Mini-USB cable.
    :~ ls -la /dev

    Scanning the output from this command you should see the device listed similar to this:  cu.usbserial-XXXXXX (where X's represent a driver id). Mine showed up as "cu.usbserial-A900ftPb" but yours may be different.

5. Use this sample code to print the unique RFID id stored inside the card, when someone swipes the card over the scanner.

``` ruby
# Simple example of reading from serial port to interface with the RFID reader.
require "serialport"
class RfidReader
 attr_accessor :key

 def initialize(port)
   port_str = port
   baud_rate = 9600
   data_bits = 8
   stop_bits = 1
   parity = SerialPort::NONE
   @sp = SerialPort.new(port_str, baud_rate, data_bits, stop_bits, parity)
   @key_parts = []
   @key_limit = 16 # number of slots in the RFID card.
   while true do
     main
   end
   @sp.close
 end

 def key_detected?
   @key_parts << @sp.getc
   if @key_parts.size >= @key_limit
     self.key = @key_parts.join()
     @key_parts = []
     true
   else
     false
   end
 end

 def main
   if key_detected?
     puts self.key
   end
 end
end

RfidReader.new("/dev/cu.usbserial-A900ftPb")  #may be different for you
```

Once you have the unique ID the possibilities become nearly limitless, you can use the card to update twitter, or turn on your lights, turn on the coffee pot, fire the nerf guns, release the hounds etc. Have fun, I know I will!

The DTS file is for this ADC:<br>
https://nl.aliexpress.com/item/32830756646.html

hook up the ADC like this:<br>
<b>
<ul>
<li>BCK to RPi GPIO 18</li>
<li>DOUT to RPi GPIO 20</li>
<li>LRCK to RPi GPIO 19</li>
<li>PDW to RPi 3.3V</li>
<li>FSY to RPi 3.3V</li>
</ul>
</b>

Solder a wire from 3.3V to FMT0 to select i2s mode. This is necessary because the + line is not connected to 3.3V.
Leave MODE0 and MODE1 as they are to select SLAVE mode.


compile pcm1802.dts with:<br>
<b> dtc -@ -I dts -O dtb -o pcm1802.dtbo pcm1802.dts</b>

copy it to the overlay folder for Trixie:<br>
<b> sudo cp pcm1802.dtbo /boot/firmware/overlays/</b>

edit /boot/firmware/config.txt:<br>
<b>sudo nano /boot/firmware/config.txt</b>

remove or disable all other sound overlays and comment out dtparam=audio=on:<br>
<b>#dtparam=audio=on</b>

now add:<br>
<b>dtparam=i2s=on<br>
dtoverlay=pcm1802</b>

save the file and reboot:<br>
<b> sudo reboot now</b>

does it appear?:<br>
<b> arecord -l</b>

if yes then run to test:<br>
<b> arecord -D hw:PCM1802 -f S32_LE -r 48000 -c 2 -d 30 test.wav</b>

I could only get it to record silence. Also I measured no squarewave on GPIO 4 which should supply something like 12.880 MHz to SCK.
If someone gets it to work I would love to hear about it! Good luck!

The DTS file is for this ADC:<br>
https://nl.aliexpress.com/item/32830756646.html

An external oscillator is necessary to get the pcm1802 to work:<br>
https://eu.mouser.com/ProductDetail/ABRACON/ACHL-24.576MHZ-EK

hook up the ADC like this:<br>
<b>
<ul>
<li>SCK to the output of the 24.576MHz oscillator</li>
<li>BCK to RPi GPIO 18</li>
<li>DOUT to RPi GPIO 20</li>
<li>LRCK to RPi GPIO 19</li>
<li>PDW to RPi 3.3V</li>
<li>FSY to RPi 3.3V</li>
</ul>
</b>

Solder a wire from 3.3V to one of the + pads and then to FMT0 to select I2S mode. 
This is necessary because the + line is not connected to 3.3V.
Solder the MODE0 and MODE1 pads to + as well to select a MASTER mode compatible with 96kHz recording using the 24.576MHz osc.


compile pcm1802.dts with:<br>
<b> dtc -@ -I dts -O dtb -o pcm1802.dtbo pcm1802.dts</b>

copy it to the overlay folder for Trixie:<br>
<b> sudo cp pcm1802.dtbo /boot/firmware/overlays/</b>

edit /boot/firmware/config.txt:<br>
<b>sudo nano /boot/firmware/config.txt</b>

remove or disable all other sound overlays and comment out dtparam=audio=on:<br>
<b>#dtparam=audio=on<br>
dtoverlay=vc4-kms-v3d,noaudio</b>

now add:<br>
<b>dtparam=i2s=on<br>
dtoverlay=pcm1802</b>

save the file and reboot:<br>
<b> sudo reboot now</b>

does it appear?:<br>
<b> arecord -l</b>

something like the following should show:<br>
<b>card 0: PCM1802ADC [PCM1802-ADC], device 0: bcm2835-i2s-dir-hifi dir-hifi-0 [bcm2835-i2s-dir-hifi dir-hifi-0]<br>
  Subdevices: 1/1<br>
  Subdevice #0: subdevice #0</b>

if yes then run to test:<br>
<b>arecord -D hw:PCM1802ADC -r 96000 -f S32_LE -c 2 -V stereo -d 30 test.wav</b>

Have fun!

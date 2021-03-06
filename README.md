# artnet-unicorn-hat
Control Pimoroni Unicorn Hat LEDs using the Art-Net protocol.

Art-Net is a protocol for controlling lights over a network. Glediator
controls LEDs on one or more Art-Net nodes. An Art-Net node drives the
LEDs. In this example, Glediator runs on a laptop and controls a Pi with
a Unicorn Hat. The Pi is the Art-Net node. A Pimoroni Unicorn Hat is
an add-on board for a Raspberry Pi+/2 with an 8 by 8 grid of ws281x
LEDs.

http://www.solderlab.de/index.php/software/glediator

https://en.wikipedia.org/wiki/Art-Net

## Preliminary

The Unicorn Hat must be installed and working on the Pi with the
Pimoroni supplied Python software. Make sure this works before graduating
to Art-Net.

http://learn.pimoroni.com/tutorial/unicorn-hat/getting-started-with-unicorn-hat

## Install libraries on the Pi

Do this only once to install the Python twisted libraries.

```
sudo apt-get install python-twisted
```

## Run Art-Net server on the Pi
The following command runs the Art-Net server turning the Pi into an Art-Net node. 
Many programs can send LED values to an Art-Net node. Glediator is one such
program.

```
sudo python artnet-server.py
```

## Glediator

See the Glediator download page to download and install Glediator.
If you see errors about missing binary RXTX, ignore them. In this case,
Glediator controls the LEDs using network packets, not serial communcations.

Glediator is designed to work with many different LED arrays so it must
be told the dimensions and arrangements of the LEDs. It must also be
told the IP address of the Pi with the Unicorn Hat.

### Change IP address in patch file
Open the Glediator patch file artnet-pimoroni-unicorn-hat.gled in your
favorite editor. nano works fine. Change the IP address to the IP address
of the Pi.

This how the IP address 192.168.1.231 looks like in the file

```
Patch_Uni_ID_0_IP1=192
Patch_Uni_ID_0_IP2=168
Patch_Uni_ID_0_IP3=1
Patch_Uni_ID_0_IP4=231
```

Close and save the file.

### Set matrix size
In Glediator change the matrix size to 8 by 8.

At the Glediator main screen, select Options | Matrix Size

Size_X = 8 Size_Y = 8

### Set Art-Net mode
At the Glediator main screen, select Options | Output

At the Output Options screen:

```
Output Mode: Artnet
Mapping Mode: Single_Pixels
```

Ignore the rest in the top half of the screen. Ignore the left bottom options
which are for serial ports.

In the right bottom options click on Patch ArtNet/TMP2.Net

At the "Artnet & TPM2.Net Patcher" screen, load artnet-pimoroni-unicorn-hat.gled

Click on Done

Back at the Output Options screen, click on Apply Changes.

Click on "Open Socket". Glediator will start sending pixel values to the Pi.

Click on Done to get back to the main screen.

At this point, the control panel can be used to generate new patterns.

## PixelController

https://github.com/neophob/PixelController/releases

PixelController is another LED pattern generator with Art-Net output. After
unzipping pixelcontroller-distribution-2.1.0-RC1.zip change into the directory
pixelcontroller-distribution-2.1.0-RC. Copy config.properties from this
repo to the directory data.

Edit data/config.properties to set the IP address of the Pi.

```
# Change the following line to match the IP of your Pi
artnet.ip=192.168.1.231
```

Next run the program. The Unicorn Hat should immediately show pixel patterns.

```
unzip pixelcontroller-distribution-2.1.0-RC1.zip
cd pixelcontroller-distribution-2.1.0-RC
cp ~/artnet-unicorn-hat/config.properties data/
nano data/config.properties
java -jar PixelController.jar
```

## Open Pixel Control Protocol

Add support for Open Pixel Control protocol on TCP port 7890. See
https://github.com/zestyping/openpixelcontrol for the OPC protocol
specification.

Do not use OPC and Art-Net at the same time. This will just produce
garbage on the LEDs.

### Example OPC programs to drive the LEDs

Many open pixel control and Fade Candy examples work. Be sure to
modify the examples with the Raspi IP address.

The following examples from https://github.com/zestyping/openpixelcontrol work.

    python_clients/
            lava_lamp.py,miami.py,nyan_cat.py,sailor_moon.py,spatial_stripes.py

            ```
            $ ./lava\_lamp.py -l grid8x8.json -s <Raspi IP addr>:7890 -f 20
            ```

The Fadecandy grid8x8 Processing examples at https://github.com/scanlime/fadecandy work. These examples show how
to create interactive LED displays. Edit the PDE file to add the Raspi IP address.

    examples/processing/
        grid8x8_dot, grid8x8_noise_sample, grid8x8_orbits, grid8x8_wavefronts


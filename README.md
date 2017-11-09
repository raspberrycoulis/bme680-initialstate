# bme680-initialstate
## Using Pimoroni's BME680 with Initial State.

This Python script will pull data from the BME680 and send it to your Initial State account so you can look at them in graph form. It is based on Pimoroni's examples from the BME680 GitHub repository, but adapated to send data to Initial State in the relvant places.

### Install the BME680 files

The installer script, created by [Pimoroni](https://shop.pimoroni.com), is simple:

    curl -sS get.pimoroni.com/envirophat | bash

But full install instructions can be found [here](https://learn.pimoroni.com/tutorial/sandyj/getting-started-with-enviro-phat).

### Install the Initial State Python Data Streamer files

You'll need to install the relevant Initial State files, which is done by:

    \curl -sSL https://get.initialstate.com/python -o - | sudo bash

### Clone and use my script

Simply clone this script by running:

    cd ~
    sudo git clone https://github.com/raspberrycoulis/bme680-initialstate.git

You'll need to make some changes first, specifically inserting your Access Key (found via Initial State), as well as optionally setting the bucket name and key in the following place:

    streamer = Streamer(bucket_name="Your Bucket Name", bucket_key="bucket_key", access_key="your_access_key")

You can use your preferred text editor, but Nano works just fine:

    sudo nano bme680-initialstate.py

Be sure to save when exiting:

    ctrl + x
    y

Make the script executable:

    sudo chmod +x bme680-initialstate.py

And then finally test it by running:

    ./bme680-initialstate.py

If done correctly, you should see printouts in the command line of the temperature, pressure humidity and air quality readings. Stop the script by pressing `ctrl + c`.

## Making the script run automatically on boot

I followed the excellent guide found on [Raspberry Pi Spy](http://www.raspberrypi-spy.co.uk/2015/10/how-to-autorun-a-python-script-on-boot-using-systemd/) to make my  `bme680-initialstate.py` script run on boot. To recap, this is what I did:

### 1. Create a Unit file

This is what will tell the Pi to run your script on boot:

    sudo nano /lib/systemd/system/bme680-initialstate.service

Then add the following text to your file (you may need to adjust the path for your `bme680-initialstate.py` script depending on where it is located (the part `/home/pi/github/bme680-initialstate/bme680-initialstate.py`):

    [Unit]
    Description=The BME680 via Initial State service
    After=multi-user.target
    
    [Service]
    Type=idle
    ExecStart=/usr/bin/python /home/pi/github/bme680-initialstate/bme680-initialstate.py
    Restart=always
    
    [Install]
    WantedBy=multi-user.target

Exit, `ctrl + x`, and save `y`to create the service unit file.

### 2. Set the relevant permissions

Make sure that the permissions are set correctly:

    sudo chmod 644 /lib/systemd/system/bme680-initialstate.service

### 3. Configure systemd

Make sure that systemd can use your newly created unit file:

    sudo systemctl daemon-reload
    sudo systemctl enable bme680-initialstate.service

Reboot the Pi to test via `sudo reboot`.

### 4. Check on the status of your service

Check that the service has started by running:

    sudo systemctl status bme680-initialstate.service

If done correctly, you should see that your `bme680-initialstate.py` script is now running!

# DonkeyCar cheatsheet

I have made the following document to collect some practical information regarding the DonkeyCars we have made during the workshop in one place. It is very concise, if you need more information go visit the excellent DonkeyCar documentation:

[https://docs.donkeycar.com/](https://docs.donkeycar.com/)

Or signup for the DonkeyCar Slack:

[https://donkey-slackin.herokuapp.com/](https://donkey-slackin.herokuapp.com/)

# 1. Connecting to wifi

The raspberry is setup to connect to a wifi network with the name DonkeyCar and password &#39;donkeycar&#39;.

If you want to change the network to connect to, you can put the raspberry&#39;s SD card in a computer and create file called &#39;wpa\_supplicant.conf&#39; on the card with the proper credentials.

You can find the instructions on how to do this here:
[https://docs.donkeycar.com/guide/robot\_sbc/setup\_raspberry\_pi/#step-2-setup-the-wifi-for-first-boot](https://docs.donkeycar.com/guide/robot_sbc/setup_raspberry_pi/#step-2-setup-the-wifi-for-first-boot)

# 2. Logging in

Once the Raspberry is connected to the wifi you can log in over SSH with the following command:

ssh [pi@hd-basel-0x.local](mailto:pi@hd-basel-0x.local)

(replace the x with the number of your car/installation)

The password is: donkey

# 3. Starting the donkey web controller (without autopilot)

The cars we used in the workshop were setup to have the donkeycar software installed in the ~/car-test directory.

Go to this directory by typing:

```cd car-test
```

You are now in the car-test directory (to check type &#39;cwd&#39; and the result should be /home/pi/car-test).

You can now start the software:

```python manage.py drive
```

Wait a while. After a few moments the software should be started. You should then be able to connect to the web controller from you laptop or telephone (if they are connected to the same wifi network of course) by going to: [http://hd-basel-0x.local:8887](http://hd-basel-0x.local:8887) (again replace the x)

Drive the car around the track to record material to train the autopilot on. You&#39;ll need 5 to 20 thousand images. You can see the number of recorded images in the terminal where you started the car.

# 4. Copy the files you recorded back to your computer

This assumes you have the donkey software already installed! If not follow the instructions in the manual: [https://docs.donkeycar.com/guide/install\_software/#step-1-install-software-on-host-pc](https://docs.donkeycar.com/guide/install_software/#step-1-install-software-on-host-pc)

First activate the python environment:

```conda activate donkey
```

First make sure you are in the correct folder. It is probably ~/mycar but you might have chosen another folder during installation!

```cd ~/mycar
```

The following command copies the files back you the directory you are currently in:

```rsync -rv [pi@hs-basel-0x.local](mailto:pi@hs-basel-0x.local):~/car-test/data .
```

-rv = also copy all files in subdirectories and show what is being copied.

The dot at the end means &quot;here&quot; or current directory.

Replace the x :)

Wait until it is done. All the files are now on your computer.



# 5. Clean the data

Run the following command:

```donkey tubclean data/
```

This command starts the tub clean utility. When it is running you can open the following page in your browser:

[http://localhost:8886](http://localhost:8886)

Follow the instructions to remove data you don&#39;t want to use for training your autopliot.

_Remember the names of the tub\_ directories with images you want to use for the next step!_

# 6. Train an autopilot

**Notice!** Make sure that the settings in config.py/myconfig.py on the computer are the same as the settings on the donkeycar!

If you are sure the setting files are identical, start training:

```
python manage.py train --tub \&lt;tub folder names comma separated\&gt; --model models/\&lt;pilot name\&gt;.h5
```

The exact command depends on the name (or names) of your tub directories. It should look something like this:

```
python manage.py train --tub data/tub\_68\_20-02-12 --model models/super-pilot.h5
```

This is going to take a while. Have some coffee… Maybe go for a walk.



# 7. Copy the autopilot back to the car

This command it almost the opposite of the one before:

```
rsync -rv ./models/\&lt;pilot name\&gt;.h5 [pi@hs-basel-0x.local](mailto:pi@hs-basel-0x.local):~/car-test/models
```

Taking the example from the previous step, it should look like this:

```
rsync -rv ./models/super-pilot.h5 [pi@hs-basel-0x.local](mailto:pi@hs-basel-0x.local):~/car-test/models
```

(again: replace the x)

# 8. Start the car with the autopilot

If necessary, log back in to the donkey car and go to ~/car-test ( see step 2 and 3).

```
python manage.py drive --model models/\&lt;model name\&gt;.h5
```

Example:

```
python manage.py drive model --models/super-pilot.h5
```

And start the car again by going to: [http://hd-basel-0x.local:8887](http://hd-basel-0x.local:8887)

- Pick a max throttle,
- Set the Throttle mode to &#39;constant speed&#39;
- Choose &#39;local angle&#39; from the Mode &amp; Pilot drop down
- Press &#39;i&#39; on your keyboard to increase the throttle

The car should now start driving!
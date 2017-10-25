# Home-Assistant-Raspberry-Pi
The whole interface will have a great looking UI with the help Home Assistant( a free open source home automation platform). Whole code will be uploade on a Raspberry Pi whichis ARM based small but poerwful computer. Raspberry Pi will have Debian based OS, Raspbian Jessie on it.



.......................................................
Check https://www.instagram.com/pytron__/ for videos and images.
.......................................................



||Install the Software ||

![rpi](https://user-images.githubusercontent.com/32539405/31984678-06041126-b980-11e7-96a6-4ac37b1aad0f.jpg)


You need to install the software. 

Connect to your pi and enter the following to get the latest rasbian image:

$ sudo apt-get update

$ sudo apt-get upgrade -y

Install the dependencies that are needed for homeassistant:

$ sudo apt-get install python3 python3-venv python3-pip

Once done you will need to create the home assistant account and the virtual environment:

$ cd /srv

$ sudo mkdir homeassistant

$ sudo chown homeassistant:homeassistant homeassistant

$ sudo su -s /bin/bash homeassistant

$ cd /srv/homeassistant

$ python3 -m venv homeassistant_venv

$ source /srv/homeassistant/homeassistant_venv/bin/activate

$ exit

|| Configure Your .bashrc for the Virtual Environment ||

For ease of switching to the virtual environment, which is where you will run testing for configuring the home assistant, I put the source command in my .bashrc of my homeassistant user to make it easier.

$ vi /home/homeassistant/.bashrc

copy and paste the following at the bottom of the file

source /srv/homeassistant/homeassistant_venv/bin/activate

Now save the file and to test it type the following:

$ exit

$ sudo su -s /bin/bash homeassistant

|| Install the Home Assistant Program ||

Now that you are in the home assistant virtual environment you will now install the program with the following commands:

(homeassistant_venv) homeassistant@raspberrypi:/home/pi $ cd /srv/homeassistant

(homeassistant_venv) homeassistant@raspberrypi:/srv/homeassistant/ $ pip3 install homeassistant

This will install the program. Be patient for it will take a while depending on the version of raspberry pi you have and what the speed of the microSD card you used.

Once finished you can manually run the program from the virtual environment by typing the following command:

(homeassistant_venv) homeassistant@raspberrypi:/home/pi $ hass

However, next step we will look at making it start when the pi starts up.

You should see the following:

(homeassistant_venv) homeassistant@raspberrypi:/home/pi $


|| Setup Home Assistant to Auto Start  ||
Now we will need to setup the home assistant program to auto start on boot through the systemctl. This information taken from the following address: https://home-assistant.io/getting-started/autostar...

Begin with creating the service file for this as starting as the pi user:

$ sudo su root

$ cd /etc/systemd/system/

$ vi home-assistant@pi.service

Now cut and paste the following:

[Unit]
Description=Home Assistant After=network.target

[Service] Type=simple User=homeassistant #make sure the virtualenv python binary is used Environment=VIRTUAL_ENV="/srv/homeassistant/homeassistant_venv" Environment=PATH="$VIRTUAL_ENV/bin:$PATH"

ExecStart=/srv/homeassistant/homeassistant_venv/bin/hass -c "/home/homeassistant/.homeassistant"

[Install] WantedBy=multi-user.target

Save this and exit out of editing the file and exit root to return to the pi user.

Now you will need to restart the systemctl and read the file with the following commands

$ sudo systemctl --system daemon-reload
$ sudo systemctl enable home-assistant@pi

$ sudo systemctl start home-assistant@pi

Now you should be able to start the service with the following command:

$ sudo systemctl start home-assistant@pi

You can view the log to see if it is starting properly with the command:

$ sudo systemctl status home-assistant@pi -l

Or if you would like to view a scrolling log you can issue the following command:

$ sudo journalctl -f -u home-assistant@pi


|| Configuration File Setup GPIO ||

Now we have gotten it started we need to configure it for the gpio.

as the user pi you need to open the configuration file as follows:

$ cd /home/homeassistant/.homeassistant

$ vi configuration.yaml

Once in the configuration file you will disable the introduction component by commenting out the "introduction" line:

# Show links to resources in log and frontend
#introduction:

Now you will add the following to the file to activate the GPIO for the raspberry pi. (in this example I am using GPIO 11 and 12 for this)

switch:

- platform: rpi_gpio

ports:

11: TestLED1

12: TestLED2

Save this to the configuration file.


|| Testing Your Changes and Restart Home Assistant ||

Now you will need to test your changes.

To test for errors in your configuration you will need to run the check_config command as follows:

from pi user:

$ sudo su -s /bin/bash homeassistant

(homeassistant_venv) homeassistant@raspberrypi:/home/pi $ hass --script check_config

If all goes well you should get no errors.

To restart with systemctl you type the following command as the pi user:

$ sudo systemctl stop home-assistant@pi

$ sudo systemctl start home-assistant@pi

Remember you can watch the start up with the following two commands:

$ sudo systemctl status home-assistant@pi -l

Or

$ sudo journalctl -f -u home_assistant@pi

Once restarted you can go back to your home assistant web page http://<ip address>:8123

Now notice you two LED's are now accessible

|| This information is taken from the site: https://home-assistant.io/docs/installation/raspberry-pi/ ||

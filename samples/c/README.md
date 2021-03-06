iot-raspberrypi
===============
This is the sample program to send the following events from the Raspberry Pi to the Quickstart IBM Internet of Things service. 

The events that are emitted in this sample are:

1. CPU temperature
2. CPU Load
3. Simulated Sine wave to demonstrate the different events can be pushed to IoT Portal and visualized.

### Use the following commands to setup and run the sample


####Get the deb installer
```
pi@raspberrypi ~ $ curl -LO https://github.com/ibm-messaging/iot-raspberrypi/releases/download/1.0.2/iot_1.0-1_armhf.deb
```

####Install the deb
```
pi@raspberrypi ~ $ sudo dpkg -i iot_1.0-1_armhf.deb
Selecting previously unselected package iot.
(Reading database ... 68603 files and directories currently installed.)
Unpacking iot (from iot1.0-1_armhf.deb) ...
Setting up iot (1.0-1) ...
Starting the iot program
```

This installs and starts the iot process.  Use the status call to check the status of the process
```
pi@raspberrypi ~ $ service iot status
[ ok ] iot is running.
```



### Visualize

Visit the [IBM Internet of Things QuickStart Portal](http://quickstart.internetofthings.ibmcloud.com/) and enter your device ID to see real time visualizations of the data sent from your device.

Run `service iot getdeviceid` to get the device ID (MAC address) for your Raspberry Pi

```
pi@raspberrypi ~ $ invoke-rc.d iot getdeviceid
The device ID is b827eba84426
For Real-time visualization of the data, visit http://quickstart.internetofthings.ibmcloud.com/?deviceId=b827eba84426
```


### Other Commands

#### Stop
```
pi@raspberrypi /opt/iot $ sudo service iot stop
Stopping the program
Stopping the IoT-Raspberry Pi program with pid : <pid>
```

#### Start
```
pi@raspberrypi /opt/iot $ sudo service iot start
Starting the iot program
```

####Uninstall
```
pi@raspberrypi ~ $ sudo dpkg -P iot
(Reading database ... 68615 files and directories currently installed.)
Removing iot ...
Stopping the program
Stopping the IoT-Raspberry Pi program with pid : 8592
Purging configuration files for iot ...
```

IoT process logs with the help of syslog. So to view the logs, go to /var/log/syslog. 

#####Switching from Quickstart connection to a Registered connection

Onboard your device onto the Internet of Things Cloud. You will get the following information.

1. Organization Id
2. Device Type ID
3. Device ID
4. Authetication Method
5. Authetication Token


Stop the iot process
		
		sudo service iot stop

Type in the following command to create a new file device.cfg at /etc/iotsample-raspberrypi. Fill in the configuration file with the details that you got during onboarding the device.
sudo nano /etc/iotsample-raspberrypi/device.cfg

```	
#Sample Device configuration file...
org = yourOrganizationCode
type = iotsample-raspberrypi
id =b827eba84426
auth-method=token
auth-token = yourAuthToken
#End of Configuration file.	

Note: All the properties in the configuration file are mandatory.
		Currently, only token based authetication is supported. 
```
Save your file by pressing CTRL-X in Nano and follow the steps on the screen

Start the iot process. This will start the device in registered mode.

			sudo service iot start



#####Command Support

Now the Raspberry Pi Sample running in registered mode supports receiving of commands sent by an application. 

1.Raspberry Pi supports Reboot Command

2.Application should send the command in the following details
		
		Topic : iot-2/type/<type>/id/<id>/cmd/reboot/fmt/json 
		Payload : 
			{
    				“delay”:<numberOfMinutes>
			}

	numberOfMinutes : minutes to wait before reboot of the device. If the value is not supplied, the device will be rebooted immediately. ```


####Note for users who want to change the code, compile and build the .deb file:

Clone the project into your local Raspberry Pi environment
```
	pi@raspberrypi ~ $ git clone https://github.com/ibm-messaging/iot-raspberrypi.git
	pi@raspberrypi ~ $ cd iot-raspberrypi/samples/c/
```
Make the necessary changes to the c source files and finally build the .deb file, the installer for iot-raspberrypi
```
	pi@raspberrypi ~ $ debuild -b
```

If 'debuild' and ssl lib are not found on your Raspberry Pi, install them and re-build the .deb file
```
	pi@raspberrypi ~ $ sudo apt-get install dpkg-dev build-essential devscripts
	pi@raspberrypi ~ $ sudo apt-get install libssl-dev
	pi@raspberrypi ~ $ debuild -b
```

Now the .deb file will be generated on the directory one level up.

Note: You can ignore the signing errors when the build finishes(lint), as a certificate is required if you want to publish this build to the debian repository. 

####Note for Windows Users:
When you clone/fork this repository,ensure that file permissions for the libraries are set properly and that  "libpaho-mqtt3a.so" and "libpaho-mqtt3a.so.1" are symbolic links. 

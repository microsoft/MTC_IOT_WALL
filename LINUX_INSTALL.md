# OPC UA Industrial IoT Wall Setup - Linux Installation

I have decided to release a dedicated installation guide for a deployment of IoT Edge for IoT Central using Linux (Ubuntu) in place of the Windows 10 deployment. I have decided to do this for two reasons.

- IoT Edge runs on Linux. It does not run on Windows. Deploying on Windows requires a Linux VM (either WSL or Hyper-V) and thus is just a layer that is not neccesary as it adds more complexity, and increases the opportunity for problems.
- The IoT Wall Gateway computer (HP) has limited storage, and runs an older (4th-Gen) Intel Processor. Running Windows 10 on the MTC Floor would show that we aren't able to support the latest OS for our showcase solution with Industrial IoT. And Linux is a better choice of an Edge Computing Device.

>**Please Note:** The OPC UA Wall is an evolving solution and will continue to be improved through the coming weeks and months. I'll be posting a list of roadmap features and showcases that you can leverage for your customer events. If you would like to assist, I'm looking for volunteers. Reach out to me directly.

Thank You

Kevin

## HP Gateway Setup

Download the latest **supported** 64-bit Desktop Image of Ubuntu Linux distrabution (at the time of creating these instructions it was Ubuntu 20.04 LTS) from the Ubuntu [website](https://releases.ubuntu.com/20.04/).

Once you have downloaded the ISO image you will need to write the ISO image to a USB (3.0/3.1) thumbdrive. I used the Raspberry Pi Imager found [here](https://www.raspberrypi.com/software/). An alternative is Win32DiskImager found [here](https://sourceforge.net/projects/win32diskimager/)

Use the thumbdrive to install a fresh image of Ubuntu on the HP Gateway machine. If you have not installed Ubunbtu, there are some great tutorials online. [I like this one](https://ubuntu.com/tutorials/install-ubuntu-desktop?msclkid=7da411b7d0c011ecbac877d8a0a8d735#1-overview)

>Don't forget to update! Make sure you have run through the Ubunutu updates before you continue.


<<TODO>>
### Update Network Settings

*This is a critical step as it must be done prior to the next steps.* The HP Gateway comes with two ethernet ports. Both ports are connected to the same switch, and by default, they both grab an IP Address from whatever DHCP server your wall is connected to. We need to assign a **static IP address** to one of the ports to ensure it can communicate with the devices that are plugged into the IoT Wall.

From Windows, press **Windows + R**. From the Run dialog enter  **control panel** and click **OK**

- From Control Panel select **Network and Internet** and then select **Network and Sharing Center**.
- From there you should see two ethernet connections. Names for these might vary (mine was labeled Ethernet and Ethernet 3). Choose one of the connections and select it.
- From the Ethernet x Status Dialog, click **Properties**
- From the Properties Dialog, click **Internet Protocol Version 4(TCP/IPv4)** and click **Properties**
- From the **Internet Protocol Version 4** dialog, click **Use the following IP address:** and enter the following:
  - IP address: **192.168.30.10**
  - Subnet mask: **255.255.255.0**

> Note: Leave all other values  blank.

- Click **OK** then **Close**

### Install WSL2

You will need to install the Windows Subsystem for Linux v2 (WSL2) by following the steps outlined from [here](https://docs.microsoft.com/en-us/windows/wsl/install). The default installation uses Ubuntu. Do not change the default Linux Distribution as Ubuntu is the only Distro that has been tested.

### Install Docker Desktop

Now, install Docker Desktop from [here](https://www.docker.com/products/docker-desktop). Please note that Docker Desktop should be configured using its WSL2 engine and it should be automatically started when you log in (check the Settings page of the Docker Desktop app running in the System Tray).

## Onboarding Your OPC UA IIoT Wall

The OPC UA IIoT Wall is hosted on IoT Central in the EXP tenant. To onboard your wall, you will need to do the following.

- Login to the [OPC UA IIoT Wall portal](https://opc-ua-iot-wall.azureiotcentral.com/devices) with your **Manager, Product Development - Engineering** persona. For example: In Irvine, it is **Aamil Shammas**. Find the Persona for your Location [here](https://exppeople.azurewebsites.net/)
- Select your location from the list.
- From the Device screen, select **Connect** in the upper left corner of the display. You will need the ID Scope, Device ID and Primary Key, to onboard your Wall during the IoT Edge runtime installation in the next step.
- Now, install the IoT Edge runtime using the IoT Edge Installer from [here](https://github.com/Azure/Industrial-IoT-Gateway-Installer/raw/master/Releases/Windows/setup.exe). No not run the IoT Edge Installer directly from the Web. You will need to download the installer, then run it with Administrator privileges.

- Select **Use Docker Desktop** and **Configure IoT Edge for Azure IoT Central (SaaS**). Provide ID Scope, Device ID and Primary Key from the previous step. Click install.

>The installer will make sure all prerequisites are installed, and you may have to reboot and rerun the IoT Edge Installer should a prerequisite need to be installed first. Eventually, the IoT Edge Installer will complete with "This part of the installation is now complete".
>After a while, the **Edge Agent**, **Edge Hub**, and **Node-RED** Docker modules will be running on the gateway and can be monitored in the Docker Desktop app running in the System Tray.

Before proceeding to the final step, please wait until the Node-RED service is deployed to the wall.

## Deploy the Local Node-RED Flow

The final step is to deploy the Node-RED Flow for the Beckhoff Controller. This process will capture and report the Telemetry from the Beckhoff sensors and services (Temperature, Fan Speed, and Lamp Status) as well as process commands from IoT Central (Enable/Disable the Lamp, Set the Fan Speed from 0-32,000 RPM)

- From Microsoft Edge on the HP gateway, navigate to <http://localhost:1880>

>You will be promted to login to Node-RED. Please see the IoT Teams Channel for this information.
>At this point you might be promoted to **Create Project**. Click **Not right now**

- In the upper right-hand corner of the Node-RED canvas there is a *hamburger* menu option (three lines).
- Click on the hamburger menu and select **Manage Palette**
  - Click on the **Install** tab
  - Search for *node-red-contrib-opcua*
  - Click **Install**, and then click **Install** from the pop-up
  - Once complete click **Close**

>This should take less than 1 minute to complete. You will see a pop-up window with the list of nodes that were installed.

- Click on the hamburger menu and select **Import**
- From the **Import nodes** window
  - Select **Clipboard** and copy and paste the following [JSON](flows/beckhoff-flow.json) into the text area in the window and click **Import**

>At this point you might be prompted for another action. If so, click **Import copy**

- Finally, click **Deploy** in the upper right-hand corner of the canvas.

## Congratulations

Your OPC UA IIoT wall has been deployed and is ready for use. You should see Beckhoff Telemetry being sent to your wall.
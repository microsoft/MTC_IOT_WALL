# OPC UA Industrial IoT Wall Setup

I am proud to announce the release of the Next Generation OPC UA IIoT Wall. This document will provide the steps to ensures a successful rollout. I'd like to thank **Ranga Vadlamudi**, from the IoT Central team for his late night and weekend support. I'd like to thank **Hans Gschoßmann** for his insights to the OPC UA integration. A final thank you to **Ali Mazaheri** and **Chad Gronbach** for their time and leadership while I struggeled to rebuild the OPC UA wall to a amazing, impactful demo and showcase for our customers.

**Please Note:** The OPC UA Wall is an evolving solution and will continue to be improved through the coming weeks and months. I'll be posting a list of roadmap features and showcases that you can leverage for your customer events. If you would like to assist, I'm looking for volunteers. Reach out to me directly. 

Kevin Orbaker

## HP Gateway Setup

The HP gateway installed on the OPC wall must be installed/upgraded with the latest version of Windows 10 Enterprise (currently this is build 21H2). It is best if you do a clean installation of Windows 10. Storage on the HP gateway is limited. Do not install any unnecessary software. Please make sure all updates are completed prior to proceeding with the installation. After all Windows Updates are applied, run Disk Cleanup on the C: drive (include system files) to free up space. 

### Update Network Settings

*This is a critical step as it must be done prior to the next steps.* The HP Gateway comes with two ethernet ports. Both ports are connected to the same switch, and by default, they both grab and IP Address from whatever DHCP server your wall is connected to. We need to assign a **static IP address** to one of the ports to ensure it can communicate with the devices that are plugged into the IoT Wall.

From Windows, press **Windows + R**. From the Run dialog enter  **control panel** and click **OK**

- From Control Panel select **Network and Internet** and then select **Network and Sharing Center**. 
- From there you should see two ethernet connections. Names for these might vary (Mine was labled Ethernet and Ethernet 3). Choose one of the connections and select it.
- From the Ethernet x Status Dialog, click **Properties**
- From the Properties Dialog, click **Internet Protocol Version 4(TCP/IPv4)** and click **Properties**
- from the **Internet Protocol Version 4** dialog, click **Use the following IP address:** and enter the following:
  - IP address: **192.168.30.10**
  - Subnet mask: **255.255.255.0**

> Note: Leave all other values  blank.

- Click **OK** then **Close**

### Instal WSL2

You will need to install the Windows Subsystem for Linux v2 (WSL2) by following the steps outlined from [here](https://docs.microsoft.com/en-us/windows/wsl/install). The default installation uses Ubuntu. Do not change the default Linux Distribution as Ubuntu is the only Distro that has been tested.

### Install Docker Desktop

Now install Docker Desktop from [here](https://www.docker.com/products/docker-desktop). Please note that Docker Desktop should be configured using its WSL2 engine and it should be automatically started when you log in (check the Settings page of the Docker Desktop app running in the System Tray). 

## Onboarding Your OPC UA IIoT Wall

The OPC UA IIoT Wall is hosted on IoT Central in the EXP tenant. To onboard your wall, you will need to do the following.

- Login to the [OPC UA IIoT Wall portal](https://opc-ua-iot-wall.azureiotcentral.com/devices) with your **Manager, Product Development - Engineering** persona. For example: In Irvine, it is **Aamil Shammas**. Find the Persona for your Location [HERE](https://exppeople.azurewebsites.net/)
- Select your location from the list
- From the Device screen, select **Connect** in the upper left corner of thie display. You will need the ID Scope, Device ID and Primary Key, to onboard your Wall during the IoT Edge runtime installation in the next step. 
- Now, install the IoT Edge runtime using the IoT Edge Installer from [here](https://github.com/Azure/Industrial-IoT-Gateway-Installer/raw/master/Releases/Windows/setup.exe). No not run the IoT Edge Installer directly from the Web. You will need to download the installer, then run it with Administrator previlegs.

- Select **Use Docker Desktop** and **Configure IoT Edge for Azure IoT Central (SaaS**). Provide ID Scope, Device ID and Primary Key from the previous step. Click install. 

>The installer will make sure all prerequisites are installed, and you may have to reboot and rerun the IoT Edge Installer should a prerequisite need to be installed first. Eventually, the IoT Edge Installer will complete with "This part of the installation is now complete".
>After a while, the **Edge Agent**, **Edge Hub**, and **Node-RED** Docker modules will be running on the gateway and can be monitored in the Docker Desktop app running in the System Tray.

Before proceeding to the final step, please wait until the Node-RED service is deployed to the wall.

## Deploy the Local Node-RED Flow

The final step is to deploy the Node-RED Flow for the Beackhoff Controller. This process will capture and report the Telemetry from the Beckhoff sensors and services (Temperature, Fan Speed, and Lamp status) as well as process commands from IoT Central (Enable/Disable the Lamp, Set the Fan Speed from 0-32,000 RPM)

- From Microsoft Edge on the HP gateway, navigate to http://localhost:1880
- When propted for login 
  - Username: **admin**
  - Password: **password**

>At this point you might be promoted to **Create Project**. Click **Not right now**

- In the upper right hand corner of the Node-RED canvas there is a *hamburger* menu option (three lines).
- Click on the hambuger menu and select **Manage Palette**
  - Click on the **Install** tab
  - Search for *node-red-contrib-opcua*
  - Click **Install**, and then click **Install** from the pop-up
  - Once complete click **Close**

>This should take less than 1 minute to complete. You will see a pop-up window with the list of nodes that were installed.

- Click on the hamburger menu and select **Import**
- From the **Import nodes** window
  - Select **Clipboard** and copy and paste the following [JSON](https://raw.githubusercontent.com/msftkevin/iot-wall/main/beckhoff-flow.json?token=GHSAT0AAAAAABSK6YTHR736RPFHHCE73P3IYTUJ25A) into the text area in the window and click **Import**

>At this point you might be prompted for another action. If so, click **Import copy**

- Finally, click **Deploy** in the upper right-hand corner of the canvas.

## Congratulations

Your OPC UA IIoT wall has been deployed and is ready for use. You should see Beckhoff Telemetry being sent to your wall.

## Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Trademarks

This project may contain trademarks or logos for projects, products, or services. Authorized use of Microsoft 
trademarks or logos is subject to and must follow 
[Microsoft's Trademark & Brand Guidelines](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general).
Use of Microsoft trademarks or logos in modified versions of this project must not cause confusion or imply Microsoft sponsorship.
Any use of third-party trademarks or logos are subject to those third-party's policies.


## Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Trademarks

This project may contain trademarks or logos for projects, products, or services. Authorized use of Microsoft 
trademarks or logos is subject to and must follow 
[Microsoft's Trademark & Brand Guidelines](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general).
Use of Microsoft trademarks or logos in modified versions of this project must not cause confusion or imply Microsoft sponsorship.
Any use of third-party trademarks or logos are subject to those third-party's policies.

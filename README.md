# RN487x-BM7x-Firmware-Upgrade-using-Host-MCU

## Introduction
The general firmware update process in the RN4870/1 and BM70/71 PICtail board is carried out using an isupdate flashing tool. The instruction guide for the RN and BM devices discusses in detail about the firmware flashing using the system based flashing tool. Based on Microchip’s continuous improvement policy we are focused on collecting the feedback from the customers, to fulfill the field requirements to make the product more reliable to the end users. Taking into consideration about few practical requirements that demand for an embedded application to execute firmware update for these devices, we have developed an alternative solution to execute firmware update for the controller devices.

   The alternative approach is designed to achieve the same task using an embedded application that execute the firmware update on the RN4870/1 and BM70/71 PICtail board is by using the host MCU.  The embedded application implements the firmware update protocol using the Host Controller interface. This document discusses in detail about the application execution and step by steps procedure to achieve the firmware update via the host MCU using UART based device firmware update protocol.
Microchip releases periodic updates to the application firmware to include new features and fix any bugs reported in previous release. These updates are provided with a release note along with the firmware files in the official webpage.

## Updating PICtail Firmware via tool 
Generally, the Firmware for the RN4870/71 PICtail can be updated using a PC Tool isupdate.exe over the USB port. The latest RN4870/71 firmware images and the isupdate.exe tool are available from the product web page at www.microchip.com/RN4870.

To update the firmware on the RN4870 PICtail, perform the following steps:

1.  Download the firmware zip file from the product web page and extract the contents.
      The zip file contains the isupdate.exe utility and a folder including the firmware images.
2. Connect the PICtail to the host PC using the micro USB cable.
3. Verify that SW7 is set to ON position.
4. Press SW5 Reset button and verify that LED1 is showing a non-flashing blue that
     indicates the RN487x module is in programming mode.
5. Launch the isupdate.exe application 
      - Select the COM port used by the PICtail board
      - Verify that other settings (baud, memory type, and address) 
6. Click the Connect button.
7. Update the firmware.
 
<p align="center">
<img src="https://user-images.githubusercontent.com/57740485/98047569-23b2bd00-1e52-11eb-86e9-01f3e4812eef.png" width=480>
</p>









# Firmware update using host MCU    

  ## Host Controller Interface
       
   The host and the controller communicate using the Host Controller Interface (HCI) protocol. This protocol is based on commands and events, i.e., the host sends (acknowledged) commands to the controller, and the controller uses events to notify the host. The HCI protocol can use different physical transports such as UART, SPI, and USB. The types of packets used are HCI command packet, HCI flash/data packets, HCI event response. 
   
   ## Memory programming
  
   The firmware update protocol follows the below described steps to access the memory of the device and execute the firmware update procedure. 
1.	Enter memory programming Mode: This step involves setting the module into the Flash programming mode. This is primarily achieved by setting the P2_0 pin to ‘0’ via hardware. Once entered, in the next subsequent steps, the host uses the UART transport layer to communicate the HCI Commands and HCI-ISDAP Flash commands to read, write or erase data in the memory
2.	Connect to the Flash: In this step, using the HCI commands, the external host can connect to the Flash in the device. The HCI event responses from the device, the user can verify that a successful connection is established with the Flash. 
3.	Flash operations: Using the HCI-ISDAP Flash commands, operations on Flash, such as, read, write and erase can be performed. 
4.	Disconnect from Flash: After the successful execution of flash memory operations, the external host needs to disconnect from the Flash by sending the relevant HCI commands to the device. The HCI event responses from the device can be used to verify the operation.






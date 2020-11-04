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


# Device Firmware Upgrade Process
## Entering Flash Programming Mode 

For memory programming, the minimum set of hardware connections needed to interface an external host to the BM70/71 is shown in figure below.  Making these hardware connections between the host and controller will allow a host to control the behavior (i.e. enter memory programming mode, enter test mode, communicate, and calibrate) of the module.

<p align="center">
<img src="https://user-images.githubusercontent.com/57740485/98063459-1c9da600-1e76-11eb-8384-366d4a6b4188.png" width=480>
</p>

   The controller operation, or mode, is determined by the level of a hardware pin, P2_0.  This pin is sampled when the RST_N pin goes active.  The RST_N signal must be active for the minimum time, to make sure the pin P2_0 logic level is latched into the IC correctly. Once the controller enters the applicable mode, communication over the UART interface becomes active. The data or protocol which is used to communicate between the host and controller is based on the mode the controller enters after a reset.   
At a high level, the memory programming mode is entered when pin P2_0 is latched by the controller at a logic level ‘0’.  
The application, or run mode, where general BTLE operation is available, is entered when pin P2_0 is latched by the controller at a logic level ‘1’. Table 1 below summarizes the use of the P2_0 pin.

<p align="center">
<img src="https://user-images.githubusercontent.com/57740485/98063851-fc221b80-1e76-11eb-857f-b605e77ca115.png" width=480>
</p>

## Commands
  To flash a new firmware revision into the Flash, the user needs to follow the command protocol. The protocol is based on the HCI command protocol outlined in the Bluetooth specification (www.bluetooth.org) volume 2, Part E, “Host Controller Interface Functional Specification” and Volume 4, Part A, “UART Transport Layer. 

   In general, the commands and responses for memory programming of the BM70/71 can be classified into three categories:
   
1. Command packets
* Connection Packet
* Disconnection Packet
2. Data Packet
* Erase Packet
* Read Packet 
*  Write Packet
3. Event Response Packet

# Connecting to the Flash
Connection command is used to create a connection to a remote device.  With regards to memory programming, the host uses this packet to establish a programming session with module.  The parameters in the packet are all set to zero, which indicates to the BM7x device, a programming connection is requested by the host.

-The HCI event responses from the device helps to verify that a successful connection is established with the Flash.
## Connection Packet
The host uses this command to establish a memory programming session with the module.

<p align="center">
<img src="https://user-images.githubusercontent.com/57740485/98064496-730be400-1e78-11eb-8167-6ebaf1833958.png" width=480>
</p>

## Connection Response format, module--->host
The module will respond to this command with an HCI Event Response 

<p align="center">
<img src="https://user-images.githubusercontent.com/57740485/98064616-b49c8f00-1e78-11eb-9b5b-b431f5c950c8.png" width=480>
</p>

## Connect Sequence
<p align="center">
<img src="https://user-images.githubusercontent.com/57740485/98064672-dac22f00-1e78-11eb-935c-72c22fbaf12b.png" width=480>
</p>

## Flash operations
After creating a programming session, the host next proceeds to execute flash operations using the HCI-ISDAP commands. 
## Data Packet 
The data packet can be used for the read, write and erase operations. Each operation uses a different command ID. 
<p align="center">
<img src="https://user-images.githubusercontent.com/57740485/98064850-51f7c300-1e79-11eb-92f0-8dd97785e045.png" width=480>
</p>

•	Command ID
* Erase - 0x0112
* Write - 0x0111
* Read - 0x0110


## Alternate data packet
This packet type is mainly used for writing continue operation.

<p align="center">
<img src="https://user-images.githubusercontent.com/57740485/98065116-de09ea80-1e79-11eb-8283-58f265e24968.png" width=480>
</p>

There are 3 type of write operation
* Initiate a write start command for the single write 
* Then uses the write continue packets to write data 
* Write stop can be used to stop the data write process 

<p align="center">
<img src="https://user-images.githubusercontent.com/57740485/98065769-3988a800-1e7b-11eb-9d70-01f0e39fe019.png" width=480>
</p>

Data will be sent in chunks; whose size gets added to packet length. If write continue operation follows this operation, then data will be appended after the data packet. The command ID session can be used to indicate the write continue operation. If this flag is set to a ‘1’, all subsequent write messages must be sent as Write 
## Response sequence, module--->host
The module will respond to the command with an HCI Event Response  

<p align="center">
<img src="https://user-images.githubusercontent.com/57740485/98065856-6f2d9100-1e7b-11eb-9ea4-dd626c637132.png" width=480>
</p>

## Disconnect 
After completing the Flash write process for flash the firmware files, the host needs to disconnect from the flash using the following command to end the programming session.  
## Disconnect packet

<p align="center">
<img src="https://user-images.githubusercontent.com/57740485/98065971-b156d280-1e7b-11eb-82d0-987e7d2b67bf.png" width=480>
</p>

##Programming Sequence
         		 The demo application implements the firmware update protocol by incorporating the data into packets which creates the data and command packets as described in the Host Controller Interface specifications. The event response from the device is then monitored by the host to check on the status notification send back from the device. The programming sequence of the demo application is described in below diagram

<p align="center">
<img src="https://user-images.githubusercontent.com/57740485/98066030-d77c7280-1e7b-11eb-9a9d-e4ba8d72d8bb.png" width=480>
</p>

## DFU Demo Application
   The embedded MPlabx application is created that implements the firmware update in the RN4870/1 and BM70/71 PICtail board using the host MCU which follows the device firmware update protocol. The Explorer 16/32 development Board with the PIC32MX795F512L is used as the host MCU.
                
   The DFU Demo application project runs on supported Microchip development hardware noted in the Hardware Combinations section below. Pre-generated hex file for PIC32 ‘DFU_Demo.X.production.hex’ is available in the ‘\Applications\DFU_Demo\Precompiled_HEX’ folder.


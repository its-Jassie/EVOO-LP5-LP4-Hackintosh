#  Evoo LP4 and LP5 Hackintosh Dual Boot Install Guide
A guide to install MacOS alongside Windows on the EVOO LP5 probably also LP4 and possibly help with other models from the creator of the [definitive guide for LP5 heat, battery, and power managment](https://www.reddit.com/r/EVOOGaming/comments/gomcd2/the_definitive_guide_for_lp5_heat_battery_and/?utm_source=share&utm_medium=web2x&context=3) guide (windows)

## What is not working

- 1660ti (MacOS does not support this hardware)
- HDMI/mini DP (these ports are connected directly to the 1660ti which is disabled)
- AirDrop & Handoff & Wireless Sidecar (Would need a native MacOS WiFi card to work)
- SD Card reader
- Turbo button

## What is partially working

- Intel Wi-Fi (slightly finicky and limited at about 30mbps down)

## What is working

- Intel UHD 630 Graphics with 3072 MB VRAM
- Pretty much everything else


### Required Tools:

USB Stick 16GB or larger

[GibMacOS](https://github.com/corpnewt/gibMacOS)

[Tongfang Hackintosh Utility.exe](https://github.com/kirainmoe/project-starbeat/releases) (installed on windows)

[DiskGenius](https://www.diskgenius.com/download.php) (For editing EFI/UEFI/ESP boot loader)

A second drive dedicated to MacOS (may be possible to do on one drive but I haven't attempted)

An ethernet connection for initial installation

# Step 1: BIOS Changes

 Disable Secure Boot, Disable Launch CSM and change to UEFI Mode in BIOS (F2)

# Step 2: Setup MacOS installer
  
  Follow [this guide](https://khronokernel.github.io/Opencore-Vanilla-Laptop-Guide/installer-guide/winblows-install.html) to make the USB installer with GibMacOS
  
  #### MAKE SURE YOU CHOOSE RECOVERY ONLY WITH `R` AND PICK THE RECOVERY IMAGE FROM THE LIST WITH THIS CRITERIA:
  - `FULL INSTALL` should be at the end of the first line
  - `(19G73)` should be just below it at the end of the second line 
 
 
 # Step 3: Configuring USB installer
 
 We are going to make two different configurations using the Tongfang Hackintosh Utility, one for the USB and one for the ESP later. 
 
 In the configuration tab make sure EVOO LP5/LP6 is selected.
 
 Under Kext Injection check `Intel Bluetooth Support` and `4K resolution monitor` then the big blue button to download the Tongfang_EFI folder to the desktop. Go and rename that folder to Tongfang_USB
 
 Back to the Utility now select `Intel Bluetooth Support` and `4K resolution monitor` and `Intel WiFi Support` and then blue button to make a new Tongfang_EFI folder. So now we should have Tongfang_USB and Tongfang_EFI on the desktop
 
 Copy the contents of Tongfang_USB(BOOT and OC) to the EFI folder in the BOOT usb drive that we made in step 2 and now you should be ready to install.
 
 # Step 4: Install MacOS
 
 Press F12 while booting to enter the MacOS installer we just made. Choose MacOS base isntall system and wait a few minutes for the progress bar to appear then load. If it does not load then see troubleshooting section(WIP).
 
 Use Disk Utility to erase your second drive and format to APFS. Name the drive something like Mac or MacOS or HacOS because it will be the name of the boot option later when we set up dual boot.
 
 After formatting the drive you can close disk utility and go onto install MacOS Catilina and select your APFS drive as the install location.
 
 Installation should take about 20-30 minutes or so. When it is done set up MacOS then shut down and boot back into windows for the next step.
 
 
 # Step 5: Setting Up Dual-Boot (the hard part)
 
 Now that we have MacOS setup you will notice that when you turn off your EVOO and turn it back on you will be booted into Windows by defualt. We have to install OpenCore to ESP to dual boot.
 
 Use DiskGenius in Windows to be able to access the ESP partition. In the ESP partition there should be a folder called EFI which contains Boot and Windows folders.
 
 Back to the Tongfang_EFI folder we made in step 3 we need to navigate to `/Tongfang_EFI/BOOT/` and rename `BOOTx64.efi` to `OpenCore.efi`
 
 Drag the BOOT and OC folder from Tongfang_EFI to `/ESP/EFI/` in DiskGenius. Tongfang_EFI has intel WiFi drivers which Tongfang_USB does not because it causes some issues with the USB installer. 
 > Note: I am not sure if it is necassary but I also add BOOT and OC to the EFI partition on second drive (the MacOS one) also using DiskGenius. I do not add this one to the boot entry though. This should allow you to boot to MacOS if you ever format or remove the Windows drive
 
 Under Tools in the toolbar `set UEFI Boot entries` to add OpenCore.efi from the ESP partition to the boot table and name it OpenCore before saving the current boot entry and then clicking restart now.
 
 When restarting go back into the BIOS (F2) and navigate to `UEFI Hard Disk Drive BBS Priorities`. Open it and set Boot Option #1 to OpenCore and Boot Option #2 to Disable
 
 Save BIOS configuration and when you restart you should be presented with the option of Windows or whatever you named your APFS drive. Congratulations! You are now dual-booting MacOS!
 
 # Step 6: Finishing Touches 

Boot into your MacOS install and connect the ethernet one last time. Download the [Tongfang Hackintosh Utility.app](https://github.com/kirainmoe/project-starbeat/releases) and [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases)(v1.0.0)

Install and open HeliPort to get that sweet sweet WiFi and you can finally unplug your Mac from the router

Install and open the Tongfang Hackintosh Utility.app and under the Tools tab do `Fix Sleep` and `Install Fn Daemon`



## Looking for a better WiFi solution?

Well you could replace the intel WiFI card that comes with our laptop to a support Broadcom WiFi adapter (DW1830, DW1860, DW1820A, maybe some more) You may also be able to get Handoff/Airdrop/WirelessSidecar working this way.

## Undervolting like we do in Windows(ThrottleStop) LP5 ONLY MAY NOT WORK ON LP4 or LP6

[VoltageShift](https://sitechprog.blogspot.com/2017/06/voltageshift.html) is the answer! Its basically a command line undervolting tool for MacOS. Grab the kext from the download link and place it in a folder together where it can stay permanatly. I put mine in the applications folder.

There is a codesig issue with the other file so we have to replace it with one that someone stripped the codesig out of for us [here](https://disk.yandex.com/d/l2cxoKNB8KgVVg). Go ahead and place the voltageshift file in Applications(or wherever you put the voltageshift.kext)

cd into the directory where both files are (ex: `cd /Applications`). Now we need to set some files permissions with the following two seperate commands
`chmod +x voltageshift` (should turn the voltageshift file into an exec file)
`sudo chown -R root:wheel build/Release/VoltageShift.kext`

Then set the following offset and use your computer for a few minutes to ensure there is no crash,
`./voltageshift offset -250 -75.2 -119.1 -75.2`

If your Hackintosh is running well for a few minutes then you can set voltageshift to run at startup and then you are set!
`./voltageshift buildlaunchd -250 -75.2 -119.1 -75.2 0 0 20`

## Troubleshooting
WIP

## That's all folks!

Thats all I have so far. I may add a troubleshooting guide here soon (mostly just explaining how to enable verbose booting) and also a slightly modified guide to install MacOS exclusivley without Windows to a Wiki page in this repository. I may also make an undervolting guide for MacOS to match what ThrottleStop does on Windows

Other than that a lot of people may be wondering why I even bothered to do all this if I can't use the 1660ti on MacOS and the answer is mostly because it's fun but also because I wanted Sidecar support to use my iPad as a drawing tablet so I can teach myself Adobe Animate.

In any case thanks for reading! I hope you are able to enjoy MacOS on your EVOO as well as I do. I am amazed by how much usage I have gotten out of this cheap Walmart gaming laptop and I can't wait to see what I can do with it next

Big thanks to u\khanofkhronos for showing me the Tongfang utility over on the r/EVOOGaming subreddit!

 
 
 
 
 
  
  

# Installation and Setup of Windows 11 VM sandbox in VirtualBox

**Systems**: 	VirtualBox 7.2, Windows 11 host

**Author**: 	    Tide Adewunmi

**Date**: 	      July 13, 2026

**Est. Time**:   	45-75 mins (includes 30-45 minutes for ISO download)

**Prereqs**: 	      Your personal computer, Internet connection, storage (~80 GB), RAM (8+ GB)


The objective of this project is to install and set up a Windows 11 virtual machine (VM) on VirtualBox as a sandbox for future home lab projects. Before downloading anything, ensure your host computer has enough storage and RAM to allocate to the virtual machines. VirtualBox must be run on your host computer, but your ISOs and VMs can be kept on an external storage device.

## VirtualBox Installation
To install VirtualBox, click on this link. Select the package for your host computer (e.g., Windows hosts). Once you select a package, VirtualBox starts downloading. Once it is done, locate the folder in File Explorer where your VirtualBox installer is downloaded. Once located, double-click the installer. Ignore the message about Networking, click Next, agree to the Terms and Agreement, then click Install. Once VirtualBox is installed, it is ready to set up the Windows 11 virtual machine.

<img width="626" height="363" alt="image" src="https://github.com/user-attachments/assets/89dc3f51-6a33-41ee-a551-430d20eb2980" />

**Figure 1**: VirtualBox Downloads page showing the Platform Packages.


## Windows 11 ISO Installation
To install the Windows 11 ISO file, go to this [link](https://www.microsoft.com/en-us/software-download/windows11) and scroll down to “Download Windows 11 Disk Image (ISO) for x64 devices” (shown in Figure 2.1). In the dropdown tab, select Windows 11 (multi-edition ISO for x64 devices), and select the Confirm tab below it.

<img width="545" height="429" alt="image" src="https://github.com/user-attachments/assets/8b0c2ef5-c124-4386-98a8-c01c59a1e924" />

**Figure 2.1**: Download Windows 11 ISO image.


Next, select your preferred product language and click the Confirm tab. Select the 64-bit Download tab, and the Windows 11 ISO file download begins. The ISO file is valid for 24 hours, so be sure to install within that time (Figure 2.2). Once the download is complete, you can move on to the Windows 11 virtual machine setup in VirtualBox Manager. 

<img width="542" height="368" alt="image" src="https://github.com/user-attachments/assets/ab301cd2-bb71-4645-9c90-e9d0c0ba0e20" />

**Figure 2.2**


## Windows 11 Setup on VirtualBox
In VirtualBox, select New to build a new virtual machine, and you will see a window similar to the image below (Figure 3.1). First, provide a name for your virtual machine (e.g., Windows 11). Ensure the VM Folder has the proper path where your VMs will be stored. You must determine whether to use your personal computer’s storage or an external storage device to host your VMs. 


<img width="559" height="356" alt="image" src="https://github.com/user-attachments/assets/2fc46dcc-2875-44ea-a46a-fcc8d0b15d7c" />

**Figure 3.1**: Virtual machine name and OS section in VirtualBox Manager.


Next, select the Windows 11 ISO image (e.g., `Win11_25H2_English_x64_v2.iso`) you just downloaded, and the OS and OS Version will fill in automatically. Unless you actually have a Product Key, do not select Proceed with Unattended Installation. If selected, unselect it.


<img width="581" height="399" alt="image" src="https://github.com/user-attachments/assets/d98dab0b-8fb9-477c-a103-84a0e8a5fcea" />

**Figure 3.2**: Specify virtual hardware section in VirtualBox Manager.

	
Under the ‘_Specify virtual hardware_’ section, adjust the Base Memory and Number of CPU cores your Windows 11 VM can borrow from your host computer to run effectively when it is in use. 
- **Base Memory**: 4096 MB (4 GB) by default
- **Number of CPUs**: With the latest version of VirtualBox (version 7.2), there have been errors or bugs with VirtualBox setting up Windows 11 with 1 or 2 cores, so allocate **at least 3 or 4 cores**, or the screen will be black when you start the VMs [Check Known Issues]. 
- **Use EFI**:  Make sure Use EFI (Extensible Firmware Interface) is enabled, since Windows 11 needs **UEFI/EFI** and **Secure Boot** to function efficiently. This is also necessary for other OSes.


<img width="612" height="418" alt="image" src="https://github.com/user-attachments/assets/cfbb0d13-87f4-4fe9-a8ea-6bd680c0a3d5" />

**Figure 3.3**: Specify virtual hard disk section in VirtualBox Manager.


Under ‘Specify the virtual hard disk’, adjust the disk size for the VM to at least 64 GB for sufficient space. Notice you can also use the existing virtual hard disk file as another VM. Once all these steps are completed, select the Finish tab. 


**Note**: A VM’s settings are not permanent. They can be changed at any time, though they must be powered off. So, should you ever select Finish without setting the hard disk size or specifying the virtual hardware, you can still make the necessary changes by selecting the Windows 11 VM and selecting Settings (the orange gear icon), typing `Ctrl+S`, or right-clicking on the Windows 11 VM and selecting Settings. 

Warning: Before hitting the Start button on VirtualBox to start your Windows VM sandbox, you must make the following changes in its Settings to avoid issues booting and setting up the Windows VM. 


Before starting the Windows 11 VM, open _**Settings → Expert**_ and configure:
- **System → Motherboard Tab**
	- **Base Memory (RAM)**: Allocate at least `4096 MB` (**4 GB**). If your physical host PC has 16 GB or more, give it `8192 MB` (**8 GB**) for a much smoother experience.
	- **Chipset**: PIIX3. 
	- **Features**: Check UEFI, Secure Boot, and I/O APIC. Uncheck _Enable Hardware Clock in UTC Time_.
- **System → Processor Tab**
	- **Processor(s)**: Allocate 3 or 4 CPUs. (Check _Known Issues_).
- **Display → Screen Tab**
	- **Video Memory**: `128 MB`
 	- Graphics Controller: `VMSVGA` or `VBoxVGA`.

<img width="581" height="545" alt="image" src="https://github.com/user-attachments/assets/dfd0b7dd-3b6d-438e-8ad0-d88c537ad10d" />

**Figure 3.4** 


## Inside the Windows 11 VM
Select **Start** to turn on the Windows 11 virtual machine → Windows 11 Pro recommended for a professional-environment-like build.


## Known Issues
| Symptom | Cause | Fix | Source |
| -------- | ------| -------| ------|
| Black screen freeze on VM start | VirtualBox 7.2 bug with 1-2 CPU cores on Windows 11 guests | Allocate 3-4 CPU cores in Settings → System → Processor | [VirtualBox forums]( https://forums.virtualbox.org/viewtopic.php?t=113950) |

Root cause of Windows VM’s black screen upon startup narrowed down via AI-assisted research (Google Gemini), confirmed against a VirtualBox forum thread.

## Moving a VM to external storage
To move your VMs to an external storage device, do not do so manually in File Explorer since this can corrupt your VMs’ data. VirtualBox has a built-in Move feature that can do that for you safely and effectively. First, ensure the VM is completely powered off to prevent data corruption. Next, simply right-click the VM you wish to move (e.g., Windows 11), select Move, and browse to the new folder. Once selected, VirtualBox Manager will automatically copy your files to the new folder. You can check to make sure everything is working well.

## Sources
- **VirtualBox Downloads**: https://www.virtualbox.org/wiki/Downloads
- **Windows 11 ISO**: https://www.microsoft.com/en-us/software-download/windows11
- VirtualBox Windows 11 Black Screen Solution:
	- **From 2 CPU cores to 3 or 4 cores**: https://forums.virtualbox.org/viewtopic.php?t=113950
- **VirtualBox Move feature**: https://docs.oracle.com/en/virtualization/virtualbox/6.0/relnotes/move-vm.html

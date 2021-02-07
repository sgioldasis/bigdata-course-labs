# Oracle VirtualBox installation and VM Set Up

## **Step 1: Download VirtualBox**

Start your browser and navigate to [virtualbox.org](https://www.virtualbox.org/)

![Virtual Box website](images/01_virtual_box_org.png)

Click on 'Download VirtualBox 6.1' (or the current version)

On the download page click the link for your operating system

> For this installation, **Windows 10** will be used as the host operating system

![Host Operating system Selection](images/02_virtual_box_host.png)

Download the installation executable to the directory of your choice

![Download the executable](images/03_download.png)

## **Step 2: Install VirtualBox**

Open the installation executable and click 'Next' on the following prompt:

![Click Next](images/04_Install_Next.png)

Leave the installation options as they are and press 'Next':

![Install Options](images/05_install_defaults.png)

Also leave the following options selected. Especially the 'Register file associations' option. Any files that are associated with VirtualBox will only open with VirtualBox.

Press 'Next'

![Installation Default Options](images/06_install_options.png)

The following step will install drivers for Internet access on VirtualBox and will disconnect from the Internet maybe for a few seconds.

Press 'Next'

![Network Interfaces](images/07_Network.png)

Press 'Install' on the following prompt:

![Install Prompt](images/08_Install.png)

The installation will begin:

![Installation starts](images/09_Install_Start.png)

Also press ‘Install’ if a Windows Security prompt like the following appears

![Device Software Prompt](images/10_device_software.png)

Let the installation finish. There is chance that it might take some time...

Press 'Finish' (leaving the option to start the Oracle VirtualBox selected)

![Installation Finished](images/11_VB_install_finish.png)

That VirtualBox starts!

![VB Started](images/12_VirtualBox.png)

## **Step 3: VirtualBox Extension Pack Set Up**

It is recommended to install the Extension Pack. It will expand the capabilities of the Virtual Machine providing support for USB 2.0 and 3.0 (communication with USB peripherals of the host computer) and Host Webcam Support among other things.

Navigate again to the [VirtualBox downloads page](https://www.virtualbox.org/wiki/Downloads).

Click on 'All Supported Platforms' under 'Oracle VM VirtualBox Extension Pack'

![Extension Pack Download](13_Extension_Pack_Download.png)

Select 'Open With [VirtualBox Manager (default)]' and press 'OK'

![Extension Pack Open](images/14_Extension_Pack.png)

This will automatically start VirtualBox (if not already open). Press Install

![Extension Pack Install](images/15_Extension_Pack_install.png)

Scroll to the end and press 'Agree' to 'VirtualBox Extension Pack Personal Use and Evaluation License (PUEL)' screen

![Extension Pack License](images/16_Extension_Pack_License.png)

The installation will complete in seconds. Press 'OK'

![Installation Finished](images/17_Installation_Finish.png)

### **Great Job! You have successfully installed Oracle VirtualBox!**

Next you will set up the provided Virtual Machine that you download from ![VM Location](https://www.test.com)on VirtualBox...

## **Step 4: Virtual Machine Set Up**

Start VirtualBox

Press the 'New' button. It should look like this:

![Start VirtualBox](images/18_Start_Virtual_Box.png)

A popup screen appears:

![New Virtual Machine Screen](images/19_New_VM_01.png)

If it does not appear automatically, press **'Expert Mode'**

Enter the configuration details of the new VM

> - A descriptive name
> - Operating System of the VM on the **'Type'** drop down list (Linux in this case)
> - The version of the operating system (_Ubuntu 64_)
> - The amount of memory that will be allocated to the VM

![VM Configuration](images/19_New_VM_02.png)

Finally, on the **'Hard disk'** section, select '_Use an existing virtual hard drive file_' and from the yellow folder icon select the **.vhd image** that you downloaded as follows:

![Use existing VM](images/19_New_VM_03.png)

Press the 'Add' button on the popup screen

![Add VM](images/19_New_VM_04.png)

and vavigate to the location that the **.vhd image** was downloaded and press 'Open'

![Select VM](images/19_New_VM_05.png)

On the next screen, highlight the VM and press 'Choose'

![Choose VM](images/19_New_VM_06.png)

Press 'Create' on the next screen

![Create VM](images/19_New_VM_07.png)

The VM is created as it is shown bellow:

![VM CREATED](images/19_New_VM_08.png)

### **Starting the Virtual Machine**

To start the VM, press the **'Start'** arrow

![VM CREATED](images/20_Start_VM.png)

The VM is starting...

After a short while the VM environment will appear

![VM started](images/21_VM_Started.png)

> **Full Screen Mode**
>
> By pressing _'Right Ctrl + F'_ you can switch between full screen and windowed mode

### **Stopping the Virtual Machine**

**After you have saved all your work**, open a terminal window:

![VM started](images/22_VM_Terminal.png)

Enter the command: **_shutdown now_** and press 'Enter'

![VM started](images/23_VM_Shutdown.png)

Now the VM is stopped

## **Congratulations! You Virtual Machine is ready for use. HAPPY LEARNING!!**

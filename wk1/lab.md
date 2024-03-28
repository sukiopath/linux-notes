# Week 1 2420

## Labs

There is no graded Lab this week. However, you need to have your VM setup before the start of next week's class. We will be using them in class.  
If you don't have your VM setup, you won't be able to follow along or complete next week's labs.

**==Important==**
- **DON'T** create your VM in your home directory!   
- **DON'T** create your VM in the same directory where you are storing your notes.   
- **DO** create a brand-new directory in the Documents and create it there.

### Software to install

- [VirtualBox](https://www.vagrantup.com/)
- [Vagrant](https://www.virtualbox.org/)

When you are installing Vagrant and VirtualBox accept the defaults.

For Windows users only
- You may need to configure SSH
	- [Windows 10](https://www.youtube.com/watch?v=g2I6en4Mdjo)
	- [Windows 11](https://www.youtube.com/watch?v=aT-xCPQZRcQ)
	- You will just need the client
- [Windows Terminal](https://github.com/microsoft/terminal)

Use [this](https://app.vagrantup.com/generic/boxes/ubuntu2210) Vagrant box for your VM.
After you have installed VirtualBox and Vagrant, and the Windows Terminal (and possibly enabled the SSH client). 
- Open up the Windows terminal, use the `cd` command to change into your Documents directory. 
- Use `mkdir` to create a new directory. Something like `mkdir 2420-VM-1` (notice the lack of spaces)
- Use `cd` to change into the directory that you just created.
- Now run this command `vagrant init generic/ubuntu2210` 
	- This will create a new 'Vagrantfile' this is the main configuration for the VM that you are going to create
-  Finally, run this command `vagrant up`
	- This will run some VirtualBox commands and create your VM.
- If this runs without error you are ready to test out your VM
	- Run the command `vagrant ssh` 
		- This will connect to your new VM using ssh (we will talk about ssh more later)
		- ==IMPORTANT== You need to run those vagrant commands in the directory where that 'Vagrantfile' you created with `vagrant init` is.
		- Vagrant uses this config file to configure your VM, and it allows Vagrant to know which VM you want to start, shutdown, destroy...
If everything worked, you are ready for next week.
If something went wrong, double check that you haven't missed a step.  
If you are sure you didn't miss a step move on to troubleshooting below.

## Exiting and shutting down your VM

When you are connected to your VM enter the command `exit`. You should return to your regular prompt.  
Next type `vagrant halt`. This will shutdown your VM.

## Troubleshooting

**Vagrant Commands**  

It takes a while to remember vagrant commands, if you just enter `vagrant` or `vagrant help`.  
You will be presented with a list of vagrant commands and a short description.

**General**

The most common problem is you run the command `vagrant up` and it hangs at "SSH auth method: private key".  

The first thing to check is that the SSH client is enabled. There are two links for Windows 10 and 11 above.  
If this is enabled, you should have everything you need. If not, enable the ssh client and reboot.  
This is the easiest thing to check and change.

The next thing to check is a little more complex. Is virtualization enabled in your BIOS?  
How you go about doing this will be different for most laptops. I have several Lenovo laptops and not all of them are the same.
Google how to enter the BIOS for your laptop make and model.  
Once you are in the BIOS look for Intel VT, VT-x or AMD-V.  
While you are in your BIOS you may also need to enable 'hardware acceleration'.

Finally, You may need to set some Windows features:
- Virtual Machine Platform = on
- Windows Hypervisor Platform = off



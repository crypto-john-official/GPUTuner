
# Nvidia GPU BIOS update on Ubuntu OS

Before the update you need to unload Nvidia kernel driver, then no OS UI will be available after that. That is why you need secondary machine.

## Two machines needed

- First PC is main PC with Ubuntu OS, where your GPU set that you want update bios
- Second machine is any machine that has `ssh client` application from which you will connect to first PC and execute commands to update bios. It can be Windows, another Linux, MacOS, and even iOS or Android device.

## First PC setup

1. Ubuntu 20.04 installed
2. Nvidia GPU driver installed
3. Download **Linux** version of `nvflash` tool from https://www.techpowerup.com/download/nvidia-nvflash/
4. Install `ssh-server` on the system

```bash
sudo apt-get remove openssh-client
sudo apt-get install openssh-server
```

5. Find ip address of your PC in *Settings -> Network -> Details* and there `IPv4 Address` to that we will connect later from second machine
6. Close all GUI apps as they will be exited on Nvidia kernel driver unloading

## Second machine setup

1. Install any `ssh client` you like. I will go with Windows OS and install there `Bitvise SSH Client` https://www.bitvise.com/ssh-client-download.
2. Open `ssh client` and set it up for first PC's ip address + user login. For `Bitvise SSH Client` it looks like this

<img src="./images/ssh-client-update-bios-01.png" width="80%"/>

where first PC ip address `192.168.88.70` and username `john`. Then make a `Log in`.

<img src="./images/ssh-client-update-bios-02.png" width="80%"/>

Click `Accept and Save`. And then type your password to the next opened window

<img src="./images/ssh-client-update-bios-03.png" width="80%"/>

On success login then on the left panel the `terminal` icon will appear that you should open next. And now we are in first PC.

<img src="./images/ssh-client-update-bios-04.png" width="80%"/>

## Update BIOS

### Prepare Ubuntu system for GPU bios update

- I've downloaded `nvflash` to *Downloads* directory on main PC, will go there first

<img src="./images/ssh-client-update-bios-05.png" width="80%"/>

- To give the execution permission for `nvflash` make command as

```bash
chmod +x nvflash
```

<img src="./images/ssh-client-update-bios-06.png" width="80%"/>

- Ask `nvflash` for GPUs list

```bash
./nvflash --list
```

<img src="./images/ssh-client-update-bios-07.png" width="80%"/>

and we got the error that we need execute this command from root (superuser/administrator)

- Ask `nvflash` for GPUs list as root user where we have to enter user password

```bash
sudo ./nvflash --list
```

<img src="./images/ssh-client-update-bios-08.png" width="80%"/>

and result said that there we have one GPU with index `0`

<img src="./images/ssh-client-update-bios-09.png" width="80%"/>

- Now save the original bios for backup via command

```bash
sudo ./nvflash --save backup.rom -i 0
```

<img src="./images/ssh-client-update-bios-10.png" width="80%"/>

and we got the error that we should unload nvidia kernel driver first

- With some experiments, I found ou that first we need to disable GPU persistent mode 

```bash
sudo nvidia-smi -pm 0
```

<img src="./images/ssh-client-update-bios-11.png" width="80%"/>

- Then turn off whole Ubuntu graphical interface using command as

```bash
sudo systemctl isolate multi-user.target
```

after this command the PC monitor will be blank (black), it is okay, don't worry.

<img src="./images/ssh-client-update-bios-12.png" width="80%"/>

- Try one more time save backup for bios

```bash
sudo ./nvflash --save backup.rom -i 0
```

<img src="./images/ssh-client-update-bios-13.png" width="80%"/>

and again we got the error because we didn't unload kernel driver yet.

- Copy-paste the command to unload kernel driver that was given to us with previous error and run it with root

```bash
sudo rmmod nvidia_uvm nvidia_drm nvidia_modeset nvidia i2c_nvidia_gpu
```

<img src="./images/ssh-client-update-bios-14.png" width="80%"/>

and it should succeed.

- Now backup GPU bios once again

```bash
sudo ./nvflash --save backup.rom -i 0
```

<img src="./images/ssh-client-update-bios-15.png" width="80%"/>

### Update bios using fully compatible same vendor bios

As I have MSI Nvidia GPU, first I want just update the bios for new one from the same MSI vendor. I go to https://www.techpowerup.com/vgabios/ and search there for new bios from MSI

<img src="./images/ssh-client-update-bios-16.png" width="80%"/>

- Then I copy link to the bios

<img src="./images/ssh-client-update-bios-17.png" width="80%"/>

- And using `wget` tool download it to main PC as

```bash
wget https://www.techpowerup.com/vgabios/236791/236791.rom
```

<img src="./images/ssh-client-update-bios-18.png" width="80%"/>

it will download file `236791.rom` and place in current directory.

<img src="./images/ssh-client-update-bios-19.png" width="80%"/>

- Let's check what we've downloaded

```bash
sudo ./nvflash --version 236791.rom
```

<img src="./images/ssh-client-update-bios-20.png" width="80%"/>

- Now update bios

```bash
sudo ./nvflash 236791.rom -i 0
```

<img src="./images/ssh-client-update-bios-21.png" width="80%"/>

- It will prompt to us to confirm that we want to update bios. Enter `y` to confirm or other key to cancel.

<img src="./images/ssh-client-update-bios-22.png" width="80%"/>

- After you confirm the update will start

<img src="./images/ssh-client-update-bios-23.png" width="80%"/>

- After update completed the `nvflash` will suggest restart PC to apply changes.

<img src="./images/ssh-client-update-bios-24.png" width="80%"/>

- To restart main PC we first should turn Ubuntu graphical interface back using command

```bash
sudo systemctl start graphical.target
```

<img src="./images/ssh-client-update-bios-25.png" width="80%"/>

then just restart your main PC (Ubuntu where your GPU resides)

### Update bios using partly compatible bios (PCI subsystem ID mismatch)

- I'd like to update bios from another vendor. I've found some bios from `Gigabyte`. Then downloaded it as `240140.rom` file

<img src="./images/ssh-client-update-bios-26.png" width="80%"/>

- Then I prepared main PC for bios update

```bash
wget https://www.techpowerup.com/vgabios/240140/240140.rom # download Gigabyte bios
sudo nvidia-smi -pm 0 # disable nvidia driver persistent mode
sudo systemctl isolate multi-user.target # turn off graphical interface
sudo rmmod nvidia_uvm nvidia_drm nvidia_modeset nvidia i2c_nvidia_gpu # unload kernel driver
```

- Try update bios

```bash
sudo ./nvflash 240140.rom -i 0
```

and getting the error `PCI subsystem ID mismatch`

<img src="./images/ssh-client-update-bios-27.png" width="80%"/>

- Using `--help` command for `nvflash` tool we find this

```txt
Allow firmware and adapter PCI subsystem ID mismatch use `--overridesub` or `-6` option.
```

- Try one more time with new `-6` option

```bash
sudo ./nvflash 240140.rom -i 0 -6
```

<img src="./images/ssh-client-update-bios-28.png" width="80%"/>

and `nvflash` ask us to confirm to override subsystem ID mismatch error, press `y` key to continue

<img src="./images/ssh-client-update-bios-29.png" width="80%"/>

then one more time press `y` to confirm bios update

<img src="./images/ssh-client-update-bios-30.png" width="80%"/>

after bios started updating

<img src="./images/ssh-client-update-bios-31.png" width="80%"/>
<img src="./images/ssh-client-update-bios-32.png" width="80%"/>

- Then turn on graphical interface for main PC

```bash
sudo systemctl start graphical.target
```

- Reboot the main PC

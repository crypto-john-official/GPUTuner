
## Nvidia CUDA and Driver installation

- Open `Show applications` window

<img src="./images/Ubuntu-Applications-Button.png" width="80%"/>

- Find there `Software Updater` and launch it. It will suggest you to update some packages and delete others. You should follow that instructions (else CUDA may conflict with the packages that were packed in Ubuntu installer). After conflicted packages got deleted (via `Partial Upgrade`) the `Sowftware Updater` will ask you to reboot the system. After reboot, we are ready to install CUDA packages and drivers.

<img src="../images/Ubuntu-Software-updater-01.png" width="60%"/>
<img src="../images/Ubuntu-Software-Updater-Partial-update.png" width="60%"/>
<img src="../images/Ubuntu-Software-updater-ask-start-partial-upgrade.png" width="80%"/>
<img src="../images/Ubuntu-Software-updater-remove-packages.png" width="80%"/>
<img src="../images/Ubuntu-Software-updater-02.png" width="60%"/>
<img src="../images/Ubuntu-Software-updater-upgrade-completed.png" width="60%"/>

- After packages got upgraded, once again open `Show applications`, find there `Terminal` application and launch it

<img src="./images/Ubuntu-Find-Terminal-App.png" width="80%"/>

- Open Nvidia CUDA website page https://developer.nvidia.com/cuda-downloads and select target platform `Ubuntu 20.04` -> `deb (network)`

<img src="./images/Ubuntu-Cuda-Select.png" width="80%"/>

- To install Nvidia driver and Cuda you should execute all the commands from `Installation Instructions` from screen above (there are 6 commands)

<img src="./images/Cuda-installation-deb.png" width="80%"/>

- after last command `sudo apt-get -y install cuda` reboot your system, and you are ready to go.

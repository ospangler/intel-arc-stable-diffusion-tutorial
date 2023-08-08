# intel-arc-stable-diffusion-tutorial

## Tutorial in a Video Format: https://www.youtube.com/watch?v=GZLjbTPLCVk

Note: This tutorial is intended to help users install Stable Diffusion on PCs using an Intel Arc A770 or Intel Arc A750 graphics card. While all commands work as of 8/7/2023, updates may break these commands in the future. I encourage people following this tutorial to check the links included for each step and following the offical Intel instructions if these commands are not working for you.

## ---Step 1: Initial Setup---
**Source: Archive Tutorial at https://youtu.be/ub9150aOMMc**

Turn on windows virtual machine and windows subsystem for linux in Windows features.

See https://learn.microsoft.com/en-us/windows/wsl/install if you have no clue what you're doing.

open powershell

```
wsl --set-default-version 2
```
```
wsl --update
```
> Note: If you've already been playing around with wsl and want to start with a fresh instalation, run ```wsl --unregister ubuntu``` first before installing.
```
wsl --install ubuntu
```
```
sudo apt-get update -y && sudo apt-get upgrade -y
```
```
git
```
```
python3
```
```
exit()
```
```
sudo apt install python3.10-venv -y
```
```
sudo apt install python3-pip -y
```
```
sudo apt install libgl1 -y
```
```
git clone https://github.com/vladmandic/automatic
```

## ---Step 2: OpenAPI PreReq---
**Source: https://www.intel.com/content/www/us/en/docs/oneapi/installation-guide-linux/2023-2/overview.html**

### Installing Intel Arc Graphics Drivers
Run ```lsb_release -a``` to check if Ubuntu version is >23.04, if not you have to install drivers below:  

```
wget -qO - https://repositories.intel.com/graphics/intel-graphics.key | \
  sudo gpg --dearmor --output /usr/share/keyrings/intel-graphics.gpg
echo "deb [arch=amd64,i386 signed-by=/usr/share/keyrings/intel-graphics.gpg] https://repositories.intel.com/graphics/ubuntu jammy arc" | \
  sudo tee /etc/apt/sources.list.d/intel-gpu-jammy.list
sudo apt-get update
```
```
sudo apt-get install -y \
  intel-opencl-icd intel-level-zero-gpu level-zero \
  intel-media-va-driver-non-free libmfx1 libmfxgen1 libvpl2 \
  libegl-mesa0 libegl1-mesa libegl1-mesa-dev libgbm1 libgl1-mesa-dev libgl1-mesa-dri \
  libglapi-mesa libgles2-mesa-dev libglx-mesa0 libigdgmm12 libxatracker2 mesa-va-drivers \
  mesa-vdpau-drivers mesa-vulkan-drivers va-driver-all vainfo hwinfo clinfo
```
```
sudo reboot
```

**CLOSE AND REOPEN POWERSHELL**

```
wsl
```
```
cd
```
```
sudo dpkg --add-architecture i386 
sudo apt-get update
sudo apt-get install  -y \
  udev mesa-va-drivers:i386 mesa-common-dev:i386 mesa-vulkan-drivers:i386 \
  libd3dadapter9-mesa-dev:i386 libegl1-mesa:i386 libegl1-mesa-dev:i386 \
  libgbm-dev:i386 libgl1-mesa-glx:i386 libgl1-mesa-dev:i386 \
  libgles2-mesa:i386 libgles2-mesa-dev:i386 libosmesa6:i386 \
  libosmesa6-dev:i386 libwayland-egl1-mesa:i386 libxatracker2:i386 \
  libxatracker-dev:i386 mesa-vdpau-drivers:i386 libva-x11-2:i386
```
```
sudo apt-get install -y \
  libigc-dev intel-igc-cm libigdfcl-dev libigfxcmrt-dev level-zero-dev
```
```
sudo apt-get install intel-opencl-icd
```

### Add Yourself to the Ubuntu Render Group
You may have to check your group membership. Use the below command to check your groups:
```
stat -c "%G" /dev/dri/render*
groups ${USER}
```

If you're not in the "render" group, use the below command to add yourself to the render group:
```
sudo gpasswd -a ${USER} render
newgrp render
```
Then recheck if you're in the "render" group" by running the below command:
```
stat -c "%G" /dev/dri/render*
groups ${USER}
```
### Disabling GPU Hangcheck
**Now we're going to need to edit the grub file disable the GPU Compute Hangcheck, which will prevent the computer from killing long compute tasks**
```
sudo apt-get --reinstall install grub-pc
```
```
cd /etc/default
```
```
ls
```
```
sudo nano grub
```

**insert ```i915.enable_hangcheck=0``` into the quotes on ```GRUB_CMDLINE_LINUX_DEFAULT="{insert it here}"```**

write out with ctrl+O

enter to confirm file name

exit with ctrl+X

```
sudo update-grub
```
```
sudo reboot
```

**close powershell and DO A FULL RESTART OF YOUR PC. If you don't, the wsl enviroment will be unstable**

reopen powershell after restarting

```
wsl
```
```
cd
```

## ---Step 3: OneAPI Installation---
**Source: https://www.intel.com/content/www/us/en/developer/tools/oneapi/base-toolkit-download.html**

```
wget https://registrationcenter-download.intel.com/akdlm/IRC_NAS/992857b9-624c-45de-9701-f6445d845359/l_BaseKit_p_2023.2.0.49397_offline.sh
```
```
sudo sh ./l_BaseKit_p_2023.2.0.49397_offline.sh
```

ignore warnings for missing gui

skip eclipse ide config

wait for install to complete

click finish

## ---Step 4: Vladmandic Installation-----
```
cd
```
```
cd automatic
```
```
ls
```
```
./webui.sh --use-ipex
```

WAIT A LONG TIME. If no progress for awhile (more than 10 minutes), use ctrl+C to break and then run ./webui.sh --use-ipex again. Keep in mind that it can take awhile to update which packages have been installed and the process can take pretty slow so don't get too impatient.

download default model : yes

I had to manually do it, ctrl click link, download, then use file exporer to go to \\wsl.localhost\Ubuntu\home\{user}\automatic\models\Stable-diffusion

After doing this, you should be able to click on the link that appears in the console. You've done it, yay! :)

**I would highly reccomend setting up the wslconfig file, otherwise you're likely to run into memory leak issues and crashes when that inevitably happens**

You should watch the tutorial from Archive on how to set up wslconfig, exporting your image, and common error messages.
https://youtu.be/ub9150aOMMc?t=307


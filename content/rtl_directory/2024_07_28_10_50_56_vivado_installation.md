+++
title = "Vivado Installation"
author = ["Kiran"]
date = 2024-07-28T10:50:00-04:00
tags = ["rtl"]
draft = false
css = "../../zcustom.css"
+++

Digilent's [Zybo-Z7](https://digilent.com/shop/zybo-z7-zynq-7000-arm-fpga-soc-development-board/) is a ready-to-use embedded software and digital circuit development board built around the Xilinx Zynq™-7000 family. Xilinx provides free licensing to certain development boards and Zybo-Z7 is one among them. This article provides detailed steps to install Vivado 2022.2 on Ubuntu 22.04 LTS. The same procedure can be followed to install the desired version of the software.


## Initial Requirements {#initial-requirements}

1.  A desktop/laptop with at least 16GB of RAM (DDR4 preferred) and 100GB of free storage space for full installation.
2.  Create an [AMD Xilinx Account](https://www.amd.com/en/registration/create-account.html) to download the software.

More information regarding the system requirements is detailed in the Vivado Design Suite User Guide [UG973](https://www.xilinx.com/support/documents/sw_manuals/xilinx2022_2/ug973-vivado-release-notes-install-license.pdf).


## Download Vivado {#download-vivado}

Navigate to the Xilinx [Download page](https://www.xilinx.com/support/download.html?_ga=2.260602445.115923376.1607278706-574785482.1605024210) and click on the _Xilinx Unified Installer 2022.2: Linux Self Extracting Web installer_, this should be redirected to the login page. Log in with the Xilinx Account and download the file.

{{< figure src="/ox-hugo/vivado.png" class="center !important" width="600px" >}}


## Running The Installer {#running-the-installer}

To run the unified installer, it must first be converted to an executable file.

```linux
chmod -x Xilinx_Unified_2022.2_1014_8888_Lin64.bin
```

After making the file executable run the the installer with root permissions.

```linux
sudo ./Xilinx_Unified_2022.2_1014_8888_Lin64.bin
```

This will open the installer GUI in a new window.


## Vivado Installer GUI {#vivado-installer-gui}

The welcome screen of the installer GUI provides a reminder of the supported OS version.

{{< figure src="/ox-hugo/installer_gui1.png" class="center !important" width="500px" >}}

Click Next, to proceed. The next window requires the user to provide the account credentials. The same account credentials were used at the beginning to log in to Xilinx and to download the Vivado software.

{{< figure src="/ox-hugo/installer_gui2.png" class="center !important" width="500px" >}}

In the select product window, select **Vivado** and proceed.

{{< figure src="/ox-hugo/installer_gui3.png" class="center !important" width="500px" >}}

Select Vivado ML Standard edition as it does not require a license to generate the necessary file to program the Zybo-Z7 development.

{{< figure src="/ox-hugo/installer_gui4.png" class="center !important" width="500px" >}}

The two versions of Zybo-Z7, Z7-10, and Z7-20 include Zynq-7000 SoC XC7Z010 and XC7Z020 devices respectively. From [UG973](https://www.xilinx.com/support/documents/sw_manuals/xilinx2022_2/ug973-vivado-release-notes-install-license.pdf) table 1, Vivado ML Standard Edition includes the license for the required FPGA.

{{< figure src="/ox-hugo/license.png" class="center !important" width="600px" >}}

Earlier it was mentioned at least 100GB of free storage is required to install Vivado, installing only the necessary tools and components will reduce the storage space. Installing only the Zynq-7000 SoC will require less storage space as it can be seen in the figure below.

{{< figure src="/ox-hugo/installer_gui5.png" class="center !important" width="500px" >}}

Agree to all the terms and conditions and process further.

{{< figure src="/ox-hugo/installer_gui6.png" class="center !important" width="500px" >}}

The installing directory should be **/tools/Xilinx**, there are errors in the image shown below because Vivado is already installed in the specified location. If it is a fresh installation then these errors will not be displayed.

{{< figure src="/ox-hugo/installer_gui7.png" class="center !important" width="500px" >}}

The final step is to click on the install button on the summary window. Here the installation location should specify the selected installation location by the user.

{{< figure src="/ox-hugo/installer_gui8.png" class="center !important" width="500px" >}}


## Install Cable Drivers {#install-cable-drivers}

Once the installation is completed, check if the cable drivers are installed correctly using the following command:

```linux
ls -la /etc/udev/rules.d
```

This should output the following two files:

> -   52-digilent-usb.rules
> -   52-xilinx-pcusb.rules

{{< figure src="/ox-hugo/drivers.png" class="center !important" width="350px" >}}

If the above files are not present, run the installer from the following directory:

> &lt;YOUR_XILINX_INSTALL&gt;/data/xicom/cable_drivers/&lt;lin64 or lin32&gt;/install_scripts/install_drivers

Example:

```linux
/tools/Xilinx/Vivado/2022.2/data/xicom/cable_drivers/lin64/install_script/install_drivers
```

[Xilinx Support](https://support.xilinx.com/s/article/66440?language=en_US)


## Launch Vivado {#launch-vivado}

Vivado can be launched from the terminal by executing the **Vivado** command. To enable this, the following should be added to the **~/.profile** file.

```linux
source /tools/Xilinx/Vivado/2022.2/setting64.sh
```

Note: Add the appropriate Vivado version folder in the source path.

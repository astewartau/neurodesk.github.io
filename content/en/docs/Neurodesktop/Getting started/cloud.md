---
title: "Cloud"
linkTitle: "Cloud"
weight: 5
description: >
  Run neurodesktop using Oracle or Azure cloud computing
---

### Minimum System Requirements
1. At least 3GB free space for neurodesktop base image
2. Docker requirements. Details found under https://docs.docker.com/get-docker/

## Quickstart
### 1. Connect to cloud server
On the computer from which you want to access Neurodesktop, open an SSH connection to your cloud instance with port forwarding (USER should be substituted with a username that has admin privileges on the cloud instance, and IP should be substituted with the IP address of the cloud instance)
```bash
ssh -L 8080:127.0.0.1:8080 USER@IP
```

### 2. Install Docker
Install Docker on the cloud instance from here: https://docs.docker.com/get-docker/. Additional information available here: https://www.neurodesk.org/docs/neurodesktop/getting-started/linux/#installing-docker 

### 3. Run Neurodesktop
Create a local folder ~/neurodesktop-storage on the cloud instance to store persistent data (data that will not disappear if neurodesktop is stopped)

#### Option 1: NeuroDesktop.run
Download and run the following executable on the cloud instance
https://github.com/NeuroDesk/neurodesktop/raw/main/Linux_run_Neurodesk/NeuroDesktop.run

#### Option 2: Using Terminal
1. Open a terminal on the cloud instance, and type the following command to automatically download the neurodesktop container and run it 

```bash
sudo docker run \
  --shm-size=1gb -it --privileged --name neurodesktop \
  -v ~/neurodesktop-storage:/neurodesktop-storage \
  -e HOST_UID="$(id -u)" -e HOST_GID="$(id -g)"\
  -p 8080:8080 \
  -h neurodesktop-{{< params/neurodesktop/version >}} vnmd/neurodesktop:{{< params/neurodesktop/version >}}
```
<!-- neurodesktop version found in neurodesk.github.io/data/neurodesktop.toml -->

{{< alert color="warning">}}
If you get errors in neurodesktop then check if the ~/neurodesktop-storage directory is writable to all users. Otherwise run:
```bash
chmod a+rwx ~/neurodesktop-storage
```
{{< /alert >}}

2. Once neurodesktop is downloaded to the cloud instance (`guacd[77]: INFO:        Listening on host 127.0.0.1, port 4822` is displayed in terminal), leave the terminal open and neurodesktop running (i.e., do not press CTRL+C)

{{< alert color="info" >}}
Even if your connection to the cloud instance is broken, and the terminal does not respond, Neurodesktop will still continue running on the cloud instance. When the connection to the cloud instance is re-established, please start over the instructions from step 3 below.
{{< /alert >}}

3. If it is required to set up an SSH tunnel to access the cloud instance, please set up such a tunnel from the computer from which you want to access Neurodesktop (e.g. `ssh -L 8080:127.0.0.1:8080 USER@IP`)

4. Open a browser on the computer from which you want to access Neurodesktop, and go to:
```none
http://localhost:8080/#/?username=user&password=password
```
If the computer runs Linux, check specific instructions at https://www.neurodesk.org/docs/neurodesktop/getting-started/linux/, Option 2, Step 3.

5. Press on "Desktop Auto-Resolution" under "ALL CONNECTIONS"

6. If it is the first time you use Neurodesktop, wait until the desktop appears (it may take a few seconds). Otherwise, it should appear instantaneously.

7. Neurodesk is ready to use! Click "What's next?" on the left of this page for further instructions.     

8. For an optimal experience, switch your browser to full-screen mode by following the instructions for your browser here (except Mac where full-screen mode is built-in):
https://www.thewindowsclub.com/open-chrome-edge-or-firefox-browser-in-full-screen-mode

{{< alert color="info">}}
The browser can be closed anytime, and Neurodesktop will continue running on the cloud instance. To reconnect to Neurodesktop, simply start over from step 4 above.
{{< /alert >}}

{{< alert color="info">}}
If your computer hibernates/reboots/etc. or if the network connection has been temporarily lost, Neurodesktop still continues running on the cloud instance. To reconnect to Neurodesktop, start over from step 3 above.
{{< /alert >}}

{{< alert color="info">}}
If you want to connect to the same instance of Neurodesktop from another computer, close the browser in the current computer, and start over from step 3 on the other computer (note that only one computer can access Neurodesktop at a time using the default RDP protocol; for access from multiple computers simultaneously, please re-run Neurodesktop with VNC enabled as explained further below).
{{< /alert >}}

## Deleting neurodesktop:
When done processing your data it is important to stop and remove the container - otherwise the next start or container update will give an error ("... The container name "/neurodesktop" is already in use...")

{{< alert title="Note" color="warning" >}}
Notice that any data that were saved outside of /neurodesktop-storage would be lost. Please make sure to move all your data to that folder before deleting neurodesktop.
{{< /alert >}}

1. Click on the terminal from which you ran neurodesktop

2. Press Ctrl-C

3. Run:
```bash
sudo docker stop neurodesktop && sudo docker rm neurodesktop
```

## Portforwarding to an iOS ipad 
You can also connect to this cloud instance from your iOS device :) For this install https://webssh.net/documentation/help/networking/port-forwarding/ and create a tunnel (the tool is free for one connection). Start the docker container in a screen session and then connect to it from your ios device in the browser.

## Cloud-provider specific Tutorials 
| Cloud provider | link                                                                                    |
|----------------|-----------------------------------------------------------------------------------------|
| Oracle         | https://mri.sbollmann.net/index.php/2020/12/08/run-neurodesk-on-oracle-cloud-free-tier/ |
| Azure          | https://henryjburg.medium.com/neurodesk-running-on-azure-3e38c590a152                   |

## Using an RDP Client
Open an SSH connection to your cloud instance with the following command
```bash
ssh -L 3390:127.0.0.1:3390 USER@IP
```

Startup Neurodesktop using the following command:

```bash
sudo docker run \
  --shm-size=1gb -it --privileged --name neurodesktop \
  -v ~/neurodesktop-storage:/neurodesktop-storage \
  -e HOST_UID="$(id -u)" -e HOST_GID="$(id -g)"\
  -p 8080:8080 -p 3390:3389 \
  -h neurodesktop-{{< params/neurodesktop/version >}} vnmd/neurodesktop:{{< params/neurodesktop/version >}}
```

{{< alert color="info" >}}
If you want to connect via RDP using a different port, replace 3390 in the previous two steps and next step with your port
{{< /alert >}}

Open your RDP client and connect to Computer `localhost:3390`

Use the following details to login if prompted

username
: user

password
: password

## Using VNC

To enable VNC and disable RDP, startup Neurodesktop using the following command:

```bash
sudo docker run \
  --shm-size=1gb -it --privileged --name neurodesktop \
  -v ~/neurodesktop-storage:/neurodesktop-storage \
  -e HOST_UID="$(id -u)" -e HOST_GID="$(id -g)"\
  -p 8080:8080 \
  -h neurodesktop-{{< params/neurodesktop/version >}} vnmd/neurodesktop:{{< params/neurodesktop/version >}} --vnc
```

To enable both VNC and RDP, startup Neurodesktop using the following command:

```bash
sudo docker run \
  --shm-size=1gb -it --privileged --name neurodesktop \
  -v ~/neurodesktop-storage:/neurodesktop-storage \
  -e HOST_UID="$(id -u)" -e HOST_GID="$(id -g)"\
  -p 8080:8080 \
  -h neurodesktop-{{< params/neurodesktop/version >}} vnmd/neurodesktop:{{< params/neurodesktop/version >}} --vnc --rdp
```

{{< alert color="info" >}}
VNC allows for multiple desktop connections to same instance
{{< /alert >}}
{{< alert color="warning" >}}
VNC option for Neurodesktop on the browser does not support auto-resolution 
{{< /alert >}}

### Using a VNC Client

Startup Neurodesktop using the following command:

```bash
sudo docker run \
  --shm-size=1gb -it --privileged --name neurodesktop \
  -v ~/neurodesktop-storage:/neurodesktop-storage \
  -e HOST_UID="$(id -u)" -e HOST_GID="$(id -g)"\
  -p 8080:8080 --network host  \
  -h neurodesktop-{{< params/neurodesktop/version >}} vnmd/neurodesktop:{{< params/neurodesktop/version >}} --vnc
```

Download the Tiger VNC client from https://sourceforge.net/projects/tigervnc/files/stable/1.12.0/

Open an SSH connection to your cloud instance with the following command
```bash
ssh -L 5901:127.0.0.1:5901 USER@IP
```

Run the VNC Client and connect to `localhost::5901`

![tigervncclient](/neurodesktop/getting-started/vnc/tigervncclient.png 'tigervncclient')

Enter `password` and click Ok.

![tigervncclient-password](/neurodesktop/getting-started/vnc/tigervncclient-password.png 'tigervncclient-password')

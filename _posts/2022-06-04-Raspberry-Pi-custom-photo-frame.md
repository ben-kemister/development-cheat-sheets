---
title: "Creating a customizable Photo frame using a Raspberry Pi"
tags:
    - raspberry_pi
    - linux
    - git
    - project
status: in progress
---

This post contains some details on my project to create a customizable digital 'photo frame' using a Raspbery Pi (running Retro-pi) and my 46" TV.
<!--more-->
## Project Goals

To re-use my always on [Raspberry Pi 3A+](https://www.raspberrypi.com/products/raspberry-pi-3-model-a-plus/), which is running Retro-pi, to display photos from my overseas trips on my 46" TV.

For bonus points I would also like to:

* Show other useful details such as:
  * details about the photo (location, filename, date taken, etc)
  * Current time
  * Current Weather
* Integrate this with my OpenHAB system to:
  * turn on the TV and switch to the Raspberry Pi input
  * be able to skip or rewind

## Software choices

A quick bit of research shows that there are lots of different software out there which can do the job of displaying a image slide show including:

* [feh](https://feh.finalrewind.org/) - Simple lightweight, console focused
* [Pi3D](https://pi3d.github.io/) - heaps of image functionality
* [MagicMirror](https://magicmirror.builders/) - open source and highly customizable

I decided to try [MagicMirror](https://magicmirror.builders/) due to its highly customizable nature and many pre-built [modules](https://docs.magicmirror.builders/modules/introduction.html).

## Magic Mirror

### Requirements

Electron, the app wrapper around MagicMirror², only supports the Raspberry Pi 2, 3 & 4. The Raspberry Pi 0/1 is currently not supported.

Additional you need to have a desktop environment to run Electron (the display component of MagicMirror?), so a *lite* version of the Raspberry Pi OS will not work.

My RetroPie setup did not have a desktop installed, but it was easily installed using the RetroPie Setup Script (`~/RetroPie-Setup/retropie_setup.sh`).  
In Configuration / Tools >> Raspbiantools >> Install Pixel Desktop Environment.  
See [my RetroPie for more details on this]({{site.baseurl}}{% link _operating_system/retropie.md %})

### Raspberry Pi 3A+ to slow for MagicMirror?

Initially I installed MagicMirror directly onto my Raspberry Pi 3A+, but it did not perform very well.

After thinking about this I ended up deploying a MagicMirror to my Kubernetes (k3s) cluster in server mode. I was able to leverage most of the work done in [this Helm Chart implementation](https://gitlab.com/khassel/magicmirror-helm) and customized it for my own cluster.

This approach had the added bonus of being able to serve the MagicMirror web interface out to multiple displays.

<!-- 

### Installation

The installation involves cloning the MagicMirror Github repo `git clone https://github.com/MichMich/MagicMirror`, so before doing this make sure you have setup your [Github authentication]({{site.baseurl}}{% link _tools/github.md %}).

After that you can just followed the installation instructions [here](https://docs.magicmirror.builders/getting-started/installation.html#manual-installation).

### Disabling Screen blanking

By default the Raspberry Pi will `blank` the screen after about 10-15 minutes. To prevent this from happening you can run

### (ssh) Usage

To start MagicMirror from a ssh session you need to use the command: `DISPLAY=:0 nohup npm start &`.

While actively configuring MagicMirror you can use `DISPLAY=:0 npm start`, which will terminate the npm process when you stop/exit the terminal.

#### Client Only

This is when you already have a server running remotely and want your RPi to connect as a standalone client to this instance, to show the MM from the server. Then from your RPi, you run it with: `node clientonly --address 192.168.1.5 --port 8080`. (Specify the ip address and port number of the server)

Not sure if this is right...
`DISPLAY=:0 nohup node clientonly --address magicmirror.cirrus-penthouse.duckdns.org --port 80`

### Configuration

## Future Ideas 
-->

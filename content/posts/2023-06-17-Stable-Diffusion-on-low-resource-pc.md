---
title: "Running Stable Diffusion on low resource PC (Dell Inspiron 7537)"
draft: false
tags:
 - dell
 - Inspiron
 - linux
 - mint
 - stable_diffusion
 - image_generation
 - ai
---

This page documents my efforts to get [Stable Diffusion](https://github.com/AUTOMATIC1111/stable-diffusion-webui) 
running on my old Dell Inspiron 7537 laptop.
<!--more-->

## TL;DR

The [CUDA capabilities](https://developer.nvidia.com/cuda-gpus) of the GeForce GT750M graphics card is only **3.0**.
Recent versions of PyTorch, which is required by Stable Diffusion, requires [at least 3.5 to be able to run](https://github.com/AUTOMATIC1111/stable-diffusion-webui/issues/3003).

There may be a possible to build an old version of [PyTorch which can use a 3.0 capability](https://discuss.pytorch.org/t/pytorch-version-for-cuda-compute-capability-3-0-gtx-780m/15889),
but this looks to be very involved.

An alternate approach is to use only the laptops CPU to be able to generate the images.


## Dell Inspiron 7537 Specs

The core specifications of the Dell Inspiron 7537 (circa ~2013) which I am using are:

* CPU: 4th Gen Intel Core i7-4510U processor (4M Cache up to 3.1 GHz)
* RAM: 16 GB Dual Channel DDR3L 1600MHz (8GB x 2)
* HDD: Current:Samsung SSD 850 EVO 500GB
* Graphics:
  * Intel HD Graphics 4400
  * NVIDIA GeForge GT750M 2GB GDDR5MOD,INFO,GNRC
* OS: Linux Mint 21.1 (Ubuntu jammy, debian)
  
## Stable Diffusion web UI

The [Stable Diffusion web UI](https://github.com/AUTOMATIC1111/stable-diffusion-webui) is a browser interface based on 
Gradio library for Stable Diffusion.

It has heaps of features and the browser interface makes it easy to run on a remote PC.

### Installation

The Dell laptop currently has Linux Mint (Debian-based) installed on it (due to some other projects I am working on) so we need to use the 
[linux installation instructions](https://github.com/AUTOMATIC1111/stable-diffusion-webui#automatic-installation-on-linux).

Namely:
1. Install the dependencies:
    ```shell
    sudo apt update && sudo apt upgrade -y
    
    # Debian-based:
    sudo apt install wget git python3 python3-venv
    ```
2. Create (and navigate to) the directory to install the webui into:
    ```shell
    # Create directory
    mkdir sd-webui && cd sd-webui
    ```
3. Install using:
    ```shell
    # Install
    bash <(wget -qO- https://raw.githubusercontent.com/AUTOMATIC1111/stable-diffusion-webui/master/webui.sh)
  
    ################################################################
    Install script for stable-diffusion + Web UI
    Tested on Debian 11 (Bullseye)
    ################################################################
    ...
    ```
4. Wait. The script will download a lot of python dependencies which will take some time ~10 - 15 minutes depending on your internet connection.

## Running Stable Diffusion

The webui is run with the script `./webui.sh`
```shell
cd stable-diffusion-webui 
./webui.sh
```

If you need to add/use any command line flags you can use the [`./webui-user.sh`](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Command-Line-Arguments-and-Settings#webui-user)
script which has ready-made placeholders and comments for these.

> Note: The `webui.sh` script needs to be run from the directory where is resides.
> Running the script from another directory will cause issues when loading any COMMANDLINE_ARGS

## First run - fixing errors and warnings!

### Installing TCMalloc

When starting the `webui.sh` if you see the message:
```shell
Cannot locate TCMalloc (improves CPU memory usage)
```
You can install `google-perftools` to make this message go away `sudo apt install --no-install-recommends google-perftools`.
See [this github issue](https://github.com/AUTOMATIC1111/stable-diffusion-webui/issues/10117) for more details.

### RuntimeError: Torch is not able to use GPU;...

On first the first run I got the following error:
```shell
RuntimeError: Torch is not able to use GPU; add --skip-torch-cuda-test to COMMANDLINE_ARGS variable to disable this check
```

After heaps of investigation I found out that the [CUDA capabilities](https://developer.nvidia.com/cuda-gpus) of the GeForce GT750M graphics card is only **3.0**.
PyTorch, which is required by Stable Diffusion, requires a CUDA capability of [at least 3.5 to be able to run](https://github.com/AUTOMATIC1111/stable-diffusion-webui/issues/3003).

> **CUDA** (Compute Unified Device Architecture) is a parallel computing platform and application programming interface (API)
> that allows software to use certain types of graphics processing units (GPUs) for general purpose processing,
> an approach called general-purpose computing on GPUs (GPGPU).
> CUDA is a software layer that gives direct access to the GPU's virtual instruction set and parallel computational elements,
> for the execution of compute kernels.

So we can configure Stable Diffusion to use only the CPU by adding some arguments to the `./webui-user.sh` file:
```shell
export COMMANDLINE_ARGS="--precision full --no-half --use-cpu all --skip-torch-cuda-test --listen"
export CUDA_VISIBLE_DEVICES=""
```

This will run everything on the CPU and use the system memory, which is **much slower** than running on the GPU and VRAM.
See below for information on testing and performance.

## Testing & Performance

The stable diffusion wiki has some [information on optimization](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Optimizations), 
unfortunately I believe much of these settings do not apply to a _CPU only_ configuration.

Testing configuration:
* Model: openjourney-v4.ckpt
* Prompt: glass of water on a table
* Sampling Steps: 20

> For brevity the following **required** COMMANDLINE_ARGS have not been included in the table below
> `--precision full --no-half --use-cpu all --skip-torch-cuda-test`

| COMMANDLINE_ARGS                                        | First run                   | Second Run                  | Third run                   | 
|---------------------------------------------------------|-----------------------------|-----------------------------|-----------------------------|
| Default args                                            | 19.59s/it (06:31 min/image) | 19.20s/it (06:26 min/image) | 19.19s/it (06:26 min/image) |
| Default args + `--opt-sdp-attention`                    | 18.70s/it (06:17 min/image) | 18.68s/it (06:16 min/image) | 18.69s/it (06:16 min/image) | 
| Default args + `--opt-sdp-attention --opt-channelslast` | 19.13s/it (06:31 min/image) | 19.16s/it (06:30 min/image) | 19.52s/it (06:30 min/image) | 

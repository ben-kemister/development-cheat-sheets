---
title: "Stable Diffusion"
tags:
 - python
 - stable_diffusion
 - image_generation
 - ai
---

[Stable Diffusion](https://stability.ai/blog/stable-diffusion-public-release) is a deep learning, text-to-image model released in 2022. 
It is primarily used to generate detailed images conditioned on text descriptions, though it can also be applied to other tasks such as inpainting, 
outpainting, and generating image-to-image translations guided by a text prompt.
<!--more-->
It was developed by the start-up Stability AI in collaboration with a number of academic researchers and non-profit organizations.

## User Interface

There are a variety of user interfaces that you can use for interacting with Stable Diffusion. 
Personally I think that [AUTOMATIC1111's WebUI](https://github.com/AUTOMATIC1111/stable-diffusion-webui) is among one of the best.

## Models

The table below provides an overview of the models that I have used:

| Model                                                                                       | Description                                                                                                                                             |
|---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Realistic Vision](https://civitai.com/models/4201/realistic-vision-v20)                    | Good for photorealistic images but requires lots of prompt engineering and higher step count to get good images                                         |
| [Anything V3](https://civitai.com/models/66/anything-v3)                                    | Good for creation of "anime" style images                                                                                                               |
| [Deliberate](https://civitai.com/models/4823/deliberate)                                    | Creates images which blend digital art and realism. Requires good prompt engineering.                                                                   |
| [Protogen x3.4](https://civitai.com/models/3666/protogen-x34-photorealism-official-release) |                                                                                                                                                         |
| [OpenJourney v4](https://huggingface.co/prompthero/openjourney-v4)                          | OpenJourney is a model inspired by Midjourney, a Discord-based AI that was used to generate artwork.                                                    |
| [f222](https://civitai.com/models/1188/f222)                                                | F222 is trained originally for generating nudes, but people found it helpful in generating beautiful female portraits with correct body part relations. |
| [Modelshoot](https://huggingface.co/wavymulder/modelshoot)                                  | incredibly realistic images created from simple prompts. Look like they've been shot by a real photographer.                                            |


Links:
* [Top 10 Best Stable Diffusion Models You Need to Try](https://softwarekeep.com/help-center/best-stable-diffusion-models-to-try)
* [Beginner’s guide to Stable Diffusion models and the ones you should know](https://stable-diffusion-art.com/models/)

## Capabilities

### Outpainting

https://stable-diffusion-art.com/outpainting/
# Diffusers-in-ComfyUI
This is a custom node allowing the use of the diffusers pipeline into ComfyUI. 

This work is greatly inspired by this [repository](https://github.com/Limitex/ComfyUI-Diffusers). I decided to recode it from scratch mainly for learning purposes, as well as because at first I only needed a portion of the code. I thank them for this opportunity to learn!

# Installation
1. Create a conda environment with Python 3.9
2. Activate your environment
3. Then clone [ComfyUI](https://github.com/comfyanonymous/ComfyUI) repository

4. Follow ComfyUI installation instructions:

    ```pip install torch torchvision torchaudio --extra-index-url https://download.pytorch.org/whl/cu121```

    ```pip install -r https://raw.githubusercontent.com/comfyanonymous/ComfyUI/master/requirements.txt```

5. Install Diffusers API 

    ```pip install git+https://github.com/huggingface/diffusers```

6. Install accelerate

    ```pip install accelerate```

7. Configure your acceleration

    ```accelerate config```

8. Install remaining missing packages

    ```pip install xformers opencv-python peft omegaconf```

9. Navigate to ComfyUI/custom-nodes, and clone this repo inside

10. Launch ComfyUI



# Nodes

## Pipeline nodes
Under "Diffusers-in-Comfy/Pipelines", you will find one node per Diffuser pipeline. **Text2ImgStableDiffusionPipeline**, **Img2ImgStableDiffusionPipeline**, and **InpaintingStableDiffusionPipeline**. Inside each one, you can precise :
- Whether you want to use SDXL
- Whether you want to use a VAE
- Whether you want to use a ControlNet
- Whether you want to activate low VRAM options to avoid low GPU memory issues.

## Inference nodes
Similarly as for pipelines, under "Diffusers-in-Comfy/Inference", you will find one node per type of inference pipeline you want to use : **GenerateTxt2Image**, **GenerateInpaintImage**, or **GenerateImg2Image**. The three of them have common options you can tweak:
- Which controlnet image to use
- Which controlnet scale
- Seed
- Positive prompt
- Negative prompt
- Inference steps
- Width
- Height
- Guidance scale

Then you have specific inputs per node.

**GenerateInpaintImage** : In this  node, you must write the paths to an input image and an input mask for inpainting. Be careful to delete all quotes. 

**GenerateImg2Image** : In this node, you must write the path to an input image to use as a prompt. Be careful to delete all quotes in the path.

## Utils nodes
Under "Diffusers-in-Comfy/Utils", you will find nodes that will allow you to make different operations, such as processing images. For now, only one is available : **Make Canny**. It takes in an image, transforms it into a canny, and then you can connect the output canny to the "controlnet_image" input of one of the Inference nodes. 

## Component nodes
Under "Diffusers-in-Comfy/Components", you will find Diffusers components and adapters that you can load into the UNET. For now, two are available.

**LoraLoader** : This node allows you to load a LoRA into the UNET and define its weight. Don't forget to write the triggerword into the prompt of the Inference node.

**Blora** : This node is the reason why I originally created this project. It implements a technique that allows for separating an image into its style and content. It acts as a LoRA but works a bit differently. Here's the (original repo)[https://github.com/yardenfren1996/B-LoRA]. Keep in mind that although a B-LoRA works well with a ControlNet, it cannot be used with a classic LoRA. Indeed, the classic LoRA will completely dilute the B-LoRA, supplanting it into the Unet.


# To do
There are quite a few things I'd like to develop further, so here's a very basic roadmap
- I don't really like that you have to choose whether your loaded model is SDXL or not. Ideally, I'd like the algo to piggyback on ComfyAPI to determine the architecture automatically.
- I'd like to give the possibility to either load a model locally, or remotely, by writing the path and then maybe add an option to specify whether it's a URL or a local path. Yet this potentially complicates the detection of the architecture, I need to dig deeper into Comfy API to pull this out.
- A small but important QOL improvement : in all paths written in the inputs, detect and delete quotes
- Test more research papers and implement them as new nodes 😊
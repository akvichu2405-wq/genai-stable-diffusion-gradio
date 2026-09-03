### Prototype Development for Image Generation Using the Stable Diffusion Model and Gradio Framework
### DATE: 03/09/2026
### AIM:
To design and deploy a prototype application for image generation utilizing the Stable Diffusion model, integrated with the Gradio UI framework for interactive user engagement and evaluation.

### PROBLEM STATEMENT:

### DESIGN STEPS:

**STEP 1:**  Install and import the required Python libraries such as Gradio, Requests, Pillow, and python-dotenv.

**STEP 2:** Load the Hugging Face API key and Stable Diffusion endpoint securely using the .env file.

**STEP 3:** Create a function to send the user's text prompt to the Stable Diffusion model through the Hugging Face API.

**STEP 4:** Receive the generated image from the API and convert the response into a PIL image format.

**STEP 5:** Design a Gradio interface with a text box for entering prompts and an image component for displaying the generated image.

**STEP 6:** Launch the Gradio application and test it by entering different text prompts to generate images.
### PROGRAM:
```
# Install dependencies if required:
# !pip install -q gradio requests python-dotenv pillow

import os
import io
import json
import base64
import requests
import gradio as gr

from PIL import Image
from dotenv import load_dotenv, find_dotenv

load_dotenv(find_dotenv())

HF_API_KEY = os.getenv("HF_API_KEY")
ENDPOINT_URL = os.getenv("HF_API_TTI_BASE")
PORT = os.getenv("PORT1", "7860")

if not HF_API_KEY:
    raise ValueError("HF_API_KEY not found. Check your .env file.")

if not ENDPOINT_URL:
    raise ValueError("HF_API_TTI_BASE not found. Check your .env file.")


def get_completion(inputs, parameters=None):

    headers = {
        "Authorization": f"Bearer {HF_API_KEY}",
        "Content-Type": "application/json"
    }

    data = {
        "inputs": inputs
    }

    if parameters is not None:
        data["parameters"] = parameters

    response = requests.post(
        ENDPOINT_URL,
        headers=headers,
        data=json.dumps(data),
        timeout=120
    )

    if response.status_code != 200:
        raise Exception(
            f"API Error {response.status_code}: {response.text}"
        )

    return response.content



def base64_to_pil(img_base64):

    decoded_image = base64.b64decode(img_base64)

    byte_stream = io.BytesIO(decoded_image)

    return Image.open(byte_stream)


def generate(prompt):

    if not prompt or not prompt.strip():
        return None

    try:

        output = get_completion(prompt)

        # Hugging Face image response
        image = Image.open(io.BytesIO(output))

        return image

    except Exception as e:
        print("Error:", e)
        return None

gr.close_all()

demo = gr.Interface(
    fn=generate,

    inputs=gr.Textbox(
        label="Enter Your Prompt",
        placeholder="Describe the image you want to generate...",
        lines=3
    ),

    outputs=gr.Image(
        label="Generated Image"
    ),

    title="Image Generation Using Stable Diffusion",

    description=(
        "Enter a text prompt to generate an image "
        "using the Stable Diffusion model."
    ),

    examples=[
        ["A futuristic city at sunset"],
        ["A cute robot exploring space"],
        ["A peaceful mountain landscape"],
        ["A cyberpunk city at night"]
    ],

    allow_flagging="never"
)
demo.launch(
    share=True,
    server_port=int(PORT)
)

```
### OUTPUT:
<img width="906" height="426" alt="image" src="https://github.com/user-attachments/assets/a2ecb355-4997-494a-b811-03d0e3130a8a" />

### RESULT:
Thus, The designing and deploying of a prototype application for image generation by utilizing the Stable Diffusion model, integrated with the Gradio UI framework is executed successfully.

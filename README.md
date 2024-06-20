#AiRES_OP

# ZeRo: AI-Powered Image Generation App

ZeRo is an AI-powered application for generating images using the Stable Diffusion model. This application is built using Python with the help of several libraries, including `tkinter` for the GUI, `Pillow` for image handling, and `torch` along with `diffusers` for model inference.

## Features

- **Custom Prompt Input**: Allows users to enter a text prompt to generate an image.
- **Progress Bar**: Visual indicator of the image generation progress.
- **Image Display**: Shows the generated image within the application.

## Prerequisites

- Python 3.6 or higher
- CUDA-capable GPU

## Installation

1. Clone the repository:

    ```sh
    git clone https://github.com/Sarth5120/AiRES_OP.git
    cd AiRES_OP
    ```

2. Create a virtual environment:

    ```sh
    python -m venv env
    ```

3. Activate the virtual environment:

    - On Windows:

        ```sh
        env\Scripts\activate
        ```

    - On macOS/Linux:

        ```sh
        source env/bin/activate
        ```

4. Install the required packages:

    ```sh
    pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu116
    pip install diffusers
    pip install customtkinter
    pip install pillow
    ```

## Usage

1. Open `Zero.ipynb`.
2. Install all required libraries and modules.
3. Run the code.
   Wait for appilication to open.
4. Enter a prompt in the text entry field and click the "Generate" button to start the image generation process.
   Wait for generation process to complete.
5. The generated image will be displayed within the application once the process is complete.
    
    (The code will download Stable Diffusion files on the first run)

## Project Structure

- `Zero.ipynb`: The main application file containing the GUI setup and the image generation logic.

## Code Explanation

### Main Components

1. **Main Application Window**: Created using `tkinter` and `customtkinter`.

    ```python
    app = tk.Tk()
    app.geometry("532x700")
    app.title("ZeRo")
    ctk.set_appearance_mode("dark")
    ```

2. **Prompt Entry**: Allows users to input text prompts.

    ```python
    prompt = ctk.CTkEntry(app, height=40, width=512, font=("Arial", 20), text_color="black", fg_color="white")
    prompt.place(x=10, y=10)
    ```

3. **Image Display Area**: Displays the generated image.

    ```python
    lmain = ctk.CTkLabel(app, height=512, width=512)
    lmain.place(x=10, y=110)
    ```

4. **Progress Bar**: Shows the progress of the image generation.

    ```python
    progress = ttk.Progressbar(app, orient=tk.HORIZONTAL, length=512, mode='determinate')
    progress.place(x=15, y=630)
    ```

5. **Stable Diffusion Model Setup**: Loads the pre-trained model for image generation.

    ```python
    modelid = "CompVis/stable-diffusion-v1-4"
    device = "cuda"
    pipe = StableDiffusionPipeline.from_pretrained(modelid, revision="fp16",
    torch_dtype=torch.float16, use_auth_token='hf_GusJkDCVmqGLNdrYQKecDdWYgngXbUtt')
    pipe.to(device)
    ```

6. **Generate Function**: Handles image generation and updating the UI.

    ```python
    def generate():
        prompt_text = prompt.get()
        print(f"Prompt: {prompt_text}") 

        generation_complete = threading.Event()

        def update_progress_bar():
            step_count = 100  
            progress_step = 100 / step_count

            for _ in range(step_count):
                if generation_complete.is_set():
                    break
                progress.step(progress_step)
                app.update_idletasks()
                time.sleep(0.1)

            progress['value'] = 100
            app.update_idletasks()

        def run_generation():
            with autocast(device):
                result = pipe(prompt_text, guidance_scale=8.5)
                image = result.images[0]

            print("Image generated successfully") 

            image = image.convert("RGB")
            image.save('generatedimage.png')
            img = ImageTk.PhotoImage(image)

            print("Image converted and saved successfully")  

            lmain.img = img
            lmain.configure(image=img)
            print("Image displayed successfully") 

            generation_complete.set()

    threading.Thread(target=update_progress_bar).start()
    threading.Thread(target=run_generation).start()
    ```

7. **Generate Button**: Triggers the image generation process.

    ```python
    trigger = ctk.CTkButton(app, height=40, width=120, font=("Arial", 20), text_color="white", fg_color="green", command=generate)
    trigger.configure(text="Generate")
    trigger.place(x=206, y=60)
    ```

8. **Run the Main Loop**: Starts the Tkinter event loop.

    ```python
    app.mainloop()
    ```
```

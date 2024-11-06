# Generate a blur_hash for an uploaded image and send it along with the image using FastAPI.
### Install the required dependencies:
```bash
pip install fastapi[all] pillow blurhash
```
### FastAPI example to generate and send blur_hash

```python
from fastapi import FastAPI, File, UploadFile
from fastapi.responses import JSONResponse
from PIL import Image
import io
from blurhash import encode
import numpy as np

app = FastAPI()

def generate_blurhash(image: Image.Image) -> str:
    # Convert the image to RGB (if it's in a different mode like RGBA or grayscale)
    image = image.convert("RGB")
    
    # Resize the image to a smaller size to improve performance (you can adjust these values)
    image = image.resize((32, 32))

    # Convert the image to a numpy array
    image_np = np.array(image)
    
    # Encode the image as a blurhash string
    blur_hash = encode(image_np)
    
    return blur_hash

@app.post("/upload/")
async def upload_image(file: UploadFile = File(...)):
    # Read the uploaded image as a byte stream
    image_data = await file.read()
    
    # Convert the byte stream to an image using Pillow
    image = Image.open(io.BytesIO(image_data))
    
    # Generate the blurhash for the uploaded image
    blur_hash = generate_blurhash(image)
    
    # You can send any other info along with the image (e.g., the original image URL)
    response_data = {
        "filename": file.filename,
        "blur_hash": blur_hash,
        "message": "Image uploaded and processed successfully."
    }
    
    # Return the response with the blur_hash
    return JSONResponse(content=response_data)
```

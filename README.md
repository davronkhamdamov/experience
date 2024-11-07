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
# Best way to do a split pane in HTML
```html
<html>
    <head>
        <style>
            
            .splitter {
                width: 100%;
                height: 100px;
                display: flex;
            }
            
            #separator {
                cursor: col-resize;
                background-color: #aaa;
                background-image: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='10' height='30'><path d='M2 0 v30 M5 0 v30 M8 0 v30' fill='none' stroke='black'/></svg>");
                background-repeat: no-repeat;
                background-position: center;
                width: 10px;
                height: 100%;
            
                /* Prevent the browser's built-in drag from interfering */
                -moz-user-select: none;
                -ms-user-select: none;
                user-select: none;
            }
            
            #first {
                background-color: #dde;
                width: 20%;
                height: 100%;
                min-width: 10px;
            }
            
            #second {
                background-color: #eee;
                width: 80%;
                height: 100%;
                min-width: 10px;
            }

        </style>
    </head>
<body>
    <div class="splitter">
        <div id="first"></div>
        <div id="separator" ></div>
        <div id="second" ></div>
    </div>
    <script>
    
        // A function is used for dragging and moving
        function dragElement(element, direction)
        {
            var   md; // remember mouse down info
            const first  = document.getElementById("first");
            const second = document.getElementById("second");
        
            element.onmousedown = onMouseDown;
        
            function onMouseDown(e)
            {
                //console.log("mouse down: " + e.clientX);
                md = {e,
                      offsetLeft:  element.offsetLeft,
                      offsetTop:   element.offsetTop,
                      firstWidth:  first.offsetWidth,
                      secondWidth: second.offsetWidth
                     };
        
                document.onmousemove = onMouseMove;
                document.onmouseup = () => {
                    //console.log("mouse up");
                    document.onmousemove = document.onmouseup = null;
                }
            }
        
            function onMouseMove(e)
            {
                //console.log("mouse move: " + e.clientX);
                var delta = {x: e.clientX - md.e.clientX,
                             y: e.clientY - md.e.clientY};
        
                if (direction === "H" ) // Horizontal
                {
                    // Prevent negative-sized elements
                    delta.x = Math.min(Math.max(delta.x, -md.firstWidth),
                               md.secondWidth);
        
                    element.style.left = md.offsetLeft + delta.x + "px";
                    first.style.width = (md.firstWidth + delta.x) + "px";
                    second.style.width = (md.secondWidth - delta.x) + "px";
                }
            }
        }
        
        
        dragElement( document.getElementById("separator"), "H" );
        
        </script>
    </body>
</html>
```

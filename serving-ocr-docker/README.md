### Approach 1: Pre-processing and Post-processing happen in Client
1. Start ocr server
```
PS C:\Users\T901> docker run -p 8501:8501 --name tfserving_ocr --mount type=bind,source=//D/docker/ocr,target=/models
/ocr -e MODEL_NAME=ocr -it --rm tensorflow/serving
```
2. Run the client
```
PS C:\Users\T901\Anaconda3> ./python D:/docker/ocr/ocr_client_simplest.py
Using TensorFlow backend.
(2560, 160, 3)
(1, 80, 144)
78 [143, 143, 0, 22, 143, 143, 143, 143, 0, 0, 143, 143, 143, 143, 0, 7, 143, 143, 143, 0, 32, 139, 53, 67, 67, 67, 60, 53, 0, 32, 47, 143, 143, 143, 143, 143, 143, 0, 0, 31, 102, 86, 0, 0, 25, 143, 143, 0, 143, 143, 143, 143, 0, 143, 143, 143, 143, 143, 143, 143, 0, 32, 76, 76, 143, 0, 0, 0, 34, 34, 0, 0, 40, 76, 0, 0, 143, 143]
[' B  - Lỳgung La Kấó E   Là N Tà ']
```
3. Inspect the model
```
PS C:\Users\T901\Anaconda3\Scripts> .\saved_model_cli show --dir D:/docker/ocr/0000000002 --all

MetaGraphDef with tag-set: 'serve' contains the following SignatureDefs:

signature_def['serving_default']:
The given SavedModel SignatureDef contains the following input(s):
inputs['input_image'] tensor_info:
    dtype: DT_FLOAT
    shape: (-1, 2560, 160, 3)
    name: the_inputs:0
The given SavedModel SignatureDef contains the following output(s):
outputs['dense2/truediv:0'] tensor_info:
    dtype: DT_FLOAT
    shape: (-1, 80, 144)
    name: dense2/truediv:0
Method name is: tensorflow/serving/predict
```
### Approach 2: Pre-processing and Post-processing happen in Server
- Building model graph with pre-processing input and post-processing output tensors

### Issues and Solutions
- Issue : ndarray is not C-contiguous > Fix: https://stackoverflow.com/questions/26778079/valueerror-ndarray-is-not-c-contiguous-in-cython
```
15  Image Loading start...  D:/docker/ocr/test/
Image Loading finish...
Traceback (most recent call last):
  File "d:/docker/ocr/ocr_client.py", line 145, in <module>
    main()
  File "d:/docker/ocr/ocr_client.py", line 118, in main
    request_bytes = base64.b64encode(X_test)
  File "C:\Users\T901\Anaconda3\lib\base64.py", line 58, in b64encode
    encoded = binascii.b2a_base64(s, newline=False)
ValueError: ndarray is not C-contiguous
```
- Issue 2: Serving signature not found in signature def > Sol: add signature def before exporting the model (see https://stackoverflow.com/questions/45705070/how-to-load-and-use-a-saved-model-on-tensorflow)
```
15  Image Loading start...  D:/docker/ocr/test/
Image Loading finish...
response =  { "error": "Serving signature name: \"serving_default\" not found in signature def" }
Traceback (most recent call last):
  File "d:/docker/ocr/ocr_client.py", line 146, in <module>
    main()
  File "d:/docker/ocr/ocr_client.py", line 127, in main
    response.raise_for_status()
  File "C:\Users\T901\Anaconda3\lib\site-packages\requests\models.py", line 928, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 400 Client Error: Bad Request for url: http://127.0.0.1:8501/v1/models/ocr:predict
```
- Issue 3: "Serving signature name: \"serving_default\" not found in signature def"
```
15  Image Loading start...  D:/docker/ocr/test/
Image Loading finish...
response =  { "error": "Serving signature name: \"serving_default\" not found in signature def" }
Traceback (most recent call last):
  File "d:/docker/ocr/ocr_client.py", line 146, in <module>
    main()
  File "d:/docker/ocr/ocr_client.py", line 127, in main
    response.raise_for_status()
  File "C:\Users\T901\Anaconda3\lib\site-packages\requests\models.py", line 928, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 400 Client Error: Bad Request for url: http://127.0.0.1:8501/v1/models/ocr:predict
```
+ Solution: Add signature_def_map in builder with 'serving_default' name
```
import time
import os

export_dir = os.path.join('models', time.strftime("%Y%m%d-%H%M%S"))
builder = tf.saved_model.builder.SavedModelBuilder(export_dir)
builder.add_meta_graph_and_variables(sess, [tf.saved_model.tag_constants.SERVING], \
                                     signature_def_map= { "serving_default": tf.saved_model.signature_def_utils.predict_signature_def( \
                                                                                                                           inputs= {"the_inputs": x}, \
                                                                                                                           outputs= {"dense2/truediv": y})}, \
                                     strip_default_attrs=True)
builder.save()
```
- Issue 4: Solution is to build model graph with pre-processing input and post-processing output tensors in server side. 
```
response =  { "error": "Failed to process element: 0 of \'instances\' list. Error: Invalid argument: JSON Value: {\n    \"b64\": ... "\n} Type: Object is not of expected type: float" }
```
Another simplier solution is to create json payload of image np array in Client and send it to Server.
```
import requests
import json

image = img_to_array(load_img('./data/train/train_10001.jpg', target_size=(128,128))) / 255.
payload = {
  "instances": [{'input_image': image.tolist()}]
}
r = requests.post('http://localhost:8501/v1/models/ocr:predict', json=payload)
json.loads(r.content)
```

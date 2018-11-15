1. Start ocr server
PS C:\Users\T901> docker run -p 8501:8501 --name tfserving_resnet --mount type=bind,source=//D/docker/ocr,target=/models
/ocr -e MODEL_NAME=ocr -it --rm tensorflow/serving

2. Run the client
PS C:\Users\T901\Anaconda3> .\python D:/docker/ocr/ocr_client_simple.py
119184
response =  {
    "predictions": [{
            "b64": "NGJ1RlZBPT0="
        }
    ]
}

3. Inspect the model
```
PS C:\Users\T901\Anaconda3\Scripts> .\saved_model_cli show --dir D:/docker/ocr/0000000001 --all

MetaGraphDef with tag-set: 'serve' contains the following SignatureDefs:

signature_def['serving_default']:
The given SavedModel SignatureDef contains the following input(s):
inputs['input_bytes'] tensor_info:
    dtype: DT_STRING
    shape: ()
    name: input_bytes_1:0
The given SavedModel SignatureDef contains the following output(s):
outputs['output_bytes'] tensor_info:
    dtype: DT_STRING
    shape: (1)
    name: output_bytes_1:0
Method name is: tensorflow/serving/predict
```
2. Issues and Solutions
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
- Issue 4: Solution is to build model graph with pre-processing input and post-processing output tensor
```
response =  { "error": "Failed to process element: 0 of \'instances\' list. Error: Invalid argument: JSON Value: {\n    \"b64\": ... "\n} Type: Object is not of expected type: float" }
```

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

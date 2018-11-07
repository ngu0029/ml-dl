A reference to [Serving ML Quickly with TensorFlow Serving and Docker](https://medium.com/tensorflow/serving-ml-quickly-with-tensorflow-serving-and-docker-7df7094aa008)

1. Download a [pre-trained ResNet](https://storage.googleapis.com/download.tensorflow.org/models/official/20181001_resnet/savedmodels/resnet_v2_fp32_savedmodel_NHWC_jpg.tar.gz) SavedModel
2. Use 7zip to untar the tar file
```
PS D:\docker\resnet> ls
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        11/7/2018   8:53 AM                resnet_v2_fp32_savedmodel_NHWC_jpg
-a----        10/5/2018   4:16 AM      102963200 resnet_v2_fp32_savedmodel_NHWC_jpg.tar
-a----        11/7/2018   8:50 AM       95029470 resnet_v2_fp32_savedmodel_NHWC_jpg.tar.gz

PS D:\docker\resnet> cd resnet_v2_fp32_savedmodel_NHWC_jpg
PS D:\docker\resnet\resnet_v2_fp32_savedmodel_NHWC_jpg> cd 1538687457
PS D:\docker\resnet\resnet_v2_fp32_savedmodel_NHWC_jpg\1538687457> ls
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        11/7/2018   8:53 AM                variables
-a----        10/5/2018   4:11 AM         561298 saved_model.pb
```
3. Pulling the latest released TensorFlow Serving serving environment image
```
PS D:\docker\resnet\resnet_v2_fp32_savedmodel_NHWC_jpg> docker pull tensorflow/serving
PS D:\docker\resnet\resnet_v2_fp32_savedmodel_NHWC_jpg> docker image ls
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
tensorflow/serving         latest              d42952c6f8a6        4 hours ago         230MB
my-receiving-app           latest              a890c68de433        2 months ago        931MB
my-server-app              latest              43bed8fcfc1f        2 months ago        927MB
my-sending-app             latest              bb57fc53a036        2 months ago        928MB
my-python-app              latest              008a9338fc5e        3 months ago        932MB
rabbitmq                   3                   5fbca98fc816        3 months ago        125MB
```
4. Pointing the Resnet SavedModel to the model
- Notice that keeping target as '/models/resnet', and adding option -d to run the container in background.
```
PS D:\docker\resnet\resnet_v2_fp32_savedmodel_NHWC_jpg> docker run -p 8501:8501 --name tfserving_resnet --mount type=bin
d,source=//D/docker/resnet/resnet_v2_fp32_savedmodel_NHWC_jpg,target=/models/resnet -e MODEL_NAME=resnet -it tensorflow/
serving
2018-11-07 03:45:13.736574: I tensorflow_serving/model_servers/server.cc:82] Building single TensorFlow model file config:  model_name: resnet model_base_path: /models/resnet
2018-11-07 03:45:13.736815: I tensorflow_serving/model_servers/server_core.cc:461] Adding/updating models.
2018-11-07 03:45:13.736846: I tensorflow_serving/model_servers/server_core.cc:558]  (Re-)adding model: resnet
2018-11-07 03:45:13.842386: I tensorflow_serving/core/basic_manager.cc:739] Successfully reserved resources to load servable {name: resnet version: 1538687457}
2018-11-07 03:45:13.842488: I tensorflow_serving/core/loader_harness.cc:66] Approving load for servable version {name: resnet version: 1538687457}
2018-11-07 03:45:13.842521: I tensorflow_serving/core/loader_harness.cc:74] Loading servable version {name: resnet version: 1538687457}
2018-11-07 03:45:13.842586: I external/org_tensorflow/tensorflow/contrib/session_bundle/bundle_shim.cc:363] Attempting to load native SavedModelBundle in bundle-shim from: /models/resnet/1538687457
2018-11-07 03:45:13.842612: I external/org_tensorflow/tensorflow/cc/saved_model/reader.cc:31] Reading SavedModel from: /models/resnet/1538687457
2018-11-07 03:45:13.876612: I external/org_tensorflow/tensorflow/cc/saved_model/reader.cc:54] Reading meta graph with tags { serve }
2018-11-07 03:45:13.945834: I external/org_tensorflow/tensorflow/cc/saved_model/loader.cc:162] Restoring SavedModel bundle.
2018-11-07 03:45:15.193646: I external/org_tensorflow/tensorflow/cc/saved_model/loader.cc:138] Running MainOp with key saved_model_main_op on SavedModel bundle.
2018-11-07 03:45:15.246606: I external/org_tensorflow/tensorflow/cc/saved_model/loader.cc:259] SavedModel load for tags { serve }; Status: success. Took 1403970 microseconds.
2018-11-07 03:45:15.257615: I tensorflow_serving/servables/tensorflow/saved_model_warmup.cc:83] No warmup data file found at /models/resnet/1538687457/assets.extra/tf_serving_warmup_requests
2018-11-07 03:45:15.266214: I tensorflow_serving/core/loader_harness.cc:86] Successfully loaded servable version {name: resnet version: 1538687457}
2018-11-07 03:45:15.269736: I tensorflow_serving/model_servers/server.cc:286] Running gRPC ModelServer at 0.0.0.0:8500 ...
[warn] getaddrinfo: address family for nodename not supported
2018-11-07 03:45:15.271319: I tensorflow_serving/model_servers/server.cc:302] Exporting HTTP/REST API at:localhost:8501 ...
[evhttp_server.cc : 237] RAW: Entering the event loop ...

In another Powershell window:
PS C:\Users\T901> docker ps -a
CONTAINER ID        IMAGE                COMMAND                  CREATED             STATUS              PORTS                              NAMES
69b320cd9295        tensorflow/serving   "/usr/bin/tf_serving…"   3 minutes ago       Up 3 minutes        8500/tcp, 0.0.0.0:8501->8501/tcp   tfserving_resnet
```
- Facing the issue "Error starting userland proxy: mkdir /port/tcp:0.0.0.0:8501:tcp:172.17.0.2:8501: input/output error": following the discussion [here](https://github.com/docker/for-win/issues/573) to restart Docker to fix the issue. 
5. Download the resnet_client.py file to make prediction 
- In cmd line window:
```
C:\Users\T901>curl https://raw.githubusercontent.com/tensorflow/serving/master/tensorflow_serving/example/resnet_client.py -o D:/docker/resnet/resnet_client.py
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  2539  100  2539    0     0   2539      0  0:00:01 --:--:--  0:00:01 13577
```
6. Test the local resnet server
- Browser to http://localhost:8501/v1/models/resnet
```
{
 "model_version_status": [
  {
   "version": "1538687457",
   "state": "AVAILABLE",
   "status": {
    "error_code": "OK",
    "error_message": ""
   }
  }
 ]
}
```
- Or try PS C:\Users\T901\Anaconda3> curl -v http://localhost:8501/v1/models/resnet
7. Run the client
```
PS C:\Users\T901\Anaconda3> .\python D:/docker/resnet/resnet_client.py
```

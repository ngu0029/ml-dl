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
```
PS D:\docker\resnet\resnet_v2_fp32_savedmodel_NHWC_jpg> docker run -p 8501:8501 --name tfserving_resnet --mount type=bin
d,source=//D:/docker/resnet/resnet_v2_fp32_savedmodel_NHWC_jpg,target=//D:/docker/resnet/model -e MODEL_NAME=resnet -t t
ensorflow/serving
```

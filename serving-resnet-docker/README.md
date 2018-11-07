A reference to [Serving ML Quickly with TensorFlow Serving and Docker](https://medium.com/tensorflow/serving-ml-quickly-with-tensorflow-serving-and-docker-7df7094aa008)

1. Download a [pre-trained ResNet](https://storage.googleapis.com/download.tensorflow.org/models/official/20181001_resnet/savedmodels/resnet_v2_fp32_savedmodel_NHWC_jpg.tar.gz) SavedModel
2. Use 7zip to untar the tar file
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

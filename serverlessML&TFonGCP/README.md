**Download the training data:**
- Open a notebook, copy and paste the following code in a code cell and then run
```
%bash
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
cd training-data-analyst
```
- As a result, a **training-data-analyst** directory is created.

**1. Lab 1 (Explore Dataset):**
- Navigate to datalab/training-data-analyst/courses/machine_learning/datasets/
- Open create_datasets.ipynb.
- In Cloud Datalab, click on Clear | Clear all Cells (click on Clear drop-down menu, select Clear all Cells).
- Read the narrative and execute each cell one by one.
Note: The lab works with [GCP BigQuerry](https://bigquery.cloud.google.com)

**2. Lab 2 (Getting Started with TensorFlow):**
- Navigate to datalab/training-data-analyst/courses/machine_learning/tensorflow/
- Open a_tfstart.ipynb.
- In Cloud Datalab, click on Clear | Clear all Cells (click on Clear drop-down menu, select Clear all Cells).
- Read the narrative and execute each cell one by one.

**3. Lab 3 (Machine Learning using tf.estimator):**
- Navigate to datalab/training-data-analyst/courses/machine_learning/tensorflow/
- Open b_estimator.ipynb.
- In Cloud Datalab, click on Clear, then click on Clear All Cells.
- Read the narrative and execute each cell in turn.

**4. Lab 4 (Refactoring to add batching and feature-creation):**
- Navigate to datalab/training-data-analyst/courses/machine_learning/tensorflow/
- Open c_batched.ipynb.
- In Cloud Datalab, click on Clear | Clear all Cells (click on Clear drop-down menu, select Clear all Cells).
- Read the narrative and execute each cell one by one.

**5. Lab 5 (Distributed training and monitoring):**
- Navigate to datalab/training-data-analyst/courses/machine_learning/tensorflow.
- Open d_traineval.ipynb.
- In Cloud Datalab, click on Clear, then click on Clear All Cells.
- Read the narrative and execute each cell in turn.

**6. Lab 6 (Scaling up ML using Cloud ML Engine):**
+ **Task 1: Launch Cloud Datalab**
> - In Cloud Shell, type:
```
datalab create dataengvm --zone us-central1-a
```
> - Datalab setup will prompt you to continue. Enter 'Y'.
> - Datalab setup will ask you for a passphrase. You can press Enter twice.
> - Datalab will take about five minutes to start. Datalab is ready when you see a message prompting you to do a "Web Preview".
+ **Task 2. Checkout notebook into Cloud Datalab**
> - Click on the Web Preview icon on the top-right corner of the Cloud Shell ribbon. 
> - Click on Change Port.
> - In the Change Preview Port dialog, in the Port Number box, enter 8081.
> - Click Change and Preview.
> - In Cloud Datalab Home page (browser), open a new notebook
> - In the new notebook, enter the following commands in the cell, and click on Run (on the top navigation bar) to run the commands:
```
%bash
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
cd training-data-analyst
```
> - Confirm that you have cloned the repo by going back to Datalab browser, and ensure you see the **training-data-analyst** directory.
+ **Task 3: Verify that you have a Cloud Storage bucket**
> - In the Console, on the Navigation menu, click Home.
> - Select and copy the Project ID. For simplicity you will use the Qwiklabs Project ID, which is already globally unique, as the bucket name. If that is already taken, create a globally unique bucket name.
> - In the Console, on the Navigation menu, click Storage > Browser.
> - Click Create Bucket.
> - Specify the following, and leave the remaining settings as their defaults (Name: <your unique bucket name (Project ID)>; Default storage class: Regional; Location: Valid Regions for this lab are asia-east1, europe-west1, us-central1, us-east1, us-west1. **Note:** Use the same bucket and location names for Environment variables for project and bucket when building and deploying your project using Cloud ML Engine - see the notebook **cloudmle**)
> - Click Create.
> - Record the name of your bucket and the location. You will need it in subsequent tasks.
+ **Task 4: Continue the lab in the notebook**
> - Navigate to datalab/training-data-analyst/courses/machine_learning/cloudmle.
> - Open cloudmle.ipynb.
> - In Cloud Datalab, click on Clear | Clear all Cells (click on Clear drop-down menu, select Clear all Cells).
> - Read the narrative and execute each cell in turn.

> See the following extract of **serving_input_fn()**
```
# Create your serving input function so that your trained model will be able to serve predictions
def serving_input_fn():
    feature_placeholders = {
        column.name: tf.placeholder(tf.float32, [None]) for column in INPUT_COLUMNS
    }
    features = feature_placeholders
    return tf.estimator.export.ServingInputReceiver(features, feature_placeholders)
```
> **Notes:** Read notebook for training locally using Python, using gcloud, or submitting training using gcloud; for local prediction using "gcloud ml-engine local predict" (only work with python 2 currently); for deploying the model and making prediction using "gcloud ml-engine predict" or REST API.

**7. Lab 7 (Feature Engineering):**
- Navigate to datalab/training-data-analyst/courses/machine_learning/feateng.
- Open feateng.ipynb.
- In Cloud Datalab, click on Clear, then click on Clear All Cells.
- Read the narrative and execute each cell in turn.
> + **Notes:** This lab uses different task.py and model.py at training-data-analyst/courses/machine_learning/feateng/taxifare/trainer, **involving two new inputs in the INPUT_COLUMNS, three engineered features, and the estimator involves bucketization and feature crosses.** See explanation of model.py in the training video "Feature Engineering Lab Review".
> + See the following extract of **serving_input_fn()** including the *add_engineered* call.
```
# Create serving input function to be able to serve predictions
def serving_input_fn():
    feature_placeholders = {
        # All the real-valued columns
        column.name: tf.placeholder(tf.float32, [None]) for column in INPUT_COLUMNS[2:7]
    }
    feature_placeholders['dayofweek'] = tf.placeholder(tf.string, [None])
    feature_placeholders['hourofday'] = tf.placeholder(tf.int32, [None])

    features = add_engineered(feature_placeholders.copy())
    return tf.estimator.export.ServingInputReceiver(features, feature_placeholders)
```    
> + A fix:
```
+ In the model.py: 
tf.feature_column.categorical_column_with_vocabulary_list('dayofweek', vocabulary_list = ['Sun', 'Mon', 'Tues', 'Wed', 'Thu', 'Fri', 'Sat'])
+ In the lab7 notebook: days = ['null', 'Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat']
>>> Correct 'Tues' in vocabulary_list to 'Tue'
```

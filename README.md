# AUV-Team14

Student Project in Autonomous Vehicles (AUV) about Road signs detection and stopping.

The following sections include short descriptions of the different programming related tasks that were performed in this project.

## 1 Input pipeline and preprocessing

### 1.1 Dataset aquisition

The used dataset is the [German Traffic Sign Recognition Benchmark (GTSRB)](https://benchmark.ini.rub.de/gtsrb_dataset.html), which can be downloaded from [this website](https://sid.erda.dk/public/archives/daaeac0d7ce1152aea9b61d9f1e19370/published-archive.html).

For training and evaluation, the following parts of the dataset, which can be found on the aforementioned download page, are used:

- `GTSRB_Final_Training_Images.zip`: Contains the training images.
- `GTSRB_Final_Test_Images.zip`: Contains the test images.
- `GTSRB_Final_Test_GT.zip`: Contains the ground truth for the test images.

### 1.2 Classification dataset (optional)

A function for reading the dataset and creating lists of images and their labels was already provided on the [dataset website](https://benchmark.ini.rub.de/gtsrb_dataset.html). Using this function, the training and test images and labels were read and stored in the `data` folder in the form of `.pkl` files. Before saving the training dataset, it was split into training and validation sets, with 10 % of the training data being used for validation. The validation set was also saved as a `.pkl` file.

### 1.3 YOLO Object detection dataset

Using the `convert_bbox_label` function, the original dataset was converted into a format suitable for object detection. Previously, each class had its own subfolder, but for object detection, all images were moved to a single folder, with the class being added to the filename.

Using the aforementioned `convert_bbox_label` function, the labels were converted into `.txt` files and the `.ppm` images were converted to `.jpg`. These steps were necessary to prepare the dataset for use with YOLO.

In order to get a balanced validation set, the training dataset was split into training and validation sets, with 10 % of the training data being used for validation. The split was stratified to ensure that each class was represented proportionally in both sets.

The test dataset was not split. The images were just moved to a separate folder, with the labels being converted the same way as for the training dataset.

The needed `.yaml` file for YOLO was also created, which contains the paths to the training, validation, and test datasets, as well as the number of classes (43).

## 2 Single model training and evaluation (YOLOv8s)

For object detection, we decided to use the YOLOv8s (small) model provided by Ultralytics. YOLOv8 is a state-of-the-art object detection architecture that builds on previous versions (YOLOv5/6/7) and features an anchor-free design and improved detection heads, while still retaining fast inference time and strong performance. We chose the **YOLOv8-small** variant because it provides a good trade-off between accuracy and efficiency, making it suitable for deployment on limited-resource platforms such as the ROSbot.

### 2.1 Training and Validation

The YOLOv8s model was initialized with pretrained weights (`yolov8s.pt`). Training was performed using the previously prepared dataset, which was structured into YOLO-compatible folders containing `.jpg` images and `.txt` label files. The `data.yaml` file defined the paths to the training, validation, and test sets and specified the total number of traffic sign classes (43).

We trained the model using the following configuration:

- **Model:** `yolov8s.pt` (Ultralytics pre-trained small model)
- **Epochs:** 50
- **Image size:** 640 × 640 pixels
- **Batch size:** 16
- **Device:** GPU (device 0)
- **Framework:** PyTorch 2.3.0

The training process was executed using the Ultralytics Python API (`model.train(...)`), which supports integrated data augmentation, validation, and checkpoint saving. The training output was saved in `runs/detect/traffic_sign_yolov8/`.

### 2.2 Evaluation

For the evaluation of the trained YOLOv8s model, the test dataset was used, with an image size of 640x640 pixels and a batch size of 16. Other hyperparameters were left unchanged. The model was evaluated using the `.val()` method. The evaluation metrics achieved were:

- **mAP@0.5:** 99.49%
- **mAP@0.5:0.95:** 97.48%
- **Precision / Recall:** Also very high across all classes

These results indicate that the YOLOv8s model successfully learned to detect and classify traffic signs with excellent accuracy, while maintaining fast inference performance. This makes it a strong candidate for deployment in embedded robotic systems where real-time detection is crucial.

## 3 Comparison of different models (...)

...

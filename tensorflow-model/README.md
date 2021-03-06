# Tensorflow Model for Person Detection on a Microcontroller

## Goal

The model should detect how many people are residing in a meeting room and retrieve their bounding boxes from a single greyscale image input.
Transforming the image into bird eye view via [OpenCV](https://opencv.org/) allows to measure distances between the detected people using the already found bounding boxes.

## Source

This model is based on the [SSD Mobileet V2 FPNLite](https://github.com/tensorflow/models/blob/master/research/object_detection/configs/tf2/ssd_mobilenet_v2_fpnlite_320x320_coco17_tpu-8.config) model.

## Files

**download_and_preprocess_mscoco_person.sh**

Downloads the MSCOCO dateset with person annotated images only. Skips download of the larger .zip files and also skips unzipping if folder already exists.

**pipeline.config**

Describes the model to train with all layers and ops. Also defines where training data is residing.

**label_map.pbtxt**

Holds training labels of this model. Just the person label in this case.

**train.ipynb**

Training notebook designed to run on a GCP AI Platform notebook instance.

## Folders

**/docs**

Contains documentations around TensorflowLite and Microcontrollers

## Wiki Entries

[1. Training](https://iteragit.iteratec.de/sparkfun/sparky-rooms/-/wikis/Google%20Cloud%20Training)

[2. Model conversion for microcontrollers](https://iteragit.iteratec.de/sparkfun/sparky-rooms/-/wikis/Convert-Model-for-Microcontroller-Tensorflow)

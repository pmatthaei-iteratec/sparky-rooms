# Reference to Original by [apivovarov](https://gist.githubusercontent.com/apivovarov/eff80275d0f72e4582c105921919b852/raw/bd6e596ab8b017d12df5053ede4ee558a143ebac/convert_tf_ssd_to_tflite.md)

# Intro

This wiki explains how to convert Tensorflow Object Detection SSD models to TFLite format.

Model Zoo: https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md

# Preparation

These instructions use `python3` and `pip3`.

## tensorflow

install tensorflow using pip3

```
sudo pip3 install tensorflow
```

## tensorflow Object Detection models

Install tensorflow Object Detection models as described below. Also, look at the installation notes for COCO API and protobuf:
https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/installation.md

### COCO API installation notes

By default COCO API Makefile uses python instead of python3. The instructions below explain how to build COCO API using python3

```
git clone https://github.com/cocodataset/cocoapi.git
cd cocoapi/PythonAPI
# vi Makefile and change python to python3
make
cp -r pycocotools <path_to_tensorflow>/models/research/
```

### protobuf installation notes

Ubuntu 16.04 apt repo has older protoc version. We need to use Manual protobuf-compiler installation. 

```
wget -O protobuf.zip https://github.com/google/protobuf/releases/download/v3.7.1/protoc-3.7.1-linux-x86_64.zip
unzip protobuf.zip
./bin/protoc object_detection/protos/*.proto --python_out=.
```

## Other

```
sudo apt-get install graphviz
```

# Generating TFLite file

## Export SSD detection model to use with tf-lite

Before converting SSD model to tflite format we need to export the model to TFLite compatible graph (.pb file)
Download SSD model from tensorflow models research repository.
https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md
Note: quantized models already contains TFLite compatible graph (.pb file). You can skip this step for quantized models.

Lets download "ssd_mobilenet_v1_coco"
http://download.tensorflow.org/models/object_detection/ssd_mobilenet_v1_coco_2018_01_28.tar.gz


```
tar zxf ssd_mobilenet_v1_coco_2018_01_28.tar.gz
ls -la ssd_mobilenet_v1_coco_2018_01_28
-rw-r--r--@   1 pivovaa  ANT\Domain Users        77 Feb  1  2018 checkpoint
-rw-r--r--@   1 pivovaa  ANT\Domain Users  29103956 Feb  1  2018 frozen_inference_graph.pb
-rw-r--r--@   1 pivovaa  ANT\Domain Users  27380740 Feb  1  2018 model.ckpt.data-00000-of-00001
-rw-r--r--@   1 pivovaa  ANT\Domain Users      8937 Feb  1  2018 model.ckpt.index
-rw-r--r--@   1 pivovaa  ANT\Domain Users   3006546 Feb  1  2018 model.ckpt.meta
-rw-r--r--@   1 pivovaa  ANT\Domain Users      4138 Feb  1  2018 pipeline.config
drwxr-xr-x@   4 pivovaa  ANT\Domain Users       136 Feb  1  2018 saved_model

# frozen_inference_graph.pb is not compatible with TFLite converter.
# We need to use the checkpoint to generate another frozen graph pb file which is compatible with TFLite converter.

# Change Dir to tensorflow/models repo.
cd models/research

export PYTHONPATH=`pwd`:`pwd`/slim

python3 object_detection/export_tflite_ssd_graph.py \
  --pipeline_config_path=/home/ubuntu/ssd_mobilenet_v1_coco_2018_01_28/pipeline.config \
  --trained_checkpoint_prefix=/home/ubuntu/ssd_mobilenet_v1_coco_2018_01_28/model.ckpt \
  --output_directory=/tmp/tflite_graph \
  --add_postprocessing_op=true

# copy tflite_graph.pb and tflite_graph.pbtxt to /home/ubuntu/ssd_mobilenet_v1_coco_2018_01_28/
cp /tmp/tflite_graph/* /home/ubuntu/ssd_mobilenet_v1_coco_2018_01_28/
```

"add_postprocessing_op" parameter adds output node TFLite_Detection_PostProcess which contains 4 output tensors (boxes, classes, scores, num_outputs)

Output tensor names will be the following:

```
# add_postprocessing_op=true
TFLite_Detection_PostProcess, TFLite_Detection_PostProcess:1, TFLite_Detection_PostProcess:2, TFLite_Detection_PostProcess:3

# add_postprocessing_op=false
raw_outputs/class_predictions,raw_outputs/box_encodings
```

`export_tflite_ssd_graph.py` changes input tensor name from `image_tensor` to `normalized_input_image_tensor`

More details: https://github.com/tensorflow/models/blob/master/research/object_detection/export_tflite_ssd_graph.py

## Convert to tf-lite format

`tflite_convert` util is part of tensorflow package installation
It is installed to `/usr/local/bin` or to `~/.local/bin`

### Convert non-quantized graph

Use the following command to convert non-quantized frozen graph to tflite format

```
tflite_convert \
--graph_def_file=/home/ubuntu/ssd_mobilenet_v1_coco_2018_01_28/tflite_graph.pb \
--output_file=/home/ubuntu/ssd_mobilenet_v1_coco_2018_01_28/model.tflite \
--output_format=TFLITE \
--input_arrays=normalized_input_image_tensor \
--input_shapes=1,300,300,3 \
--inference_type=FLOAT \
--output_arrays="TFLite_Detection_PostProcess,TFLite_Detection_PostProcess:1,TFLite_Detection_PostProcess:2,TFLite_Detection_PostProcess:3" \
--allow_custom_ops
```

More Details: https://www.tensorflow.org/lite/convert/cmdline_examples

### Convert quantized graph

Tensorflow Object Detection Model zoo has several quantized models
https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md

For example:
ssd_mobilenet_v1_quantized_coco - http://download.tensorflow.org/models/object_detection/ssd_mobilenet_v1_quantized_300x300_coco14_sync_2018_07_18.tar.gz

```
tar zxf ssd_mobilenet_v1_quantized_300x300_coco14_sync_2018_07_18.tar.gz

ls -la ssd_mobilenet_v1_quantized_300x300_coco14_sync_2018_07_18
-rw-r-----@   1 pivovaa  ANT\Domain Users  27381492 Jul 18  2018 model.ckpt.data-00000-of-00001
-rw-r-----@   1 pivovaa  ANT\Domain Users     14948 Jul 18  2018 model.ckpt.index
-rw-r-----@   1 pivovaa  ANT\Domain Users   2229805 Jul 18  2018 model.ckpt.meta
-rw-r-----@   1 pivovaa  ANT\Domain Users      4469 Jul  8  2018 pipeline.config
-rw-r--r--@   1 pivovaa  ANT\Domain Users  27692743 Jul 17  2018 tflite_graph.pb
-rw-r--r--@   1 pivovaa  ANT\Domain Users  78617899 Jul 17  2018 tflite_graph.pbtxt
```

tflite_graph.pb - is frozed graph which can be converted to tflite format

Use the following command to convert quantized frozen graph to tflite format

```
tflite_convert \
--graph_def_file=/home/ubuntu/ssd_mobilenet_v1_quantized_300x300_coco14_sync_2018_07_18/tflite_graph.pb \
--output_file=/home/ubuntu/ssd_mobilenet_v1_quantized_300x300_coco14_sync_2018_07_18/model.tflite \
--output_format=TFLITE \
--input_arrays=normalized_input_image_tensor \
--input_shapes=1,300,300,3 \
--inference_type=QUANTIZED_UINT8 \
--mean_values=128 \
--std_dev_values=127 \
--output_arrays="TFLite_Detection_PostProcess,TFLite_Detection_PostProcess:1,TFLite_Detection_PostProcess:2,TFLite_Detection_PostProcess:3" \
--allow_custom_ops
```

Use `--output_arrays="raw_outputs/class_predictions,raw_outputs/box_encodings"` if you run `export_tflite_ssd_graph.py` with `--add_postprocessing_op=false`

### Python API

Tensorflow recommends to use Python API instead of `tflite_convert` util

```
input_arrays = ["normalized_input_image_tensor"]
output_arrays = ["TFLite_Detection_PostProcess","TFLite_Detection_PostProcess:1","TFLite_Detection_PostProcess:2","TFLite_Detection_PostProcess:3"]
input_shapes = {"normalized_input_image_tensor" : [1, 300, 300, 3]}
converter = tf.contrib.lite.TFLiteConverter.from_frozen_graph(
        graph_def_file, input_arrays, output_arrays, input_shapes)
converter.inference_type = lite_constants.QUANTIZED_UINT8 if is_quant else lite_constants.FLOAT
converter.output_format = lite_constants.TFLITE
converter.allow_custom_ops = True
converter.quantized_input_stats = {"normalized_input_image_tensor": (128., 127.)} if is_quant else None
tflite_model = converter.convert()
open(out_name, "wb").write(tflite_model)
```

Full version: https://gist.github.com/apivovarov/df0c502a45755702974b42dce3c9e858

More details: https://www.tensorflow.org/lite/convert/python_api

### Visualize the graph

The converter can export a graph to the Graphviz Dot format for easy visualization. This may be useful if the conversion to TFLite is failing.

Use the following command to generate .dot file for non-quantized frozen graph

```
tflite_convert \
--graph_def_file=/home/ubuntu/ssd_mobilenet_v1_coco_2018_01_28/tflite_graph.pb \
--output_file=/home/ubuntu/ssd_mobilenet_v1_coco_2018_01_28/model.dot \
--output_format=GRAPHVIZ_DOT \
--input_arrays=normalized_input_image_tensor \
--input_shapes=1,300,300,3 \
--inference_type=FLOAT \
--output_arrays="TFLite_Detection_PostProcess,TFLite_Detection_PostProcess:1,TFLite_Detection_PostProcess:2,TFLite_Detection_PostProcess:3" \
--allow_custom_ops

# generate model.dot.pdf file
dot -Tpdf -O /home/ubuntu/ssd_mobilenet_v1_coco_2018_01_28/model.dot
```

Example: https://www.dropbox.com/s/a9wkjgeoxrhvybp/ssd_mobilenet_v1_coco_2018_01_28.tflite.pdf?dl=0

# Run Inference

Run Inference using TFLite python API:

run-tflite.py - https://gist.github.com/apivovarov/d4a1a83a66169df1309e0631d2be31f8

coco.py - https://gist.github.com/apivovarov/47722db5fb9d5bbfcc79d189f6573eda

```
./run-tflite.py
```

# Links

https://www.tensorflow.org/lite/convert/cmdline_examples

https://www.tensorflow.org/lite/convert/python_api

https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/running_on_mobile_tensorflowlite.md

https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md

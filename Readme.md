# This document captures simple instructions to run the TLT converter _engine_ for the x86 platform

TensorRT version: 7.2.2-1

CUDA version: 11.1

CUDNN version: 8.0.5


**Installation instructions:**

You can follow the approprivate version tlt-converter for Hardware: [TLT-converter](https://docs.nvidia.com/metropolis/deepstream/dev-guide/text/DS_TLT_integration.html#id16)

Check version TensorRT: `dpkg -l | grep TensorRT`

1. Copy the executable to the target device 
2. Install openssl library using the following commam.
    
    `sudo apt-get install libssl-dev`

3. Export the following environment variables
  
    `export TRT_LIB_PATH=”/usr/lib/x86_64-linux-gnu"`

    `export TRT_INC_PATH=”/usr/include/x86_64-linux-gnu”`
    
4. Run the converter following the below guide: _Sample usage_


**Note:** 

In Int8 mode, when using calibration table generated from the TLT v1.0 GA docker, if the version of the tensorrt on the deployment device is 5.x/6.x, please make sure to update the version number in the TensorRT calibration table file. Inorder to do so, 
1. Open the `calibration.bin` or `calibration.txt` file as generated by `tlt-export` using a simple text editor
2. Update the first line in calibration file from `TRT-5105-EntropyCalibration2` or `TRT-6001-EntropyCalibration2` to `TRT-7000-EntropyCalibration2`. 

> You need change the first line TRT version siutable for enviroment TRT Hardware eg: **TRT-7221-EntropyCalibration2** siutable for TensorRT-version

3. Save the edited file with the new changes. 
4. Use this file with `tlt-converter` to generate the engine.


**Sample usage:**

    Sample:
    $./tlt-converter resnet34_peoplenet_pruned.etlt -k tlt_encode -c resnet34_peoplenet_int8.txt -o output_cov/Sigmoid,output_bbox/BiasAdd -d channel,height,width -i nchw -e peoplenet34_int8.engine -m $MAX_BATCH_SIZE -t $INFERENCE_PRECISION -b $BATCH_SIZE
    Example:
    $./tlt-converter peoplenet_v2/resnet34_peoplenet_pruned.etlt -k tlt_encode -c peoplenet_v2/resnet34_peoplenet_int8.txt -o output_cov/Sigmoid,output_bbox/BiasAdd -d 3,544,960 -i nchw -e peoplenet_resnet34_int8.engine -m 1 -t int8 
    
    tlt_encode = KEY-TLT
    
**After achived file engine, you can check inference:**

Type: detectnet_v2,maskrcnn,unet [Model-arch](https://docs.nvidia.com/tlt/tlt-user-guide/text/deepstream_tlt_integration.html#id2)

    Sample:
    $tlt-infer type -e peoplenet_v2/infer_spec.txt -k tlt_encode -o infer_result -i file-image -v
 

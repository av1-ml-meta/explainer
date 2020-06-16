# Explainer

Proposal to add Machine Learning attributes as Meta in AV1 specification

# Problem we are trying to solve

Today, to apply machine vision techniques using Machine Learning like real-time object recognition on client-side in, we need to have the dedicated hardware accelerators capability and corresponding heavy loaded software stack or the cloud support. 

Let's take the case of object detection as an example; today, each client has to decode each of the video frames and apply the Machine vision algorithm. This does not scale from a hardware or a software perspective when each client has to do the same compute-intensive operations. On the other hand, if there is a generic meta to save the position coordinates of objects, only the video content producers need to perform the object detection or frame classification as part of the coded bitstream. The object-meta can be embedded as part of the compressed bitstream, and then it will be just a few lines of software changes for most of the video players to draw bounding boxes around the objects in each video frame. This would enable even old and legacy platforms & software to do this in zero cost.

# ML Metadata

Add Machine Learning metadata as part of AV1 spec, and the data entails scene-specific information for:

    - Image classification data for each frame
    - Object localization and tracking data
    - Semantic segmentation

The information consists of image labels, coordinate data of objects, scene semantics, label confidence, the scheme used for each of these such as the model name with version, dataset name, and model accuracy metric.

# Image classification data

    - Classify generic image
        String representing the scene information : str
    - Probability of confidence
      - Model architecture (eg: ResNet50_v3) : str
          Quality of classification varies with model
      - Dataset name (model meta eg: ImageNet) : str
          Quality of classification varies with dataset
    - Confidence value : int
          Classification accuracy based on the model


![classification vs obj detection](https://docs.microsoft.com/en-us/dotnet/machine-learning/tutorials/media/object-detection-onnx/img-classification-obj-detection.png)


# Localized Object Annotation*

![object_detect_ph](https://docs.microsoft.com/en-us/cognitive-toolkit/tutorial_fastrcnn/bus_01.jpg)


    - Number of objects : int
    - Coordinate information : [] 
    - Height, width : []
    - Labels : str[]
    - Probability of confidence:[]
        Model architecture (eg: Mask-RCNN_v1, Yolo) : str
        Dataset name (model meta eg: Coco*) : str
        Confidence value : int

# Metadata OBU extension (5.8.1)
    metadata_obu( )  {
        ..
        ..
        else if ( metadata_type == METADATA_TYPE_ML_ATTRIBUTES)
            metadata_ml_attributes( )
    }

# Metadata ML attributes syntax (5.8.8)

<table>
<tr>
<td> metadata_ml_attributes() {</td> <td>  Type  </td>
</tr>
<tr>
<td> 

```c
    scene_classification_data_present_flag
```
 </td> 
 <td> f(1) </td>
<tr>
<td>

```c
    if ( scene_classification_data_present_flag ) { 
```
</td>
<td></td>
</tr>
<tr>
<td> 
    
```c 
        n = scene_classification_data_description_length
```
</td> 
<td> f(8) </td>
</tr>

<tr>
<td> 
    
```c 
        scene_classification_data_description
```
</td> 
<td> string(n) </td>
</tr>
<tr>
<td> 
    
```c 
        model_architecture_name_present_flag
```
</td> 
<td> f(1) </td>
</tr>

<tr>
<td> 
    
```c 
        if ( model_architecture_name_present_flag ) {
```
</td> 
<td> </td>
</tr>


<tr>
<td> 
    
```c 
            n = model_architecture_name_length
```
</td> 
<td> f(8) </td>
</tr>


<tr>
<td> 
    
```c 
            model_architecutre_name
```
</td> 
<td> string(n) </td>
</tr>

<tr>
<td> 
    
```c 
        }
```
</td> 
<td> </td>
</tr>

<tr>
<td> 
    
```c 
        model_data_set_name_present_flag
```
</td> 
<td>f(1) </td>
</tr>

<tr>
<td> 
    
```c 
        if (model_data_set_name_present_flag) {
```
</td> 
<td></td>
</tr>

<tr>
<td> 
    
```c 
            n = model_data_set_name_length
```
</td> 
<td>f(8)</td>
</tr>

<tr>
<td> 
    
```c 
            model_dataset_name
```
</td> 
<td>string(n)</td>
</tr>


<tr>
<td> 
    
```c 
        }
```
</td> 
<td></td>
</tr>

<tr>
<td> 
    
```c 
        confidience_value
```
</td> 
<td>f(8)</td>

</tr>

<tr>
<td> 
    
```c 
    }
```
</td> 
<td></td>

</tr>


<tr>
<td> 
    
```c 
    object_annotation_present_flag
```
</td> 
<td>f(1)</td>
</tr>


<tr>
<td> 
    
```c 
    if (object_annotation_present_flag) {
```
</td> 
<td></td>
</tr>

<tr>
<td> 
    
```c 
        N = number_of_identified_objects
```
</td> 
<td>f(8) //Upto 256 objects per frame</td>
</tr>

<tr>
<td> 
    
```c 
        for ( i = 0; i < N; i++ ) {

```
</td> 
<td></td>
</tr>

<tr>
<td> 
    
```c 
            object_label_name_present_flag
```
</td> 
<td>f(1)</td>
</tr>

<tr>
<td> 
    
```c 
            if (object_label_name_present_flag) {
```
</td> 
<td></td>
</tr>

<tr>
<td> 
    
```c 
                n = object_label_name_length
```
</td> 
<td>f(8)</td>
</tr>

<tr>
<td> 
    
```c 
                object_label_name
```
</td> 
<td>string(n)</td>
</tr>

<tr>
<td> 
    
```c 
            }
```
</td> 
<td></td>
</tr>

<tr>
<td> 
    
```c 
            object_bounding_box_x_coordinate
```
</td> 
<td>f(16)</td>
</tr>



<tr>
<td> 
    
```c 
            object_bounding_box_y_coordinate
```
</td> 
<td>f(16)</td>
</tr>

<tr>
<td> 
    
```c 
            object_bounding_box_width
```
</td> 
<td>f(16)</td>
</tr>



<tr>
<td> 
    
```c 
            object_bounding_box_height
```
</td> 
<td>f(16)</td>
</tr>


<tr>
<td> 
    
```c 
            confidence_value
```
</td> 
<td>f(8)</td>
</tr>



<tr>
<td> 
    
```c 
        }
```
</td> 
<td></td>
</tr>

<tr>
<td> 
    
```c 
        model_architecture_name_present_flag
```
</td> 
<td>f(1)</td>
</tr>

<tr>
<td> 
    
```c 
        if (model_architecture_name_present_flag) {
```
</td> 
<td></td>
</tr>

<tr>
<td> 
    
```c 
            n = model_architecture_name_length
```
</td> 
<td>f(8)</td>
</tr>

<tr>
<td> 
    
```c 
            model_architecutre_name
```
</td> 
<td>string(n)</td>
</tr>


<tr>
<td> 
    
```c 
        }
```
</td> 
<td></td>
</tr>


<tr>
<td> 
    
```c 
        model_data_set_name_present_flag
```
</td> 
<td>f(1)</td>
</tr>


<tr>
<td> 
    
```c 
        if (model_data_set_name_present_flag) {
```
</td> 
<td></td>
</tr>

<tr>
<td> 
    
```c 
            n = model_architecture_name_length
```
</td> 
<td>f(8)</td>
</tr>


<tr>
<td> 
    
```c 
            model_data_set_name
```
</td> 
<td>string(n)</td>
</tr>

<tr>
<td> 
    
```c 
        }
```
</td> 
<td></td>
</tr>

<tr>
<td> 
    
```c 
    }
```
</td> 
<td></td>
</tr>

<tr>
<td> 
    
```c 
}
```
</td> 
<td></td>
</tr>
</table>

# Sample Bitstream format

Metadata OBU extension (5.8.1)
…..0x06…// METADATA_TYPE_ML_ATTRIBUTES

scene_classification_data_present_flag / scene_classification_data_description_length / scene_classification_data_description /   model_architecture_name_length / model_architecutre_name / model_data_set_name_length / model_data_set_name /  object_annotation_present_flag / number_of_identified_objects / object_label_name_length / object_label_name / object_bounding_box_x_coordinate / object_bounding_box_y_coordinate / object_bounding_box_width /  object_bounding_box_height / model_architecture_name_length / model_architecutre_name / model_data_set_name_length / model_dataset_name

/ TRUE / 0x12 / “Person, sheep, dog” /  0x08 / “ResNet50” /  0x08 / “ImageNet” / 0X5F = (95%) / 
TRUE / 0x0007 / 0x03 / “dog” / X-dog / Y-dog / W-dog / H-dog / 0x03 / “MAN”  / X-man / Y-man / W-man / H-man / 0x05 /  “sheep1” / X-sheep1 / Y-sheep1 / W-sheep1 / H-sheep1 / 0x05 /  “sheep2” / X-sheep2 / Y-sheep2 / W-sheep2 / H-sheep2 / 0x05  /  “sheep3” / X-sheep3 / Y-sheep3 / W-sheep3 / H-sheep3  / 0x05 /  “sheep4” / X-sheep4 / Y-sheep4 / W-sheep4 / H-sheep4 /  0x5 /  “sheep5” / X-sheep5 / Y-sheep5 / W-sheep5 / H-sheep5 /  0x04 / “Yolo”/ 0x04 / “Coco” / 0x5A (= 90%) /

       X-{xxx} & Y-{xxx} indicate X coordinate and Y coordinate of the object bounding box
       W-{xxx} & H-{xxx} indicate Width & Height of the object bounding box


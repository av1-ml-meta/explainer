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

# Localized Object Annotation*
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

| metadata_ml_attributes() {                           |    Type     |
| -------------------------------                      | ----------- |
|   scene_classification_data_present_flag             |     f(1)    |
|   if ( scene_classification_data_present_flag ) {    |             |
|     n = scene_classification_data_description_length |     f(8)    |
|     scene_classification_data_description            |   string(n) |
|     model_architecture_name_present_flag             |     f(1)    |
|     if ( model_architecture_name_present_flag ) {    |             |
|       n = model_architecture_name_length             |     f(8)    |
|       model_architecutre_name                        |   string(n) |
|     }                                                |             |
|     model_data_set_name_present_flag                 |    f(1)     |
|     if (model_data_set_name_present_flag) {          |             |
|       n = model_data_set_name_length                 |    f(8)     |
|       model_dataset_name                             |   string(n) |
|     }                                                |             |
|     confidience_value                                |    f(8)     |
|   }                                                  |             |
|                                                      |             | 
|   object_annotation_present_flag                       |    f(1)     |
|   if (object_annotation_present_flag) {                |             |
|     N = number_of_identified_objects                   |    f(8) //Upto 256 objects per frame |
|     for ( i = 0; i < N; i++ ) {                        |             |
|       object_label_name_present_flag                   |    f(1)     |
|       if (object_label_name_present_flag) {            |             |
|         n = object_label_name_length                   |    f(8)     |
|         object_label_name                              |  string(n)  |
|       }                                                |             |
|       object_bounding_box_y_coordinate                 |    f(16)    |
|       object_bounding_box_y_coordinate                 |    f(16)    |
|       object_bounding_box_width                        |    f(16)    |
|       object_bounding_box_height                       |    f(16)    |
|       confidience_value                                |    f(8)     |
|     }                                                  |             |
|     model_architecture_name_present_flag               |    f(1)     |
|     if (model_architecture_name_present_flag) {        |             |
|       n = model_architecture_name_length               |    f(8)     |
|       model_architecutre_name                          |  string(n)  |
|     }                                                  |             |
|     model_data_set_name_present_flag                   |    f(1)     |
|     if (model_data_set_name_present_flag) {            |             |
|       n = model_data_set_name_length                   |    f(8)     |
|       model_data_set_name                              |  string(n)  |
|     }                                                  |             |
|   }                                                    |             |
| }                                                      |             |
|                                                        |             |
 
 



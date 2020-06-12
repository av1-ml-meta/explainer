# Explainer
Proposal to add Machine Learning attributes as Meta in AV1 specification

# Problem we are trying to solve

Currently, to perform real-time object detection on client-side, either we need to have the hardwarecapability and corresponding heavy loaded software stack or the cloud support. Again, each client
has to repeat the same operations. On the other hand, If there is a generic meta to save the position coordinates of objects as part of the coded bitstream, It will only the Video Content Producers supposed to perform the object detection. The object-meta can be stuffed to the compressed bitstream. Then it will be just the matter of a few lines of software changes for most of the video players to draw circles around the images in each video frame. Even old and legacy platforms & softwares will beable to do this in zero cost

# ML Metadata

Add Machine Learning metadata as part of AV1 spec, the data entails scene-specific information for:

	- Image classification data for each frame
	- Object localization and tracking data
	- Semantic segmentation of frame 


The information consists of image labels, coordinate information of objects, scene semantics, label confidence, scheme used for each of these such as the model name with version, dataset name and model accuracy metric.

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


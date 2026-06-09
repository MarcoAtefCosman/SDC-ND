# Real-time 3D Object Detection on Point Clouds

## The complex YOLO Algorithm
In the paper Complex-YOLO: Real-time 3D Object Detection on Point Clouds(opens in a new tab), M. Simon et al. extend the famous YOLO network for bounding box detection in 2D images to 3D point clouds. As can be seen from the following figure, the main pipeline of Complex YOLO consists of three steps:
![complex yolo](imgs/complex_yolo.png)

1. **Transforming the point cloud into a bird's eye view (BEV)**

    First, the 3D point cloud is converted into a bird's eye view (BEV), which is achieved by compacting the point cloud along the upward-facing axis (the z-axis in the Waymo vehicle coordinate system). The BEV is divided into a grid consisting of equally sized cells, which enables us to treat it as an image, where each pixel corresponds to a region on the road surface. As can be seen from the following figure, several individual points often fall into the same grid element, especially on surfaces that are orthogonal to the road surface. The following figure illustrates the concept:
    ![BEV](imgs/bev.png)

    As can be seen, the density of points varies strongly between cells, depending on the presence of objects in the scene. While on the road surface, the number of points is comparatively low due to the angular resolution in vertical direction (64 laser beams), the number of points on the front, back or side of a vehicle is much higher as neighboring vertical LEDs are reflected from the same distance. This means that we can derive three pieces of information for each BEV cell, which are the intensity of the points, their height and their density. Hence, the resulting BEV map will have three channels, which from the perspective of the detection network, makes it a color image. The process of generating the BEV map is as follows:
        
    1.  First, we need to decide the area we want to encompass. For the object detection in this course, we will set the longitudinal range to 0...50m and the lateral range to -25...+25m. The rationale for choosing this particular set of parameters is based partially on the original paper as well as on design choices in existing implementations of Complex YOLO.

    2. Then, we divide the area into a grid by specifying either the resolution of the resulting BEV image or by defining the size of a single grid cell. In our implementation, we are setting the size of the BEV image to 608 x 608 pixels, which results in a spatial resolution of ≈8cm.

    3. Now that we have divided the detection area into a grid, we need to identify the set of points P_ij that falls into each cell, where i,j are the respective cell coordinates. In the following, we will be using N_i,j to refer to the number of points in a cell. As proposed in the original paper, we will assign the following information to the three channels of each cell:
        - Height H_i,j
        - Intensity I_i,j
        - Density D_i,j

    ![bev channels](imgs/bev_channels.png)

    On the top-left, you can see the BEV map with all three channels superimposed. On the top right you can observe the height coded in green. It can clearly be seen that the roofs of the vehicles have a higher intensity than the road surface. On the lower left, you can see the intensity in blue. Depending on the contrast of your screen, you might be able to distinguish objects such as rear lights or license plates. If not, don't worry, we will investigate this more closely further on in this chapter. Finally, on the lower right, the point cloud density is displayed in red and it can clearly be seen that vehicle sides, fronts and rears show up the most. Also, with increasing distance, the point density on the road surface gets smaller, which obviously is related to perspective effects and the vertical angular resolution of the lidar.

2. **Complex YOLO on BEV map**

    In the original publication, a simplified YOLOv2 CNN architecture has been used. Note that in our implementation in the mid-term project we will be using YOLOv4(opens in a new tab) instead. Extensions to the original YOLO network are a complex angle regression and an Euler-Region Proposal Network (E-RPN), which serve to obtain the direction of bounding boxes around detected objects.

    The YOLO Network has been configured to divide the image into a 16 x 32 grid and predicts 75 features. The model has a total of 18 convolutional layers and 5 pooling layers. Also, there are 3 intermediate layers, which are used for feature reorganization.

    Let us discuss how the features per grid cell are obtained:
        
    - The YOLO network predicts a fixed set of boxes per cell, in this case 5. For each box, 6 individual parameters are obtained, which are its two-dimensional position in the BEV map, its width and length and two components for the orientation angle.

    - In addition to the box parameters, there is one parameter to indicate whether the bounding box contains an actual object and is accurately placed. Also, there are three parameters to indicate whether a box belongs to the classes "car", "pedestrian" or "bicycle".

    - Finally, there are the 5 additional parameters used by the Region Proposal Network to estimate accurate object orientations and boundaries.

3. **3D bounding box re-conversion**

    One of the aspects that makes Complex YOLO special is the extension of the classical Grid RPN approach, which estimates only bounding box location and shape, by an orientation angle, which it encodes as a complex angle in Euler notation (hence the name "E-RPN") such that the orientation may be reconstructed as arctan2(Im,Re). The following figure shows the parameters estimated during the bounding box regression:
    ![box regression](imgs/box_regression.png)

    The regression values are then directly passed to the computation of a loss function, which is based on the YOLO concept (i.e. the sum of squared errors) using multi-part loss(opens in a new tab) but extends it by an Euler regression part, which is obtained by calculating the difference between the ground truth and predicted angle which is always assumed to be inside the circle shown above.

### Why use Complex YOLO ?
- One of the major advantages of the Complex YOLO networks is its speed in comparison to other currently available methods. As can be seen from the following graph, the achievable frame rate of Complex YOLO is significantly higher than e.g. PointNet or VoxelNet while achieving a similar detection performance. This makes it well suited for real-time applications such as autonomous vehicles. 
- One disadvantage of the current implementation of Complex YOLO though is the lack of bounding box height and vertical position. All bounding boxes are assumed to be located on the road surface and height is set to a pre-defined constant based on the detection class. In the tracking stage, this might lead to inaccuracies for driving scenarios with varying elevation.
![complex yolo performance](imgs/complex_yolo_performance.png)


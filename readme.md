
## Task
The task is to find the shaded segmentation mask based on a raw image, human parsing of the image, and pose estimation of the person. The guidelines provided led me to assume that the desired output is a mask of the dress placed on the raw image.

## Algorithm
1. Place points at a distance from the ankles in the body pose.
2. Create a convex hull of the shorts and those points.
3. Add a mask of the legs.  
4. Subtract masks of the shirt and arms.    
5. Subtract a mask below the line between the ankles.

I divided the images into three categories:
1. Main (shorts and short skirts), a total of 587 instances.
2. Skirts (vertical distance between ankles and the bottom point of the shorts mask is less than 100), a total of 105 instances.
3. Images without ankles (photos up to the knee and where the skirts hide the ankles), a total of 12 instances.

To create the masks of the skirts, I decided to use the lower body part mask from human parsing, as it seemed more accurate, although in most cases, the main algorithm also produced good results.

To create masks for images without ankles, I used a convex hull of the shorts mask and two points located away from the edges of the image. This gave good results, although the three images where the skirts hide the ankles were not processed as well.

## Challenges

Sometimes the ankles were positioned unfavorably for the algorithm (left-right, right-left), for example, when the model was facing away, which led to the creation of inadequate masks. However, I corrected this.

In addition, shorts masks sometimes contained outliers (e.g., a point in the lower left corner), which could lead to inadequate results. Therefore, I used ndimage from the skipy library to keep only the largest fragment of the mask. 

I also tried to implement an algorithm that searched for the edge points of the shorts masks (upper-left, upper-right, lower-right, lower-left) using the OpenCV library. Then, the lines drawn through the left and right points, when intersecting with the line between the ankle points, would create a polygon that resembled the shape of the skirt. However, the result was not stable (shorts polygons often looked like triangles).

## Extras

The results of the algorithm's work are located in the preprocessed folder, and in the _extras folder, there are images with auxiliary masks and numbers used to distribute images into the three categories described above.

## Possible Improvements

Most problems are due to artifacts in the human parsing part of the dataset. Improving the masks could lead to significant improvements in the results.

It is also possible that adding two more points, similar to the points located away from the ankles, but for knees, could help create more realistic dress geometry.
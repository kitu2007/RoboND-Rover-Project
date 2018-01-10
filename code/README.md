
Notebook Analysis
------------------
Please see video test_mapping2.mp4 on the test dataset provided.

Rock identification
------------------------
The function is 8th cell. The function is under the notebook heading Rock Identification (below the Color Thresholding) 
 -- function is called get_rock for rock sample identification using color thresholding.
 -- Since rocks are yellow, they will have high red and green and low blue values.
 -- The threshold has been set using experimental trying different values for red, green and blue. rock_color_thresh=[100,100,50]

Obstacle Identification
-------------------------
1. Anything that is not a navigable area is obstacles
2. Using the negative of navigable area (threshed) as obstable area
 obstacle_xpix, obstacle_ypix = rover_coords(1-threshed) line 32 of the process_image function

WorldMap update
---------------
1. Converted the obstacle, navigable and rock pixels from rover coordinate frame to worldmap frame using the yaw and x and y location of the robot. The world map is fixed so setting the pixels to high value (1) on the worldmap creates a heat map that can be used to visualize the navigable terrain as well as obstacle area and rocks
 -- red channel is used for obstacles.
 -- green is set for rocks (comes as yellow since rocks pixels are also obstacles and red and green gives yellow)
 -- blue is for navigable terrain

Process_image.
--------------------
  -- uses the get_rock function for rock identification
  --  (1-nav_area) for obstacle identification
  -- converts the binary perspective images into warped images (homography images viewed from top and also at a scale) using warped function which takes 4 source and destination points to compute homography matrix and then uses it to transform images
  -- convert the warped images into rover coordinates (using rover_coords function)
  -- rotate and translate these warped images to bring them into the map world (fixed), using provided pix_to_world function.  
  -- the obtained pixels for obstacles, rocks and navigable are used to update the worldmap
  data.worldmap[obstacle_y_world, obstacle_x_world, 0] +=1
   data.worldmap[rocks_y_world, rocks_x_world,1] +=1
   data.worldmap[navigable_y_world, navigable_x_world, 2] +=1

Autonomous Navigation and Mapping
-----------------------------------
perception_step()
 1. using the clicked points in the perspective image and the desired points ie. source and destination points to compute homography matrix.
 2. These points are used within the warped function to compute homography matric and then used to compute warped images
 3. Threshold the warped images to get the navigable, obstacle area and identify rock pixels
 4. convert these binary images in rover coordinates (using rover_coords function).
 5. Get the rover location and yaw values from accessing Rover.pos and Rover.yaw.
 6. Use this to compute the transformation from rover coordinate to world coordinates (using the pix_to_world function)
 5. Update the rovers worldmap by incrementing the appropriate channels -- red for obstacles, green for rocks and blue for navigable terrain.
 6. Set the Rover's nav_dists and nav_angles field. It contains the pixels that are navigable. The decision step uses this to compute the robots steer angle which is given by mean of the navigable pixles. Also, the number of pixels in nav_angles is used to see, if there are enough pixels to determine the forward path.
 
 decision_step()
 1. Didn't change much here. Tried some other smart methods for steering angle when robot is stuck but -15 gives good enough performance.
 2. The decision function uses Rover's nav_dists and nav_angles field to compute the robots steer angle which is given by mean of the navigable pixles. Also, the number of pixels in nav_angles is used to see, if there are enough pixels to determine the forward path.

Using 1024X768 resolution at good graphics quality.. Giving 31-32 frames per second

Note -- not sure how to use figure with .md file. Also, the ipython notebook has the images. 

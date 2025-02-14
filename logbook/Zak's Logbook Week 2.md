# Lab 2 - Colour and Perception 
*_Zachary Berry 23rd Jan 2024_*

## Part 1: Seeing colours and shapes 

### Blind spot  

It is interesting how the brain can fill in the blind spot based on the surrounding information. Reminds me of the spot-healing tool in Photoshop.
Observing the blind spot with the left eye is strange as you can only ever see it blurred - because the blind spot is of course outside of the 
fovea region and in your peripheral vision. 

###  Ishihara Colour Test

I got them all correct! 

###  Reverse colour 

Unfortunately, this effect didn't work - I wonder if it's because of the ambient light colour of the room, and it being slightly yellow.
This effect works because the cone cells in your eyes, or particularly the ones that detect green and yellow, become fatigued after looking at 
the image for a prolonged period of time. When I suddenly shift my gaze, the cone cells which detect red and blue are not fatigued, but the 
green and yellow ones are. The brain interprets the difference in signals between the red and blue cones, and the green and yellow, as the 
opposite colour, meaning I see the regular American flag. 

###  Troxler's fading

Troxler's fading occurs because the cells in the retina fire less signals when there are few movements in the environment. Because there is a lack of signals firing, the image can
fade from conscious perception. Staring at the cross/dot in the centre minimises micro-saccades, so the blurred surrounding image is never refreshed 
on the retina. 

###  The brain sees what it expects 

In the first images, the tables are the same length, but I perceive the blue table to be longer. The blue table has a perspective such that it is foreshortened, implying
depth that isn't there, because it's a 2D image rather than a 3D scene. If I were to perceive the same image of these tables in real life, in a 
3D environment, the blue table would have to be longer. My brain makes the same assumption even when looking at the 2D image. 

In the second image, I perceive square A to be darker than square B. However, in an editing app, when I remove the squares from the surrounding 
context, it's revealed that they are the same colour. The shadow, the consistent dark and lights of the grid lead my brain to assume that the grid
in shadow is darker. In conclusion, my vision doesn't just interpret brightness based on the raw intensity of light hitting my retina, 
and instead takes into account the surroundings and assumed lighting conditions. 

###  The grid illusion 

Attempted making the grid smaller to try and disprove the retinal ganglion cell theory for this illusion - and the illusion was less visible. 

###  The silhouette illusion 

So strange! Initially wasn't working for me but then I covered up the body and just looked at the legs, and the dancer flipped from spinning 
clockwise to anticlockwise. 

###  Incomplete triangles 

I see 8 triangles in this picture. Again, my brain is drawing lines based on the surroundings, as it assumed that there should be continuity in 
the lines and shapes, which form the outline of a triangle. 


## Part 2: Exploring colours in Matlab 


###  Converting RGB to grayscale

The *_peppers_* image displays information in the Matlab command window when this code is run. 

```
RGB = imread('peppers.png'); 
imshow(RGB)
imfinfo('peppers.png')
```


<p align="center"> <img src="../assets/Screenshot 2025-02-13 at 19.13.05.png" /> </p>



This code then displays the two images, one RBG, and one grayscale, next to each other in a montage, with a title. 

```
I = rgb2gray(RGB);
figure              
imshow(I)
imshowpair(RGB, I, 'montage')
title('Original colour image (left) grayscale image (right)');
```

<p align="center"> <img src="../assets/Screenshot 2025-02-13 at 19.58.27.png" /> </p>


###  Splitting RGB image into separate channels 

With this code, the *_peppers.png_* image can be split into red, green and blue channels. We see in the workspace panel in Matlab that each channel has a dimension of *_584 x 777_* and is of data type *_uint8_*. When an RGB image is stored in Matlab, which in this script it is stored as variable *_I_*, it has 3 dimensions, which in this case is *_584 x 777 x 3_*. The first and second dimensions represent how many rows and columns of pixels the image has respectively. The third dimension represents the 3 different colour channels, R, G and B. If we take the R channel for example, stored in the workspace panel as *_R_*, we are viewing a single two-dimensional slice of the 3D array. This slice is a 2D matrix, which stores intensity values of the *_R_* colour channel at every pixel in the image. The data type of *_R_*, and indeed *_G,B_* and *_I_*, is *_uint8_*, which stands for 8-bit unsigned integer, meaning that the colour intensities at a specific pixel can range from 0 to 255.  


```
[R,G,B] = imsplit(RGB);
montage({R, G, B},'Size',[1 3])
```

<p align="center"> <img src="../assets/Screenshot 2025-02-13 at 19.20.00.png" /> </p>

<p align="center"> <img src="../assets/Screenshot 2025-02-13 at 19.19.52.png" /> </p>



###  Mapping RGB image to HSV space and into separate channels

This code converts the RGB image to an HSV colour space. 

```
HSV = rgb2hsv(RGB);
[H,S,V] = imsplit(HSV);
montage({H,S,V}, 'Size', [1 3])
```

<p align="center"> <img src="../assets/Screenshot 2025-02-13 at 19.31.53.png" /> </p>


### XYZ colour space 

This code converts the RGB image to an XYZ colour space. The channels in an XYZ image are stored as a *_double_*, rather than a *_uint8_*, which means Matlab scales them differently, resulting in darker images.  

```
XYZ = rgb2xyz(RGB)
[X,Y,Z] = imsplit(XYZ);
montage({X,Y,Z}, 'Size', [1 3])
```

<p align="center"> <img src="../assets/Screenshot 2025-02-13 at 19.32.26.png" /> </p>


<p align="center"> <img src="../assets/Screenshot 2025-02-13 at 19.36.16.png" /> </p>


### YCBCR colour space 

Finally, this code converts the image to YCBCR colour space. 

```
YCBCR = rgb2ycbcr(RGB);
imshow(YCBCR)
```

<p align="center"> <img src="../assets/Screenshot 2025-02-13 at 19.32.42.png" /> </p>
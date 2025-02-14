# Lab 1 - Introduction to Matlab 
*_Zachary Berry 16th Jan 2024_*

## Loading the test image

Putting this code into the command window in Matlab loads *_clown.mat_*, and then prints the grayscale value at the coordinates *_(319, 20)_*, which gives an answer of *_0.1554_*. 

```
load clown
clown (20,319)
```

<p align="center"> <img src="../assets/Screenshot 2025-02-14 at 09.22.22.png" /> </p>

Then, the following code shows an image of the clown. 

```
imshow (clown)
```

<p align="center"> <img src="../assets/Screenshot 2025-02-14 at 10.31.23.png" /> </p>


## Image rotation 

For rotating the clown about the centre of the image, and by *_theta_* degrees radian, we create a function with the following format, *_function [out] = ZakRotate(In, theta)_*. Where *_In_* is the orignal clown image and *_out_* is the rotated output. 


```
function [Out] = ZakRotate(In, theta)
```


First, we determine the image dimensions, by getting the number of rows anc columns in *_In_*. Then, we compute the centre of the image by dividing the dimensions by 2, and rounding to the nearest integer. This will be the pivot of rotation. 

```
width=size(In,1);
height=size(In,2); 
CentrePoint = [round(size(In,1)/2), round(size(In,2)/2)];
```

Then, we define the rotation matrix using a standard forward rotation matrix, which rotates an image theta degrees radian about the centre in a clockwise direction. Then, we compute the *_ReverseTransformationMatrix_*, using *_inv_*. We need to calculate the inverse because when we generate the output image, we need to iterate over each output pixel and reverse map it to the corresponding input pixel. This backward mapping ensures that every pixel gets a value and avoids the holes that occur with forward mapping. 



```
forwardMatrix = [cos(theta), -sin(theta) ; sin(theta), cos(theta)] 
ReverseTransformationMatrix = inv(forwardMatrix);
```
Next, we loop over every pixel in the output image. This is done using two *_for loops_*, one for the pixels vertical positions, and one for the horizontal positions. Next, we create a 2D vector *_specifiedPixel_*, which holds the current pixels coordinates in the output image. Then, we compute the corresponding pixel in the input image that should be used for the output pixel. By doing *_specifiedPixel - CentrePoint_*, we subtract the centre of the image, ensuring that rotation happens about the origin. We can then use *_'_*, to transpose the reesult, converting it from a row vector to a column vector for matrix multiplication. Then, we use *_ReverseTransformationMatrix_* to apply the inverse rotation, which undoes the rotation for each output pixel. Finally, using *_'_*, on the *_Centrepoint_*, shifts the coordinates back so that the origin is moved back to the original centre of the image. 



```
for ypixel=1:height 
for xpixel=1:width
specifiedPixel = [xpixel, ypixel]; 
newPixel = round(ReverseTransformationMatrix*(specifiedPixel-CentrePoint)'+CentrePoint');
```
Finally, we do a boundary check, so that if the output pixel is outside the bounds of the input pixel it's painted black. This can be done using serveral conditions in an if statement. Firstly, *_newPixel(1) < 1_*, or newPixel(2) < 2_* checks if the coordinates are less than the minimum valid index, which is 1. Next, *_newPixel(1) > width_* or *_newPixel(2) > height_*, checks if the coordinates exceed the dimensions of the input image. If the output pixel is out of bounds, we assing *_0_* to it, so it will appear black. Then, in the else statement, if the coordinates are valid, the pixel value from the input image at the computed location is assigned to the current location in the output image. 


```
if newPixel(1)<1 | newPixel(2)<1 | newPixel(1)>width | newPixel(2)>height 
Out(xpixel, ypixel) = 0; 
else 
Out(xpixel, ypixel) = In(newPixel(1), newPixel(2)); 
end 
end 
end 
```

We can then call the function *_ZakRotate_* in the Matlab command window, and declare any theta value we like. Matlab will generate the *_forwardMatrix_* for this theta value and print it in the command window. 

```
theta = 45 * pi/180;
Out = ZakRotate(clown, theta);
montage({clown, Out})
```


<p align="center"> <img src="../assets/Screenshot 2025-02-14 at 10.02.47.png" /> </p>

<p align="center"> <img src="../assets/Screenshot 2025-02-14 at 10.35.11.png" /> </p>




## Image shearing 

We can shear the image of the clown in an x and y direction and then centre the result, using a function of the following format. 

```
function [Out] =  Shear(In, Xshear, Yshear)
```


The code for image shearing is very similar to image rotation, but we change a few things. Rather than using a forward rotation matrix, we use a forward shearing matrix, as in the code below. 

```
forwardMatrix = [1, Xshear; Yshear, 1];
```

The entire shearing code is below. This code still uses a *_ReverseTransformationMatrix_* to avoid holes in the output image, and the same boundary calculations to paint output pixels outside the bounds of the input pixels black. By calculating the *_CentrePoint_*, we can ensure that the *_forwardMatrix_* uses shear factors as proportions relative to the half-dimensions of the image. Using shear values as fractions of the image dimensions ensures that the transformation scales with the size of the image.

```
function [Out] =  ZakShear(In, Xshear, Yshear)
width=size(In,1);
height=size(In,2); 
CentrePoint = [round(size(In,1)/2), round(size(In,2)/2)];
forwardMatrix = [ 1, Xshear ; Yshear, 1]
ReverseTransformationMatrix = inv(forwardMatrix); 
for ypixel=1:height 
    for xpixel=1:width
        specifiedPixel = [xpixel, ypixel]; 
        newPixel = round(ReverseTransformationMatrix*(specifiedPixel- CentrePoint)'+CentrePoint');
if newPixel(1)<1 | newPixel(2)<1 | newPixel(1)>width |              
newPixel(2)>height 
Out(xpixel, ypixel) = 0; 
else 
Out(xpixel, ypixel) = In(newPixel(1), newPixel(2)); 
end 
end 
end 
```

We can also call this function in the Matlab command window like this, and get any shearing output we like. Note that *_Xshear_* and *_Yshear_* are fractions, so that the shearing is proporational to the image dimensions. 

```
Xshear = 0.56;
Yshear = 0.31; 
Out = ZakShear(clown, Xshear, Yshear); 
montage({clown, Out}) 
```

<p align="center"> <img src="../assets/Screenshot 2025-02-14 at 10.28.19.png" /> </p>

<p align="center"> <img src="../assets/Screenshot 2025-02-14 at 10.35.50.png" /> </p>

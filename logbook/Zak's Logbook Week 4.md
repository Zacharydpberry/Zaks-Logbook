# Lab 4 - Morphological Image Processing
*_Zachary Berry 12 Feb 2024_*


## Task 1: Dilation and Erosion

This first piece of code makes the SE bigger, which dilates the text more. Additionally, if you apply the SE, and dilate the image multiple times, the text dilates more each time.

### Dilation 
```
A = imread('assets/text-broken.tif');
B2 = ones(10,10);    
Bx = [1 0 1;
      0 1 0;
      1 0 1];
B1 = [0 1 0;
     1 1 1;
     0 1 0];    
A1 = imdilate(A, B1);
A2 = imdilate(A1, B1);
A3 = imdilate(A2, B1);
montage({A,A3})
```

<p align="center"> <img src="../assets/Screenshot 2025-02-13 at 13.55.34.png" /> </p>

### Strel Function 

Here I used the *_strel_* function to create a *_disk_* shaped structuruing element with a radius of 4, and then printed a neighborhood in the command window to see it's shape. 

```
SE = strel('disk',4);
SE.Neighborhood   
```
<p align="center"> <img src="../assets/Screenshot 2025-02-13 at 13.57.14.png" /> </p>

### Erosion 

The larger the radius of the *_disk_* SE that *_strel_* generates, the stronger the effects of erosion. 

```
A = imread('assets/wirebond-mask.tif');
SE2 = strel('disk',2);
SE10 = strel('disk',10);
SE20 = strel('disk',20);
E2 = imerode(A,SE2);
E10 = imerode(A,SE10);
E20 = imerode(A,SE20);
montage({A, E2, E10, E20}, "size", [2 2])
```
<p align="center"> <img src="../assets/Screenshot 2025-02-13 at 13.57.47.png" /> </p>

## Morphological Open and Close 

In side the *_strel_* function, the *_disk_* and *_diamond_* SE's, with smaller radii, produced the best results. The Gaussian filter seems to work better here at filtering the noise and preserving the details.


```
f = imread('fingerprint-noisy.tif'); 
SEnew = strel('disk', 2); 
fe = imerode(f, SEnew); 
fed = imdilate(fe, SEnew); 
fo = imopen(fed, SEnew); 
fclos = imclose(fo, SEnew); 
f_gauss = imfilter(f, w_gauss, 20);
montage({f, fe, fed, fo, fclos, f_gauss}, "size", [2 3])
```
<p align="center"> <img src="../assets/Screenshot 2025-02-13 at 14.00.50.png" /> </p>

## Boundary Detection 

The outlines or boundaries of the circles can be seen clearly in the last object, however, there is still some surrounding noise which could be cleared up with gaussian filtering. The boundaries of the new image can be seen better if you use a larger SE. 

```
I = imread('assets/blobs.tif');
I = imcomplement(I);
level = graythresh(I);
BW = imbinarize(I, level);
SEbound = strel('disk', 14); 
BWe1 = imerode(BW, SEbound); 
BWe2 = BW - BWe1; 
montage({I, BW, BWe1, BWe2}, "size", [2 2])
```
<p align="center"> <img src="../assets/Screenshot 2025-02-10 at 13.35.23.png" /> </p>

## Thickening, Thinning and *_bwmorph_*

If you keep thinning, and use *_inf_* in *_bwmorph_*, the image foreground pixels disappear completely and you are left with a black image. If you take the complement of the image (i.e., switch black and white), 
applying thickening to that image will yield the same result as if you had 
thinned the original image and then complemented it back. 

```
C = imread('fingerprint.tif');
C = imcomplement(C);
g7 = imadjust(C, [0 1], [1 0]); 
level = graythresh(C);
CBW = imbinarize(g7, level);
g = bwmorph(CBW, 'thin', 1); 
g2 = bwmorph(CBW, 'thin', 2); 
g3= bwmorph(CBW, 'thin', 3); 
g4 = bwmorph(CBW, 'thin', 4); 
g5 = bwmorph(CBW, 'thin', 5); 
g6 = bwmorph(CBW, 'thin', inf); 
montage({C, CBW, g, g2, g3, g4, g5 ,g6}, "size", [3 3])
```
<p align="centre"> <img src="../assets/Screenshot 2025-02-10 at 13.43.02.png" /> </p>
<p align="centre"> <img src="../assets/Screenshot 2025-02-10 at 20.23.10.png" /> </p>

## Connected Components and Labels 

Running this code removes the largest component, and in this case removed the "ff" letters. 

```
a = imread('assets/text.png');
imshow(a)
CC = bwconncomp(a)
numPixels = cellfun(@numel, CC.PixelIdxList);
[biggest, idx] = max(numPixels);
a(CC.PixelIdxList{idx}) = 0;
figure
imshow(a)
```
<p align="center"> <img src="../assets/Screenshot 2025-02-13 at 14.09.00.png" /> </p>

## Morphological Reconstruction 

In the initial image *_f_*, we see the input binary image of text. In *_g_*, the image is eroded with the SE, thinning the largest vertical letters - this is
the marker image. In *_fo_*, the image is opened, which removes small noise 
and preserves larger structures. Finally, the morphological reconstruction 
occurs with *_fr_*, which restores the structures lost during erosion, using *_f_* as the mask. 

```
f = imread('assets/text_bw.tif');
se = ones(17,1);
g = imerode(f, se);
fo = imopen(f, se);    
fr = imreconstruct(g, f);
montage({f, g, fo, fr}, "size", [2 2])
```

Also we can use this code to fill holes in the image, which in this case fills the "o" letters in. 

```
ff = imfill(f);
figure
montage({f, ff})
```
<p align="center"> <img src="../assets/Screenshot 2025-02-13 at 14.10.44.png" /> </p>



## Morphological Operations on Grayscale Images

Image *_f_* is the unprocessed CT scan of a head. *_gd_* shows the dilated image,
which expands bright regions, and makes structures thicker. In *_ge_* the
image is eroded, which shrinks bright regions and makes structures thinner
finally, in *_gg_*, by substracting the eroded image from the dilated image,
we see a morphological gradient, which highlights only edges and transitions 
of intensity. 

```
f = imread('assets/headCT.tif');
se = strel('square',3);
gd = imdilate(f, se);
ge = imerode(f, se);
gg = gd - ge;
montage({f, gd, ge, gg}, 'size', [2 2])
```
<p align="center"> <img src="../assets/Screenshot 2025-02-11 at 11.46.58.png" /> </p>

## Challenge 1 

This code first dilates the image using a *_strel_* structuring element of radius 1. This is to remove the noise. Then, a threshold is applied using Otsu's method, but I had to add .32 cut out some of the less bright components. After getting the binary image, I erode it to remove some of the smaller elements, and then I had to further clean it using *_bwareaopen_*, which removes components under 20 pixels. I also filled any holes using *_imfill()_*. I can then view the number of components by displaying *_CC.Numobjects_*, and then, after applying the *_cellfun()_* function, display the number of pixels in each component. As seen in the image below, the number of components, or fillings, was *_2_*. The size each filling was *_11891_* and *_7465_* respectively. 



```
I = imread('assets/fillings.tif');  
imshow(I); 
SE2 = strel('disk',1);
E2 = imdilate(I,SE2);
level = graythresh(E2) + .32;
CBW = imbinarize(E2, level);
ECBW = imerode(CBW, SE2); 
montage({I, E2, CBW, ECBW}, "size", [2 2])
BWclean = bwareaopen(ECBW, 20);  
BWclean = imfill(BWclean, 'holes');
CC = bwconncomp(BWclean);
numPixels = cellfun(@numel, CC.PixelIdxList);
fprintf('Number of connected components: %d\n', CC.NumObjects);
disp('Pixels in each component:');
disp(numPixels);
```
<p align="center"> <img src="../assets/Screenshot 2025-02-13 at 14.12.44.png" /> </p>
<p align="center"> <img src="../assets/Screenshot 2025-02-12 at 17.04.07.png" /> </p>


## Challenge 2

This code first uses a *_strel disk_* SE to erode the grayscale image, which removes some of the smaller lines on the palm. Then, I binarize the image, and use *_imcomplement_* so the larger lines can be seen more clearly. Finally, I dilate the image to remove some of hte finer details, making the larger lines more obvious. 


```
I = imread('assets/palm.tif'); 
SE4 = strel('disk', 2); 
I2 = imerode(I, SE4); 
level = graythresh(I2);
BW = imbinarize(I2, level);
imshow(BW)
BW = imcomplement(BW); 
SE3 = strel('disk', 1);
BWD = imdilate(BW, SE3);
montage({I, I2, BW, BWD}, "size", [2 2])
```
<p align="center"> <img src="../assets/Screenshot 2025-02-13 at 14.17.52.png" /> </p>


## Challenge 3 

### Method 1


This code first uses *_rgb2gray_* to make the image grayscale. Then, a threshold is applied using Otsu's method, which is used to create a binary image. I then dilated the image 3 times using *_bwmorph_* which separates the cell boundaries slightly, so the count is more accurate. Finally, I use *_bwconncomp_* to calculate the amount of components, which was *_43_*. I think the true amount is *_45_*. 

```
Blood = imread('assets/normal-blood.png'); 
imshow(Blood)
BloodB = rgb2gray(Blood);
imshow(BloodB); 
level = graythresh(BloodB);
BloodB2 = imbinarize(BloodB, level);
imshow(BloodB2); 
g = bwmorph(BloodB2, 'dilate', 3); 
imshow(g); 
montage({Blood, g}, "size", [2 1])
CC = bwconncomp(g);
numPixels = cellfun(@numel, CC.PixelIdxList);
fprintf('Number of connected components: %d\n', CC.NumObjects);
```
<p align="center"> <img src="../assets/Screenshot 2025-02-12 at 18.02.29.png" /> </p>
<p align="center"> <img src="../assets/Screenshot 2025-02-12 at 18.02.16.png" /> </p>

### Method 2


Although the first method provides a more accurate answer for this image, the code isn't scalable or robust because it counts two or more overlapping blood cells as one component. To solve this, I wrote this new code which binarizes the image, uses *_imclose_* to close up some of the gaps, using an SE of size 7. Then the holes in the image are filled with *_imfill_*. We can then subtract the filled holes image from the orignal image to reveal just the centres of the blood cells, which are the bright spots in the original image. This solves the overlapping problem, but doesn't count the cells on the edges of the image, where their centre is not visible, so provides a less accurate count of *_35_*. 

```
f = imread('assets/normal-blood.png');
g = im2gray(f);
i = imcomplement(g);
level = graythresh(i); 
bw = imbinarize(i, level);
size = 7;
se = ones(size,size); 
bwc = imclose(bw, se);
no_holes = imfill(bwc, 26, 'holes');
holes = no_holes - bwc;
montage({f, bwc, no_holes, holes})
CC = bwconncomp(holes);
num_holes = CC.NumObjects
```

<p align="center"> <img src="../assets/Screenshot 2025-02-13 at 15.36.37.png" /> </p>


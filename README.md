# steganography
Theory and practice

## Definition

Steganography is the practice of concealing a message, file, or other information within another object or message in a way that the presence of the hidden information is not apparent. It is often used to protect sensitive data or to secretly communicate messages.

The word steganography comes from the Greek words "steganos," meaning "covered," and "graphia," meaning "writing." The practice of steganography dates back to ancient times, where people used various techniques to hide messages within objects such as wax tablets or tattoos on shaved heads.

In modern times, steganography is often used in digital media, such as images, audio files, and videos. For example, one common technique is to embed a message within the least significant bits of an image or audio file. This technique is often used to hide copyright information or to secretly communicate information.

## Example

Suppose we want to hide the picture. We can you another, the bigger one for that purpose.

Suppose we want to hide little picture of the dog into the bigger picture of the cat.

<img src="pictures-readme/hide-en-01.png" alt="dog in cat" width="800">

Suppose that first pixel of dog picture is ($\color{red}{\text{145}}$, $\color{green}{\text{156}}$, $\color{blue}{\text{165}}$). In binary, we will have ($\color{red}{\text{10010001}}$, $\color{green}{\text{10011100}}$, $\color{blue}{\text{10100101}}$). We can hide these numbers if we slightly modify several pixels colors in cat picture. "Slightly" means that the modification will not be noticeable by human eye. Let's take 4 pixels in cat image and modify only last 2 digits of each pixels' colors. Here are cats' pixels (00101100, 00110001, 00111000) (00100010, 00100110, 00101100) (00011111, 00100001, 00101010) (00100001,  00100100, 00101110) and here are modified numbers for cat pixels (001011$\color{red}{\text{10}}$, 001100$\color{red}{\text{01}}$, 001110$\color{red}{\text{00}}$) (001000$\color{red}{\text{01}}$, 001001$\color{green}{\text{10}}$, 001011$\color{green}{\text{01}}$) (000111$\color{green}{\text{11}}$, 001000$\color{green}{\text{00}}$, 001010$\color{blue}{\text{10}}$) (001000$\color{blue}{\text{10}}$,  001001$\color{blue}{\text{01}}$, 001011$\color{blue}{\text{01}}$).

We can hide other pixel of the dog picture the similay way.

The information will be distributed on the cat pictre. The distribution area for the picture will be four times larger than the dog picture. So it will not be noticable for human eye.

## Python Code
Import the necessary libraries

```Python
from PIL import Image
import matplotlib.pyplot as plt
import numpy as np
```

If we are going to use Google Colab, we should mount the Google Drive ar first and then read the picture files. We should skip this section if we are going to run the program locally!

Read the secret and the public pictures from the files.

```Python
from google.colab import drive

# Mount Google Drive 
drive.mount('/content/drive')

# Read the Pictures
public_img = np.asarray(Image.open('drive/MyDrive/Cyber/cat.png'), dtype='uint8').copy()
secret_img = np.asarray(Image.open('drive/MyDrive/Cyber/dog.png'), dtype='uint8').copy()
```

You need the following section if the previous one was skipped. If you have executed the program locally, then you will need the following section in order to read private and the public pictures from the files.

```Python
public_img = np.asarray(Image.open('pictures/cat.png'), dtype='uint8').copy()
secret_img = np.asarray(Image.open('pictures/dog.png'), dtype='uint8').copy()
```

Let's show the secret image on the screen. Note that the display size of the image (which we see on the screen) was automatically selected by the library function and does not correspond to the actual size. To determine the number of points in the image, we should use the numbers written on the axes. In our case, it is even convenient, because the image of the dog is small, and zooming in to see it better is not a bad solution.

```Python
# Display the private picture
plt.imshow(secret_img)
plt.show()
```
<img src="pictures-readme/dog.png" alt="dog" width="250">

Like the secret image, the size was automatically selected by the library function. To determine the number of points in the image, we should use the numbers written on the axes.

```Python
# Display the public picture
plt.imshow(public_img)
plt.show()
```

<img src="pictures-readme/cat.png" alt="cat" width="500">

Let's prepare working functions.
- `hide_image` and `unhide_image` are the basic functions to hide and restore an image.
- `get_pixel` and `set_pixel` functions allow us to work directly with image arrays. By means of them, it is possible to view and change the pixel with specific $(x, y)$ coordinates of the image.
- For auxiliary purposes, the function `hide_pixel` is introduced, the purpose of which is to hide a single pixel. For this function to work, it needs to split the pixel color channels into small parts (which is what the `chop` function does) and merge those parts with the public image (which is what the `merge` function is designed for).

```Python
# Gets one pixel from the image
def get_pixel(img, x, y) -> np.uint8:
  return img[x, y]


# Sets one pixel to the image
def set_pixel(img, x, y, r, g, b):
  img[x, y, 0] = r
  img[x, y, 1] = g
  img[x, y, 2] = b


# Splitting one channel of the pixel into 2 bit fragments
def chop(c) -> np.uint8:
  return c >> 6, (c & 48) >> 4, (c & 12) >> 2, (c & 3)


# Merge the fragment of the colot with the public image
def merge(part, img, x, y, channel):
  pix = get_pixel(img, x, y)
  pix[channel] = (pix[channel] >> 2 << 2) | part

    
# Hides the pixel of the private image into the public image
def hide_pixel(s_img, p_img, x, y, dx = 0, dy = 0):
  z = 4*y

  s_pixel = get_pixel(s_img, x, y)

  r = s_pixel[0]
  g = s_pixel[1]
  b = s_pixel[2]

  r1, r2, r3, r4 = chop(r)
  g1, g2, g3, g4 = chop(g)
  b1, b2, b3, b4 = chop(b)

  merge(r1, p_img, x + dx, z + dy,     0)
  merge(r2, p_img, x + dx, z + dy,     1)
  merge(r3, p_img, x + dx, z + dy,     2)
  merge(r4, p_img, x + dx, z + dy + 1, 0)

  merge(g1, p_img, x + dx, z + dy + 1, 1)
  merge(g2, p_img, x + dx, z + dy + 1, 2)
  merge(g3, p_img, x + dx, z + dy + 2, 0)
  merge(g4, p_img, x + dx, z + dy + 2, 1)

  merge(b1, p_img, x + dx, z + dy + 2, 2)
  merge(b2, p_img, x + dx, z + dy + 3, 0)
  merge(b3, p_img, x + dx, z + dy + 3, 1)
  merge(b4, p_img, x + dx, z + dy + 3, 2)


# Hides the private image into the public image
def hide_image(s_img, p_img, dx = 0, dy = 0):
  for i in range(np.shape(s_img)[0]):
    for j in range(np.shape(s_img)[1]):
      hide_pixel(s_img, p_img, i, j, dx, dy)


# Restores the secret image from the public one
def unhide_image(p_img, w, h, dx = 0, dy = 0):
  unhided = np.zeros((h, w, 3), dtype='uint8')
  for i in range(h):
    for j in range(w):
      p1 = get_pixel(p_img, dx + i, dy + 4 * j)
      p2 = get_pixel(p_img, dx + i, dy + 4 * j + 1)
      p3 = get_pixel(p_img, dx + i, dy + 4 * j + 2)
      p4 = get_pixel(p_img, dx + i, dy + 4 * j + 3)

      r: np.uint8 = ((p1[0] & 3) << 6) | ((p1[1] & 3) << 4) | ((p1[2] & 3) << 2) | (p2[0] & 3)
      g: np.uint8 = ((p2[1] & 3) << 6) | ((p2[2] & 3) << 4) | ((p3[0] & 3) << 2) | (p3[1] & 3)
      b: np.uint8 = ((p3[2] & 3) << 6) | ((p4[0] & 3) << 4) | ((p4[1] & 3) << 2) | (p4[2] & 3)

      set_pixel(unhided, i, j, r, g, b)

  return unhided
```

Using the function prepared above, let's hide the secret image in public and show the resulting image on the screen

```Python
# Lets hide the private image into the public image
hide_image(secret_img, public_img, 0, 0)

# Lets show the modified public image, which already has hidden private image inside 
plt.imshow(public_img)
plt.show()
```

<img src="pictures-readme/modified-cat.png" alt="cat" width="500">

As we can see, the picture does not seem to have been changed at all! This means that our method copes well with the assigned task.

Now it's time to extract the hidden image.

Let's use the function we prepared to extract the image of the dog and display it on the screen.

```Python
# Lets unhide the private image from the public image
unhided = unhide_image(public_img, 200, 161, 0, 0)

# Lets show the extracted private image of the dog
plt.imshow(unhided)
plt.show()
```

<img src="pictures-readme/dog.png" alt="dog" width="250">

We managed to extract the private image of the dog successfully!

## Contact
paatagog@gmail.com

# ICER Image Compression Algorithm

The code in this repository implements the NASA ICER image compression algorithm. Said compression algorithm is a progressive, wavelet-based image compression algorithm designed to be resistant to data loss, making it suitable for use as the image compression algorithm when encoding images to be transmitted over unreliable delivery channels, such as those in satellite radio communications.



| ![original](C:\Users\linyi\CLionProjects\icer_compression\assets\original.bmp) | ![70kb_quota](C:\Users\linyi\CLionProjects\icer_compression\assets\70kb_quota.bmp) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| original image                                               | 70 kilobyte quota                                            |
| ![50kb_quota](C:\Users\linyi\CLionProjects\icer_compression\assets\50kb_quota.bmp) | ![30kb_quota](C:\Users\linyi\CLionProjects\icer_compression\assets\30kb_quota.bmp) |
| 50 kilobyte quota                                            | 30 kilobyte quota                                            |



## What is this?

This compression algorithm is based on [a document published by NASA](https://ipnpr.jpl.nasa.gov/progress_report/42-155/155J.pdf). This repository simply implements the algorithm as described by the paper. 

This algorithm is best described by the abstract of the document by NASA, which reads as follows:

> ICER is a progressive, wavelet-based image data compressor designed to meet the specialized needs of deep-space applications while achieving state-of-the-art compression effectiveness. ICER can provide lossless and lossy compression, and incorporates an error-containment scheme to limit the effects of data loss during transmission. The Mars Exploration Rover (MER) mission will rely primarily on a software implementation of ICER for image compression. This article describes ICER and the methods it uses to meet its goals, and explains the rationale behind the choice of methods. Performance results also are presented.



## Key Features

There are a few key design considerations which were taking into account when writing this library, which are as follows:

- Designed to only utilise integer arithmetic operations, as designed by NASA
- Avoids dynamic memory allocation in the library, so memory can be better managed in memory-constrained embedded systems
- Optimised for execution on embedded systems
- Error containment features prevent data loss from affecting the quality of the whole image
- Ability to set a output size target and stop compression once the quota is reached
- Able to compress images losslessly (if the output quota permits)



## How do I use it?

The project is designed as a C-library and as such is intended to be used as a part of a larger program, not as a standalone program. The main functions which are important for the usage of the library are:

```c
int icer_compress_image_uint8(uint8_t *image, size_t image_w, size_t image_h, uint8_t stages, enum icer_filter_types filt, uint8_t segments, icer_output_data_buf_typedef *output_data);
```

which is the function which enables the user to specify an image buffer of a specific width and height, as well as the filter coefficients to use, the number of time to perform wavelet decomposition, and the number of error containment segments to subdivide the image into

```c
int icer_decompress_image_uint8(uint8_t *image, size_t *image_w, size_t *image_h, size_t image_bufsize, uint8_t *datastream, size_t data_length, uint8_t stages, enum icer_filter_types filt, uint8_t segments);
```

which is the function which enables the user to decompress a byte stream stored inside `datastream` and fill the image and the dimensions into the buffer specified.



### Note: Work in progress!

This project is still a work in progress! Currently, images of a bit depth of 5-6 bits per pixel are supported, because the wavelet transform produces an increased dynamic range which can cause overflows. Encoding in a 16-bit word will allow for a greater bit depth per pixel. Colour image support is also yet to be implemented.


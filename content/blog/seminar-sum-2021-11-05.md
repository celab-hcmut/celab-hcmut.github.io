---
title: "[Seminar Summary] Display subsystem on FPGA: from hardware to software [EN]"
date: 2021-11-21T14:59:00.000+07:00
featureImage: images/blog/blog-seminar-summary.jpg
postImage: https://lh3.googleusercontent.com/pw/AM-JKLVLcNjKyEXmhF6jTCuVFSvvXC3tiMYq_dlAbl_Jidri8MuPYJ6MuVo7ow5dH4DX1M4QvP5FkV1Z-OK2DESJWiWyr4SkUUkbRhjxzUP50FBmO4_YgB9Nb3lGiaPrvc6x8suyjf605zgzf4nYYpiQjopZ=w1669-h873-no?authuser=0
imageUrl: https://lh3.googleusercontent.com/pw/AM-JKLVLcNjKyEXmhF6jTCuVFSvvXC3tiMYq_dlAbl_Jidri8MuPYJ6MuVo7ow5dH4DX1M4QvP5FkV1Z-OK2DESJWiWyr4SkUUkbRhjxzUP50FBmO4_YgB9Nb3lGiaPrvc6x8suyjf605zgzf4nYYpiQjopZ=w1669-h873-no?authuser=0
author: Duy Tran
---

### Topic
**Display subsystem on FPGA: from hardware to software**

### Introduction

#### Goals:
1. To help readers have the most basic understanding of the implementation of display on FPGA and display driver on Linux kernel.
2. To help readers gain basic knowledge about Linux kernel.

#### Brief:
1. Display technologies on Linux: FBDEV, DRM/KMS, V4L2.
2. Why do we choose DRM/KMS (Direct Render Manager/Kernel Mode Setting)? Because of its stability and variable of features.
3. 5 components of DRM/KMS: Framebuffer, CRTC, Plane, Encoder, Connector.
4. Hardware implementation on FPGA and software development.
5. What is X11? It is a protocol allowing applications to create objects such as windows and use basic drawing primitives.
6. Future plans:
    - Hardware development of GPU IP Core and Linux kernel driver development for it (12 months).
    - Integrate H264 or H265 IP core and develop Linux kernel driver for it (4 months).
    - Ubuntu rootfs porting to replace the Petalinux rootfs (on going).

#### Resources:

- Source code: [https://github.com/DuyTrandeLion/PYNQ-Z1](https://github.com/DuyTrandeLion/PYNQ-Z1)
- Slide: [click here](https://docs.google.com/presentation/d/1119vFQNhGSqU__s178U-ZZwy85gwyTc_/edit?usp=sharing&ouid=114541921222835776509&rtpof=true&sd=true) (HCMUT account required)

### Seminar recored video

Watch the video at [here](https://drive.google.com/file/d/1Ytv7jb7QDYpqlbnSsoheZWOrSSUVzmm6/view?usp=sharing).  

This record can be accessed by HCMUT accounts. If you don’t have any HCMUT account, please request for access permission by a Gmail, thank you.

### About the speaker

Mr Tran Pham Duy, Embedded Software Engineer from AXON Enterprise, an alumni in Computer Engineering (K2014), and also a graduate student in Computer Science at Ho Chi Minh City University of Technology. He has nearly 4 years of experience on Electrical & Electronics and Embedded Systems. His current work involves both hardware design and software development. Beside contributing to the company, he also develops several projects for open source community. His goal is to become a Specialist Engineer and he have been trying my best to archive it.



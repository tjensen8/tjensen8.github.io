---
layout: post
title: Thesis Abstract (Teaser)
---

As many folks know, I have been working for several months to write out my thesis. As I am excited that I am (hopefully) nearing completion, I wanted to share the abstract. The abstract will serve as a teaser for a summary to come once the paper has been fully reviewed and approved. 

When the paper has been fully reviewed and approved by Northwestern, I'll post a summary here and link to the paper and it's relevant publication, if applicable. In the meantime, enjoy the draft abstract as a teaser of more to come.

<br>
<br>
<br>

<div align="center">

<b> Abstract Draft </b>
<br>
Landing a probe on an asteroid can require thousands of hours of work to identify areas suitable for landing. In fact, a mission to the asteroid Bennu in 2020 required significant effort on the part of scientists and volunteers to find landing areas and label rocks inside of the surface of Bennu. Even though technology capabilities are increasing, this manual review is still necessary for a successful mission. I contribute to this work by creating and evaluating five deep learning architectures to automate this task. I use an existing architecture and create four custom architectures to detect rocks using synthetic lunar images. Two of the architectures are an adaptation of Unet structure. The remaining two utilize both a grayscale image and an image processed by a Sobel edge detector. After preprocessing, I test the best two models on real images from NASA’s mission to the asteroid Bennu and create a global rock mosaic. I find that Unet performs the best on synthetic data but is sensitive to the difference between the datasets and does not have the same performance on real images. In contrast, a simpler custom model architecture named “Y Model, High Granularity” has the opposite results of Unet with poorer performance on synthetic images but better performance on real images. While the findings promise that there is opportunity to automate rock-finding tasks in missions, additional architectures should be explored. To that end, I recommend that Y Model, High Granularity be used over Unet, given the improved performance. 
</div>
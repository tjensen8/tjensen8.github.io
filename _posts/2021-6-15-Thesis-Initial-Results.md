---
layout: post
title: A visual overview of modeling results on the surface of Bennu.
---

## Overview

As my thesis is in the process of being approved, I thought it would be good to show some of the results from the modeling process. A more comprehensive executive summary will follow once the thesis is fully approved. Two model architectures are tested over the entirety of the surface of the asteroid Bennu. The architectures tested include Unet of Ronneberger, Fischer, and Brox (https://arxiv.org/abs/1505.04597) and a custom architecture named "Y Model, High Granularity" (YMHG) developed specifically for this thesis. 

<b> Figure 14 From Thesis: Visual Comparison of Unet vs Y Model High Granularity (YMHG) </b>

![Unet vs Y Model, High Granularity (YMHG)](/images/unet-vs-ymhg-far.PNG)


## Discussion of Figure 14

For the image of landing area Nightingale (white circle) in Figure 14, both models predict that Nightingale is more suitable for landing than the surrounding area. However, there are unique characteristics between the two heatmaps produced. 

The Unet model heatmap in Figure 14 does not capture rocks that are extremely large or small, as compared with the rest of the rocks in the image. The model captures areas that have rocks that are the most common size or areas that have a significant number of small rocks as shown in Figure 14 in the square boxes. In addition, Unet misses an areas of the image that clearly has rocks, as found in Figure 14, in the left hand box. Unet also misses labeling the large rocks in the image as found in Figure 14, in the right hand box. Within the circled landing area, Unet captures the major rocks and obstacles in the area but ignores the clearly visible small rocks.

In contrast, YMHG covers a significant amount of the rocky areas in the image while excluding the landing site itself. However, it misses covering the large rocks in the area, such as the one found in Figure 14, in the Y Model HG white box. Inside of the circled landing area, YMHG picks up on most of the visible rocks and highlights smaller rocks with darker red, which means that the model is applying less certainty to the rocks themselves. This model’s ability to capture smaller rocks at a distance is useful in getting a more complete look at the rocks in a crater, but the model is more conservative in predictions than the Unet model. 


## What's Next?

I will continue to post tasters of the modeling performance leading to the full approval of the thesis. Once the thesis has been fully approved, I will release an executive summary of the work and link to the thesis publication.
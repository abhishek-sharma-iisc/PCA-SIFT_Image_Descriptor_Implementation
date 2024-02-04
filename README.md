# PCA-SIFT-implementation-from-scratch

PCA-SIFT: A More Distinctive Representation for Local Image Descriptors

**Abstract**

Stable local feature detection and representation is a fundamental component of many image
registration and object recognition algorithms. Mikolajczyk and Schmid recently evaluated a variety of approaches and identified the
SIFT algorithm as being the most resistant to common
image deformations. This paper examines (and improves
upon) the local image descriptor used by SIFT. Like SIFT,
our descriptors encode the salient aspects of the image gradient in the feature point’s neighborhood; however, instead
of using SIFT’s smoothed weighted histograms, we apply
Principal Components Analysis (PCA) to the normalized
gradient patch. Our experiments demonstrate that the PCAbased local descriptors are more distinctive, more robust to
image deformations, and more compact than the standard
SIFT representation. We also present results showing that
using these descriptors in an image retrieval application results in increased accuracy and faster matching.

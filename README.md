# PCA-SIFT_Image_Descriptor_Implementation

Reference: https://ieeexplore.ieee.org/document/1315206

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

**1. Introduction**

Local descriptors [6, 12, 18] are commonly employed in a
number of real-world applications such as object recognition [3, 11] and image retrieval [13] because they can be
computed efficiently, are resistant to partial occlusion, and
are relatively insensitive to changes in viewpoint. There are
two considerations to using local descriptors in these applications. First, we must localize the interest point in position
and scale. Typically, interest points are placed at local peaks
in a scale-space search, and filtered to preserve only those
that are likely to remain stable over transformations. Second, we must build a description of the interest point; ideally, this description should be distinctive (reliably differentiating one interest point from others), concise, and invariant over transformations caused by changes in camera pose
and lighting. While the localization and description aspects
of interest point algorithms are often designed together, the
solutions to these two problems are independent [14]. This
paper focuses on approaches to the second aspect – the construction and evaluation of local descriptor representations.
Mikolajczyk and Schmid [14] presented a comparative
study of several local descriptors including steerable filters [4], differential invariants [9], moment invariants [18],
complex filters [16], SIFT [11], and cross-correlation of different types of interest points [6, 13]. Their experiments
showed that the ranking of accuracy for the different algorithms was relatively insensitive to the method employed to
find interest points in the image but was dependent on the
representation used to model the image patch around the
interest point. Since their best matching results were obtained using the SIFT descriptor, this paper focuses on that
algorithm and explores alternatives to its local descriptor
representation.
The remainder of this paper is organized as follows. Section 2 reviews the relevant aspects of the SIFT algorithm.
Section 3 details our PCA-based representation for local
features (PCA-SIFT). Section 4 presents our evaluation
methodology and performance metrics. Section 5 provides
detailed experimental results comparing PCA-SIFT to standard SIFT on feature-matching experiments and also in the
context of an image retrieval application. Section 6 examines the reasons behind PCA-SIFT’s accuracy by exploring
the role of different components in the representation. Finally, Section 7 summarizes the contributions of this paper.

**2. Review of the SIFT Algorithm**

SIFT, as described in [12], consists of four major stages:
(1) scale-space peak selection; (2) keypoint localization;
(3) orientation assignment; (4) keypoint descriptor. In the
first stage, potential interest points are identified by scanning the image over location and scale. This is implemented efficiently by constructing a Gaussian pyramid and
searching for local peaks (termed keypoints) in a series of
difference-of-Gaussian (DoG) images. In the second stage,
candidate keypoints are localized to sub-pixel accuracy and
eliminated if found to be unstable. The third identifies the
dominant orientations for each keypoint based on its local
image patch. The assigned orientation(s), scale and location for each keypoint enables SIFT to construct a canonical view for the keypoint that is invariant to similarity transforms. The final stage builds a local image descriptor for
each keypoint, based upon the image gradients in its local
neighborhood (discussed below in greater detail). The first
three stages will not be discussed further in this paper since
our work makes no contributions to those areas.
The final (keypoint descriptor) stage of the SIFT algorithm builds a representation for each keypoint based on
a patch of pixels in its local neighborhood. Note that the
patch has been previously centered about the keypoint’s location, rotated on the basis of its dominant orientation and
scaled to the appropriate size. The goal is to create a descriptor for the patch that is compact, highly distinctive (i.e.,
patches from different keypoints map to different representations) and yet robust to changes in illumination and camera viewpoint (i.e., the same keypoint in different images
maps to similar representations). As discussed in [12], obvious approaches such as normalized correlation between
image patches do not work since they are overly sensitive
to registration errors and non-rigid deformations. The standard keypoint descriptor used by SIFT is created by sampling the magnitudes and orientations of the image gradient
in the patch around the keypoint, and building smoothed
orientation histograms to capture the important aspects of
the patch. A 4×4 array of histograms, each with 8 orientation bins, captures the rough spatial structure of the patch.
This 128-element vector is then normalized to unit length
and thresholded to remove elements with small values.
The standard SIFT keypoint descriptor representation is
noteworthy in several respects: (1) the representation is
carefully designed to avoid problems due to boundary effects — smooth changes in location, orientation and scale
do not cause radical changes in the feature vector; (2) it
is fairly compact, expressing the patch of pixels using a
128 element vector; (3) while not explicitly invariant to
affine transformations, the representation is surprisingly resilient to deformations such as those caused by perspective effects. These characteristics are evidenced in excellent
matching performance against competing algorithms [14].
On the other hand, the construction of the standard SIFT
feature vector is complicated and the choices behind its specific design (as given in [12]) are not clear. Our initial goal
was to explore simpler alternatives and to empirically evaluate the tradeoffs. However, as discussed in the remainder of
this paper, we discovered that our alternate representation
was theoretically simpler, more compact, faster and more
accurate than the standard SIFT descriptor. To ensure that
our results are an accurate reflection of reality, we use the
original SIFT source code and restrict our changes to the
fourth stage.

**3. PCA-based SIFT descriptors**

Our algorithm for local descriptors (termed PCA-SIFT) accepts the same input as the standard SIFT descriptor: the
sub-pixel location, scale, and dominant orientations of the
keypoint. We extract a 41×41 patch at the given scale,
centered over the keypoint, and rotated to align its dominant orientation to a canonical direction.1 PCA-SIFT can
be summarized in the following steps: (1) pre-compute an
eigenspace to express the gradient images of local patches;
(2) given a patch, compute its local image gradient; (3)
project the gradient image vector using the eigenspace to
derive a compact feature vector. This feature vector is significantly smaller than the standard SIFT feature vector,
and can be used with the same matching algorithms. The
Euclidean distance between two feature vectors is used to
determine whether the two vectors correspond to the same
keypoint in different images.
Principal Component Analysis (PCA) [7] is a standard technique for dimensionality reduction and has been
applied to a broad class of computer vision problems,
including feature selection (e.g., [5]), object recognition
(e.g., [15]) and face recognition (e.g., [17]). While PCA
suffers from a number of shortcomings [8, 10], such as its
implicit assumption of Gaussian distributions and its restriction to orthogonal linear combinations, it remains popular
due to its simplicity. The idea of applying PCA to image
patches is not novel (e.g., [3]). Our contribution lies in rigorously demonstrating that PCA is well-suited to representing keypoint patches (once they have been transformed into
a canonical scale, position and orientation), and that this
representation significantly improves SIFT’s matching performance. PCA-SIFT is detailed in the following subsections.

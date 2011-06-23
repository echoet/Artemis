Background
==========
One of the biggest issues in dealing with aerial imagery is calculating altitude and rotation offsets due to sensor and optical calibration problems.  It is my (Alex Kesling) opinion that we may be able to calculate these automatically and dynamically using only the information provided by the estimated location of the image and simple annotations (whether captured automatically or manually).  This would provide us with a simple first step toward mosaicing without the significant overhead of most full mosaic algorithms.

This may work brilliantly when the telemetry is "good enough" but not properly calibrated, but there will still be issues, however, when trying to adjust rotation offsets on a full imagery run basis instead of per "flight track."  If we can further break apart the imagery run into individual flight tracks we may be able to tune our offsets to a point where most (if not all) targets are within 15 feet (an educated guess pulled out of my arse) of their actually coordinates.

This "per flight track" adjustment was suggested years ago, but never fully persued. Maybe we can finally resolve this issue.

Goal
====
Construct a utility which will calculate the offsets automatically.  It will also be able to update the offsets if provided with a new image/annotation.  The runtime of this program should be less than 0.5 seconds (on a single core of an OMAP4430 ARM) in worst case.

General
=======
It will take and output JSON which summarizes image/annotation and previous offset estimations.  In this fashion it can be "prior run" agnostic while still being useful in the case where it isn't fully recalculating everything.

Potential Routes
================
When using our "imagery viewer" we tend to futz with it until everything lines up.  This generally means guesstimating where we think the average intersection of multiple image/annotations is and adjusting everything accordingly.  This can be problematic especially when the annotation is at the edge of an image (since the offset will change different parts of the image at different rates).  But it all really reduces to an intuitionistic averaging.  Because of this, I expect the solution will be found in some form of averaging algorithm.

Basic Averaging: ((x_1+...x_n)/n, (y_1+...y_n)/n), then run a stable search until we have a suitable distance converge.

Centroid Averaging: Use the same approach as one would with basic averaging but instead calculate the centroid of the given image/annotations.

Bayes:  We may be able to average out offsets with a Bayesian inference (which is potentially more accurate than (x_1+...x_n)/n ).

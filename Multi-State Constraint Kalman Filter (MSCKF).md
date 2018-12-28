# Multi-State Constraint Kalman Filter (MSCKF)

fork from here https://github.com/tomas789/tonav.wiki.git

It took me some time to put together all the literature about MSCKF, I've decided to save you the work and publish it here.

First of all, it is quite interesting to understand historical developments of MSKCF. Before the world begun, we had nothing very useful to perform efficient localization using gyroscope, accelerometer and camera. First broadly used technique was *EKF-SLAM*. As name suggests, it is an Extended Kalman filter based technique for solving SLAM. It usually includes current attitude, position and velocity estimates in state. Beside that it includes positions of known landmarks (usually visual features but can be anything). Using traditional EKF it then estimates positions of known landmarks and robot itself. It works really well (according to literature). Main drawback of it is computational complexity that is O(n^3). It means you usually can afford to include only few features to achieve real-time operation. There seems to be an alternative called FastSLAM but I don't know much about it. Looks like it uses particle filtering (which I really love!) instead of EKF which means you can control tradeoff between speed and accuracy.

Beside EKF-SLAM, there are alternatives that employ iterative minimization over sliding window of last few "items" (usually camera poses). These filters might have some advantage because one can do iterative re-linearization. Which means you can almost get rid of linearization errors. Main drawback is again computational complexity, which looks to be similar to EKF-SLAM ([definitely check out this link](https://github.com/utiasSTARS/msckf-swf-comparison)).

Then in 2006, [Mourikis](http://www.ee.ucr.edu/~mourikis/) and [Roumeliotis](http://www-users.cs.umn.edu/~stergios/) came with MSCKF. It uses accelerometer and gyroscope measurements for localization and camera for EKF updates. It was first published in [A Multi-State Constraint Kalman Filter for Vision-aided Inertial Navigation](http://www.ee.ucr.edu/~mourikis/tech_reports/TR_MSCKF.pdf). It looks very promising and authors reported total error of 10 meters in total of 3.2 km traveled. It means an error of 0.31% of the travelled distance which is really impressive. But after that, there was few papers that extended it even further and made it really usable solution. Main advantages are self calibration which means a lot of parameters are estimated directly by the filter as opposed to manual offline calibration. It can save you a lot of time! Second equally great advantage is use of rolling shutter camera model, which can be found in every smartphone. It is very elegant solution because by modeling rolling shutter effects authors reported quite significant improvements in accuracy. Who'd guess it. Cheaper camera means better performance.

## Bibliography

### 2006: [A Multi-State Constraint Kalman Filter for Vision-aided Inertial Navigation](http://www.ee.ucr.edu/~mourikis/tech_reports/TR_MSCKF.pdf)

This is the original work. It is great overview of how it all works. You have to keep in mind that a lot of things changes since then like self-calibration, rolling shutter and so on.

### May 2012: [Improving the Accuracy of EKF-Based Visual-Inertial Odometry](http://www.ee.ucr.edu/~mourikis/papers/Li2012-ICRA.pdf)

In this work they analyze observability properties of MSKCF and propose few changes to ensure good properties in terms of both accuracy and consistency. For example they proposed to use global rotation errors in error state instead of local ones. Such a small change leads to quite significant improvements (according to authors).

### July 2012: [Optimization-based Estimator Design for Vision-aided Inertial Navigation](http://www.roboticsproceedings.org/rss08/p31.html)

### 2013: [3-D Motion Estimation and Online Temporal Calibration for Camera-IMU Systems](http://www.ee.ucr.edu/~mourikis/papers/Li2013ICRA.pdf)

Probably first paper presenting camera-to-IMU time offset self-calibration. Not completely sure.

### May 2013: [Real-time Motion Tracking on a Cellphone using Inertial Sensing and a Rolling-Shutter Camera](http://www.ee.ucr.edu/~mourikis/papers/Li2013ICRA_b.pdf)

This paper brings rolling shutter camera in. Paper itself is well written and authors reported very significant improvements in terms of accuracy. They reported localization error of 0.58% and 0.8% of total traveled distance using smartphone(!).

### 2014: [Online Temporal Calibration for Camera-IMU Systems: Theory and Algorithms](http://www.ee.ucr.edu/~mourikis/papers/Li2014IJRR_timing.pdf)

### June 2014: [High-fidelity Sensor Modeling and Self-Calibration in Vision-aided Inertial Navigation](http://www.ee.ucr.edu/~mourikis/papers/Li2014ICRA.pdf)

Features a lot of self-calibration things. Namely self calibration of IMU biases, misalignment and scale factors of the IMU sensors, acceleration dependence (typically called g-sensitivity) of the gyroscope measurements, camera-to-IMU spatial configuration, camera intrinsic parameters including lens distortion, image readout time of the rolling shutter camera and the time offset between the timestamps of the camera and the IMU. This paper is really nice and makes MSKCF much more user-friendly. You want definitely all the things included here.

### Aug 2014: [Monocular Visual Inertial Odometry on a Mobile Device](https://vision.in.tum.de/_media/spezial/bib/shelley14msc.pdf) [Youtube](https://www.youtube.com/watch?v=Xrq56SZONvo)

This is very nice work Michael Andrew Shelley's Master's thesis. I use it a lot when implementing Tonav. For me his biggest added value is derivations of all the Jacobians and other things. It is very error prone and Michael did a great work.

**Works from other authors**

### Joan Solà, [Course on SLAM](http://www.iri.upc.edu/people/jsola/JoanSola/objectes/toolbox/courseSLAM.pdf)

Nice introduction to SLAM. In general, I've found those papers from Joan Solà very useful.

### Joan Solà, [Quaternion kinematics for the error-state KF](http://www.iri.upc.edu/people/jsola/JoanSola/objectes/notes/kinematics.pdf)

Best quaternion overview I've ever seen. He describes quaternions itself, operations with them, time derivatives and integrations, different notations (very important!) and ES-KF. I'm using this a lot.

### Lee E Clement, Valentin Peretroukhin, Jacob Lambert, and Jonathan Kelly,

[The Battle for Filter Supremacy](http://leeclem.net/assets/docs/crv2015_battle_paper.pdf) [slides](http://leeclem.net/assets/docs/crv2015_battle_slides.pdf) [GitHub](https://github.com/utiasSTARS/msckf-swf-comparison)

Nice paper comparing SWF and MSKCF. Great thing is author gave his source codes public at Github. In fact, this is the only public implementation of MSKCF I'm aware of.

### Faust Terrado González, [Visual Inertial Odometry for Mobile Robotics](http://upcommons.upc.edu/bitstream/handle/2099.1/26213/103146.pdf?sequence=1)

I have only scanned it. It is implementation of one of the earlier versions of MSKCF. Author describes some parts in more details.

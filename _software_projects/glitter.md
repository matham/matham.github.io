---
title: "Glitter: App to manually track lab animal behavior"
excerpt: "[Glitter](https://github.com/matham/glitter) is a Python app to manually code animal behavior and position using video recordings."
header:
  image: /assets/images/glitter.png
  teaser: /assets/images/glitter_small.png
gallery:
  - url: /assets/images/Glitter-screenshots1.jpg
    image_path: /assets/images/Glitter-screenshots1.jpg
    alt: "Glitter"
  - url: /assets/images/Glitter-screenshots2.jpg
    image_path: /assets/images/Glitter-screenshots2.jpg
    alt: "Glitter"
  - url: /assets/images/Glitter-screenshots3.jpg
    image_path: /assets/images/Glitter-screenshots3.jpg
    alt: "Glitter"
  - url: /assets/images/Glitter-screenshots4.jpg
    image_path: /assets/images/Glitter-screenshots4.jpg
    alt: "Glitter"
  - url: /assets/images/Glitter-screenshots5.jpg
    image_path: /assets/images/Glitter-screenshots5.jpg
    alt: "Glitter"
---

[Glitter](https://github.com/matham/glitter) is a app written in Python, using [Kivy](kivy.org) and [FFPyPlayer](/software_projects/ffpyplayer/), that enables users to efficiently code animal behaviors (e.g. eating) using video recording of experiments. It also allows users to track the position of the animal using touch or with a mouse. Data is saved in the [HDF5](https://www.hdfgroup.org/) format and can be exported and analyzed using Python scripts.

## Background

Most behavior experiments with animals that are not live-scored, are video recorded, scored by multiple human scores, and then statistically analyzed. Although there is commercial software to track animal position, it is often inaccurate, especially for experiments under difficult or uneven backgrounds. No software currently commercially exists that can automatically score higher level behavior, such as eating accurately.

We needed software that can:

* Replay experimental video,
* Support manual coding of higher level behavior by having a user press a button or a keyboard key when a behavior occurs,
* Support user tracing of animal position using touch screen or a mouse. Either placing new traces, or correcting the automatic traces of commercial software. 
* Support drawing of areas of interest so we can compute when the animal traces intersect those zones.
* Data file management of the resulting coded data.
* Export and analysis of the GUI coded data.

## Glitter

Glitter is Python app developed to meet these requirements. It is written in Python and uses [Kivy](kivy.org) for the GUI, [FFPyPlayer](/software_projects/ffpyplayer/) for the video, and [HDF5](https://www.hdfgroup.org/) for data storage.

### Interface

Typically, a user

* Loads an experiment video file and creates a data file for it.
* Creates a data channels for the desired behavior to be tracked. E.g.
  * A channel that is controlled by the ``g`` key, which when pressed indicates the animal is grooming.
  * Two channels that track the animals head and tail, respectively.
  * A area of interest such as a reward cup.
* Start coding the video
  * Video playback is controlled using the
    * Space key for pausing/playing.
    * Right/left arrow keys for seeking.
    * Up/down arrow keys for logarithmic control over the play speed.
  * Behavior data is visually shown on a timeline to indicate where behavior occurred, and animal position is similarly displayed.

### Analysis

After coding, data is accessible in Python using a object oriented interface. We can export information such as the duration of behavior events or the speed of the animal in a particular zone.

{% include gallery caption="Glitter gallery" %}

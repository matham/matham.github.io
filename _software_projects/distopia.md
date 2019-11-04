---
title: "Distopia: Collaboratively exploring gerrymandering"
excerpt: "A research tool to explore how human and robots collaborate on a design problem: gerrymandering."
header:
  image: /assets/images/distopia_districts.png
  teaser: /assets/images/distopia_districts_small.png
---

[Distopia](https://github.com/hrc2da/distopia/) is the backend and **a** frontend for a research tool that explores how humans and robots collaborate to design voting districts and gerrymander.

## Background

M.Eng. CS (masters of engineering in CS) project in Guy Hoffman's [HRI lab](https://hrc2.io/), with Matt Law (grad student).

The research project is about how humans collaborate with robots to explore the problem space in a design problem. Specifically, how robots can be helpful to humans when they collaboratively design voting precincts using a touch user interface (TUI) shared by the robot and the human.

![Architecture]({{ site.url }}{{ site.baseurl }}/assets/images/distopia.svg)

Components:

* **TUI table**: The the human and the robot use special objects to interact with the TUI table to update the precinct designs. Distopia visually projects the designs on the table.
* **Robot**: The robot receives the design state from distopia over a [ROS](https://www.ros.org/) bridge and uses it to collaboratively suggest a different design.
* **Heads-up display (HUD)**: Distopia sends evaluation metrics of the design to the HUD, which displays it to the human.
* **Distopia**: Is the central server that manages the state. It receives the positions of the design objects over the TUI interface, computes the new design and projects it on the table. It also sends the state to the robot and the evaluation metrics to the HUD.

## Distopia

Distopia is the server that manages the designs (not including the agent) and the frontend that is projected on the table. It is written in Python using OOP and uses [Kivy](kivy.org) for its frontend. [Cython](https://cython.org/) is used to optimize computation bottlenecks such as polygon collision and boundary computation. See the [docs](https://hrc2da.github.io/distopia/) for the API.

### Districts and precincts

The [precinct](https://hrc2da.github.io/distopia/precinct.html) class describes the low level atomic precincts that are assigned to a [district](https://hrc2da.github.io/distopia/district.html) based on the design. There are [precinct](https://hrc2da.github.io/distopia/precinct.html#precinct-metrics), [district](https://hrc2da.github.io/distopia/district.html#district-metrics), and [state](https://hrc2da.github.io/distopia/metrics.html) specific metric classes that evaluate the design and are associated with the respective classes. Each metric class knows how to compute its specific type of metric (e.g. a histogram of income for a district, or compactness across the state).

The [mapping](https://hrc2da.github.io/distopia/mapping.html) module manages the state and computes the precinct-districts assignment using the [Voronoi](https://en.wikipedia.org/wiki/Voronoi_diagram) method based on [SciPy](https://docs.scipy.org/doc/scipy-0.18.1/reference/generated/scipy.spatial.Voronoi.html). It uses an internal thread to not block the main Kivy GUI thread.

### Apps

* **Data**: The [Data](https://github.com/hrc2da/distopia/blob/master/distopia/app/voronoi_data.py) module provides a OOP interface to loading state data to be used by the metrics, e.g. precinct level economic data. It also loads the precinct polygon-represented boundaries for the state. Currently the state of Wisconsin is being used, but any state can be loaded, provided the data.
* **App**: The main Distopia [app](https://github.com/hrc2da/distopia/blob/master/distopia/app/voronoi.py) uses Kivy to project the state and the design on the table (see header image). It uses a [json config file](https://github.com/hrc2da/distopia/blob/master/distopia/data/config.json) to fully configure the interface.
* **ROS**: Distopia uses the [ROS module](https://github.com/hrc2da/distopia/blob/master/distopia/app/ros.py) to communicate with the agent and the HUD to send them the designs and design evaluation.
* **Calibration**: A [calibration app](https://github.com/hrc2da/distopia/blob/master/distopia/app/alignment_app.py) is used to align the TUI table to the Kivy projected interface. 
* **Agent**: A [agent interface](https://github.com/hrc2da/distopia/blob/master/distopia/app/agent.py) is provided to allow an agent to to quickly compute the value of a design by simply providing the positions of all the fiducials. To be used for training the robotic agent.

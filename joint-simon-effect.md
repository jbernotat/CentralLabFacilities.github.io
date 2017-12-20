Test

# "Reproduction of Joint Simon effects for non-human co-actors using the NAO Robot in a cooperative HRI task"

## Table of Content

0. Introduction
1. Hardware Requirements and Prerequisites
2. Software Requirements and Prerequisites
3. Deploying the Software Infrastructure for the Experiment
4. Physical Experiment Setup
5. Subjects
6. Executing the Experiment
7. Results
8. Literature

## Introduction

This experiment is a reproduction of the "Joint Simon effects for non-human co-actors" experiment 
by Stenzel et al. [1] which has also been further reviewed in [2]. In order to reproduce our experiment, 
please read this guideline carefully and execute the mentioned steps in the correct order. 
The theoretical background is described in the referenced literature.

In this particular study however, we pursue a twofold goal. 

A) we investigate how software intensive robotics experiments can be deployed and executed 
automatically (the software part) by any interested researcher. Why?

> The insufficient level of reproducibility of published experimental results has been identified 
as a core issue in the field of robotics in recent years. Why is that? First of
all, robotics focuses on the abstract concept of computation and the creation of 
technological artifacts, i.e., software that implements these concepts. Hence, before 
actually reproducing an experiment, the subject of investigation must be artificially
created, which is non-trivial given the inherent complexity. Second, robotics experiments 
usually include expensive and often customized hardware setups (robots), that are difficult
to operate for non-experts. Finally, there is no agreed upon set of methods in order to setup, 
execute, or (re-)conduct an experiment. To this end, we introduce an interdisciplinary and geographically
distributed collaboration project that aims at implementing good experimental methodology in 
interdisciplinary robotics research with respect to: a) reproducibility of required technical
artifacts, b) explicit and comprehensible experiment design, c) repeatable/reproducible experiment 
execution, and d) reproducible evaluation of obtained experiment data. The ultimate goal of this 
collaboration is to reproduce the same experiment in two different laboratories using the same 
systematic approach. [3]

B) We investigate if we can observe a JSE using a physiologically different robot (from Stenzel's) 
without explicit belief manipulation of the subjects (cf. [2] "biologically inspired" vs. "machine like" robot).

## Hardware Requirements and Prerequisites

Any recent standard NAO robot platform (acquired within the last 2-3 years) should suffice. 
The software interface to the robot is downward compatible to NAOQi 1.x.x in this experiment.

Furthermore, a recent desktop PC or laptop is required. The PC/laptop initially requires an
internet connection for our automated software installation, running the experiment does not require internet access.

At least two cpu cores, and 2 GB Ram (4 GB recommended) should suffice. Additionally, 
it is required that the robot and the laptop/PC are connected to the same network (subnet). 

We also strongly recommend a wired network connection for both, the robot 
and the laptop. If there is absolutely no chance of establishing a wired connection, 
WiFi will also work. However, since WiFi usually introduces a higher 
latency and instability than a wired connection, we disadvise using it.

In order to check if the laptop/PC 'can talk to' the robot, simply plug a network cable
into the NAO (back of the head, open the service hatch first). Startup the laptop connected to
the robot. As soon as the laptop is up and running, press the robot's chest button (single long press) 
to initiate the boot sequence. This will take a few minutes. When the NAO is ready (startup jingle is over) 
press the chest button of the robot again (single short press). The robot will tell you
its IP address. On the laptop/PC you can now ping the robot's IP, check if you get a "pong". 
It is also recommended to leave the power cord of the robot plugged during the experiment.
This is however not mandatory.

<pre>
ping ROBOT_IP
</pre>

<img align="left" hspace="20" src="https://github.com/CentralLabFacilities/CentralLabFacilities.github.io/blob/master/images/pepper_cable.jpg" width=300px>
<img src="https://github.com/CentralLabFacilities/CentralLabFacilities.github.io/blob/master/images/pepper_power.jpg" width=300px>

The monitors used to present the stimuli to the robot and the subject (at Bielefeld University) was a 24-inch 
Dell U2412M with an 16:10 IPS panel and a resolution of 1920:1200 @ 60 Hz. The refresh rate of this type is 
8ms (gray to gray). The brightness was set to 100% (for this display type: 300 cd/m²). 

The exact physical setup will be described later. These are just the requirements.

## Software Requirements and Prerequisites

In general, the software for this experiment has been designed and tested on Ubuntu Linux (16.04). 
**That's a prerequisite**. In case you a not familiar with *nix operating systems, it might be helpful to ask 
someone in your lab who is experienced to help you setting up the infrastructure. If you have basic knowledge 
about *nix operating systems, the next steps will be a "piece of cake".
 
Download [Ubuntu 16.04]: https://www.ubuntu.com/download/desktop/contribute?version=16.04.3&architecture=amd64 
and install it on the laptop/PC. If you already have a machine with Ubuntu 16.04 installed that meets the requirements, 
check if you have sudo permissions, you will need them in the following steps.

Before proceeding, please execute this in a terminal in order to get your system up to date

<pre>
sudo apt update && sudo apt upgrade
</pre>

Enable additional repositories.

<pre>
sudo apt-get install software-properties-common
sudo apt-add-repository universe
sudo apt-get update
sudo apt upgrade
</pre>

When the update process is finished, please proceed. Note: since almost everything in this experiment
is done in a web browser (e.g., experiment execution and orchestration) please use the
**Firefox** web browser that is shipped with Ubuntu --- we verified every feature works with Firefox.

# Deploying the Software Infrastructure for the Experiment

## Bootstrapping the CITK

We are using the CITK [4] to bootstrap your software experiment environment, please follow https://toolkit.cit-ec.uni-bielefeld.de/tutorials/bootstrapping
tutorial (you might need to accept a SSL certificate when accessing the referenced web site, the browser might warn you).

**Stop** after "Step #3: Expected Result", do **not** proceed to the next tutorial mentioned on the web site
(Installing a Distribution). In general, you will need to execute the steps in the grey boxes in a terminal.

After you've finished the above tutorial you should see something similar to the picture below when accessing your
localhost https://localhost:8080/?auto_refresh=true

Please login (top right corner) using the credentials you chose when executing the "./create_user" step.
**Don't** close the terminal in which your Jenkins is running. You are all set for now.

![empty_jenkins](https://toolkit.cit-ec.uni-bielefeld.de/sites/toolkit.cit-ec.uni-bielefeld.de/files/tutorial_jenkins_new.jpg)

## Generate Distribution and Deploy

We are now going to the install all required software components for you. This includes the software
that is required in order to control the robot, as well as experiment execution and data acquisition. Yay!

Please open a new terminal and execute the following steps.

<pre>
mkdir -p $HOME/citk/dist && cd $HOME/citk/dist
git clone https://opensource.cit-ec.de/git/citk .
</pre>

In the next step please substitute "{YOUR_USERNAME}" and {YOUR_PASSWORD} with the credentials in
the "./create_user" (see section above) step and execute the command line below.

<pre>
$HOME/citk/jenkins/job-configurator --on-error=continue -d $HOME/citk/dist/distributions/remotelab-nightly.distribution -m toolkit -D toolkit.volume=$HOME/citk/systems -u {YOUR_USERNAME} -a {YOUR_PASSWORD}
</pre>

Now, there are two things to check:

A) You should see the following at the end of the console output

<pre>
START ENABLE-JOBS
  0.00 % ENABLE
100.00 % ENABLE
END   ENABLE-JOBS, 0.001 seconds

START LIST-CREDENTIALS
END   LIST-CREDENTIALS, 0.000 seconds

100.00 % JOBS
</pre>

B) **IMPORTANT** Please also check the console output for the following (you might need to scroll up a little). 
If you don't see "missing platform dependency" you are all set.

<pre>
  10 missing platform dependencies:
    python-requests python-sphinx wmctrl libssl-dev libffi-dev libzmq-dev libxml2-dev libxslt1-dev zlib1g-dev python-tk
</pre>

If missing dependencies are reported you **need to** install them using:

<pre>
sudo apt-get install [list of reported packages]
E.g.: sudo apt-get install python-requests python-sphinx wmctrl libssl-dev libffi-dev libzmq-dev libxml2-dev libxslt1-dev zlib1g-dev python-tk
</pre>

Hint: You can safely ignore other warnings (other mentioned problems). 
In the unlikely case something is wrong in general --- please contact us.

Now, go back to your browser: https://localhost:8080/?auto_refresh=true  You should see something similar to this:
 
![jenkins_done](https://github.com/CentralLabFacilities/CentralLabFacilities.github.io/blob/master/images/remote_lab_jobs.png)

In order to deploy (install) the entire software system, the **only** thing you need to do is to click the "stopwatch" icon
next to the build job "remotelab-nightly-toolkit-orchestration".

The Jenkins will guide you to a next page that displays a dialog "ageLimit ..." **Just press the blue build button**.
In order to get back to the overview page, simply click the top left Jenkins icon.


![jenkins_trigger](https://github.com/CentralLabFacilities/CentralLabFacilities.github.io/blob/master/images/trigger_job.png)

Our toolchain will now install all required software components for you **automagically**. When it's done
(this can take up to 10 minutes) all, except for two, jobs in your Jenkins instance should turn from grey
(haven't been built yet) to blue (successfully installed). You only need to install the system **once**.

There will be  **TWO** "grey" jobs: "runnable-remotelab-nao-physical-demo-master-runnable-toolkit-remotelab-nightly" and
"runnable-remotelab-jsp-nao-calibration-master-runnable-toolkit-remotelab-nightly" (the latter is missing in the image below,
don't worry...).

These jobs will be used later on to actually calibrate the robot and **RUN** your experiment.

How cool is that?

Leave this for now, we will now setup the physical parts.

![jenkins_trigger](https://github.com/CentralLabFacilities/CentralLabFacilities.github.io/blob/master/images/remote-lab-done.png)

# Physical Experiment Setup

Because two NAOS were available at Bielefeld, a symmetrical setup was installed in an otherwise empty office.
The two monitors were connected to the same workstation. The displays were set to mirror mode, showing the same image.

Because having two NAOs is not given in every laboratory, we will describe a setup using just **one** NAO in the following.
The setup is easily adjustable by moving the robot from one side to another depending on the random position
condition which is assigned automatically by our software.

<img align="left" hspace="20" src="https://github.com/CentralLabFacilities/CentralLabFacilities.github.io/blob/master/images/pepper_setup_table.jpg" width=300px>
<img src="https://github.com/CentralLabFacilities/CentralLabFacilities.github.io/blob/master/images/pepper_press.jpg" width=300px>

The viewing distance is taken from the original Stenzel paper (approx. 80cm). The NAO kneels (predefined resting posture by
the manufacturer) next to the participants on a table. The barycenter of the robot is approximately at elbow height of a
sitting participant.

The participant and the robot each have their own keyboard of identical type. The keyboards are directly adjacent (touching)
and on the _same level_. The posture of the robot's hand above the keyboard is predefined and will be individually set up
using a calibration program that will be introduced later. Calibration is necessary because there might be little
differences in positioning at the institutions and the angles of the NAO's motors might deviate from robot to robot.
The robot's head is turned towards the screen to indicate that the robot is looking at it.

# Calibration procedure

**During calibration, the robot's stiffness needs to be released. If the motors are stiff, release stiffness with
two short chest button presses.**

It is assumed that the robot is powered on and already connected to the network as described in "Hardware Requirements
and Prerequisites".

Assuming you set up the experiment as described above you should check the robot's IP one last time. You can do this
by pressing the chest button. Remember the IP address, e.g., 192.168.1.30. You will need it in the following steps. 

During the calibration procedure the arm postures for the key press movement (left and right side), obviously needed in
the experiment, will be recorded.

If you have to move the robot in general (because, e.g., it is used by others) you should mark the exact foot
position on the table with e.g. tape. Additionally, you have to set the robot to a stable hip-roll position.
Remember the approximate angle. You can, e.g., align the back of the Nao with a book you put on the table for setup purposes.

In the calibration procedure there are four postures recorded : _prepose_, _safepose_, _keyrelease_ and _keypress_.
The posture recording is triggered via a so called _job_ (in the Jenkins) that is explained in the following.

Now, switch to the Jenkins in your browser https://localhost:8080/?auto_refresh=true

Log in and trigger the job "runnable-remotelab-jsp-nao-calibration-master-runnable-toolkit-remotelab-nightly" (stopwatch icon)
, follow the instructions on screen.

You will be asked for whether you want to record postures for the left or right
arm and then to move the easily moveable arms (thus not stiffened) to the positions as depicted in the program.

Please execute the calibration for the left and right arm (trigger the job two times, 1st time for left,
second time for right arm).

Great, you are done! Now you can run the experiment.

# Executing the Experiment

It is assumed that you either just calibrated the robot or (if you continue the experiment on another day)
set up the Nao exactly like the last time you calibrated.

Switch to the Jenkins in your browser (you need to be logged in) https://localhost:8080/?auto_refresh=true.
Trigger the job "runnable-remotelab-nao-physical-demo-master-runnable-toolkit-remotelab-nightly" (stopwatch icon), 
enter the IP and port of the robot in the small popup window and confirm the dialog.

Now, in another browser (Firefox) or a new tab enter the following address into the address bar: http://localhost:5000/.

You should see the first experiment setup slide!

You don't need to enter anything on this slide. Just click "submit answers".
On the next slide, you have to choose on which side the robot is kneeling. Choose right or left and - congratulations -
the setup is done! The robot should stiffen and move the correct arm towards the keyboard.

Now, you can get the subject and start the experiment! The subject should not be present during the setup of the robot.

In general, you don't need to re-run the "runnable-remotelab-nao-physical-demo-master-runnable-toolkit-remotelab-nightly"
job per participant. After each trial the browser window will return to its initial state (new subject).

The job will keep running until you **explicitly** stop it using the "[x]" button next to the running job (Build Queue).

When you're done for the day, you **may** shut down everything (if you want to). When you continue the next day,
you just need to setup the NAO (if it has been moved), start the Jenkins using the "./start_jenkins" script like
you have done it before and trigger the "runnable-remotelab-nao-physical-demo-master-runnable-toolkit-remotelab-nightly"

Voila, you're all set.

# Subjects

The subjects were recruited by paper sheet advertisement at the nearby campus. On the paper, it said that we were looking 
for people who wanted to participate in a co-operational study and that it takes about 45min and pays 8 EUR. 
The robot was not mentioned and not illustrated. The location of the experiment (CITEC) might (for some) indicate 
something robots-related. 

When they arrived, each participant was welcomed and led to the room were the study took place, an ordinary office with a 
symmetrical setup (two setups [NAO, Monitor, 2 keyboards]) on either side. The participants were asked to sit down and 
turn off their phones or set it to flight mode. The robot was introduced as also participating in the study but without 
further description or details. The participants were told that all the details will be displayed on-screen. 
As described in the previous step, the study was initiated by the operator during the first setup slides.

This results in the robot's motors being turned on and stiffened. The robots hand next to the participants now should move to the initial position over to the 
space bar to introduce the robot's ability to move to the participant. The participants were asked to follow the instructions on screen and 
that if they had any questions or something went wrong they could just come next door where the operator was waiting.

After the experiment, the participants received a full disclosure about what the study was really about and any further 
questions answered. 

# Results

At the end of the experiment, after the participant is done, the last slide asks you to download the experiment data as a .csv 
file with the unique id of the participant as file name.

Please store this file on your computer and regularly **backup your data**!

Soon we will offer a solution to store the data on a database we provide. Until then, it is stored locally.
If you ever forget to store the data or refresh the browser before downloading, at the beginning of the next 
experiment you can still download the file.

## Literature

- [1] https://www.ncbi.nlm.nih.gov/pubmed/22866762
- [2] http://journal.frontiersin.org/article/10.3389/fpsyg.2014.00974/full
- [3] https://pub.uni-bielefeld.de/publication/2910475
- [4] https://pub.uni-bielefeld.de/publication/2904605

# Final Nvidia Project: Weapon Detector using DetectNet

**This is the final Nvidia Project for iDTech's AI and Machine Learning Academy with NVIDIA!**
This project identifies dangerous objects, such as guns, bombs, and knives, and boxes them on a live camera feed in your browser.
The repository contains the engine file and its dependancies. It does NOT contain training data or software (see "Running This Project" below).
This was built on a *Jetson Orin Nano*. You will also need a *webcam*.

![dangerous7](https://github.com/user-attachments/assets/09e2425c-cd5f-44c1-b87e-cbb88aa1f91d)


## The Algorithm

For the very long and detailed explanation, please see dusty_nv's jetson inference repository: https://github.com/dusty-nv/jetson-inference/tree/master.

DetectNet is a deep learning model designed for object detection. It learns to identify objects within given images/video
by being trained on datasets where the desired objects are marked with bounding boxes. DetectNet learns to
extract specific details and features of the objects, which allows it to detect desired objects in new images.

In this case, DetectNet is trained on five object classes: "bomb", "handgun", "knife", "rifle", and "shotgun". All images were
pulled from Open Images Dataset: https://storage.googleapis.com/openimages/web/index.html. If you want to retrain the model, see "Running This Project" below.

## Limitations

**This program is NOT perfect!** This model was running for 480 epochs, with a training dataset of ~700 images (which took ~18 hours to train). Despite this, it may
not correctly classify objects, or fail to identify objects at all. In order to get better results,  you will need to retrain this model. See "Running This Project"
for more information.

## Requirements to Run

### **This model was built and tested on the Jetson Orin Nano (*Version 36, using Jetpack v6*)!** 

Compatibility with other devices and machines have not been tested!

^(It might run fine on other machines, but **you have been warned!**)

### **A Webcam is required to stream video.** 
The supported cameras are below:
- MIPI CSI cameras (csi://0)
- V4L2 cameras (/dev/video0)
- RTP/RTSP streams (rtsp://username:password@ip:port)
- WebRTC streams (webrtc://@:port/stream_name)

### **If you are running headless, you will need a way to connect to your Nano.** 
In my case, I simply connected my Nano to a monitor, connected my Nano to my laptop 
via the mobile hotspot feature in Windows 10/11 (and recorded the IP of the Nano), then secure shelled (SSH) into my Nano via Visual Studio Code. There is probably
a better way to do this, but this way is fairly beginner friendly.

### **For software, I recommend VSC (Visual Studio Code).** 
It is possible (and possibly easier) to code on the Nano, but Visual Studio Code will (probably) help keep you focused in the long run.

### **You will need to import repositories and containers for this project!** 
Internet connection is necessary to download everything, but running the model *does not*require internet.

## Running This Project

If you want to go through all the *painful* steps of creating a new DetectNet model from scratch, using your own dataset, look here: https://docs.google.com/document/d/1i4WsFui5fuGUALkeWdmcqqGaycF6AALmTyXp1bikalQ/edit?usp=sharing.

1. Go into Terminal.
2. Clone this repository: git clone https://github.com/samuong1/FinalNvidiaProject.git
3. Clone dusty_nv repository for Jetson-Inference: git clone --recursive https://github.com/dusty-nv/jetson-inference
4. Change Directories to Jetson-Inference: cd jetson-inference
5. Make a new folder: mkdir build
6. Change Directories to that new build: cd build
7. Make a new build: cmake ../
8. After a couple minutes, a popup for PyTorch will appear. Select the PyTorch option with Space, then use the arrow keys to hover over Ok. Press the space bar to continue.
9. Compile it all: make -j$(nproc)
10. Install the compiled executables: sudo make install
11. Let the system know where everything is: sudo ldconfig
12. Set the NET variable to the path of the model folder: NET=~/FinalNvidiaProject

**From here, you have the freedom to decide what your inputs and outputs are.**

## Displaying, Inputting, and Outputting

### *The basic command:*

detectnet   --model=$NET/ssd-mobilenet.onnx   --labels=$NET/labels.txt   --input-blob=input_0   --output-cvg=scores   --output-bbox=boxes [input here] [output here]

### *For videos, put the path to your video.*

Example:
detectnet   --model=$NET/ssd-mobilenet.onnx   --labels=$NET/labels.txt   --input-blob=input_0   --output-cvg=scores   --output-bbox=boxes ~/Downloads/thevideoisubmitted.mp4 theoutputtedvideo.mp4

### *For live recognition, it becomes a bit more complicated:*

For the more in depth tutorial, see dusty_nv's documentation (or the more complicated Google Doc in "Running This Project".

You will need to use HTTPS and SSL/TLS for livestreams. You will therefore need an SSL_KEY (key) and a SSL_CERT (certificate) to run HTTPS.

Run the following commands:
- cd /jetson-inference/data
- openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -sha256 -days 365 -nodes -subj '/CN=localhost'
- export SSL_KEY=~/key.pem
- export SSL_CERT=~/cert.pem

    ^ Note that if you are planning on retraining the model, or training a new model, you should keep the key and certificate in ~/jetson-inference/data. See documentation 			above for reasons.
  
    ^ Alternate Key location for retraining: export SSL_KEY=~/jetson-inference/data/key.pem
  
    ^ Alternate Certificate location for retraining: export SSL_CERT=~/jetson-inference/data/cert.pem

You are ready for live recognition! Run the following:

detectnet   --model=$NET/ssd-mobilenet.onnx   --labels=$NET/labels.txt   --input-blob=input_0   --output-cvg=scores   --output-bbox=boxes /dev/video0 webrtc://@:8554/output

^ /dev/video0 is for V4L2 cameras, such as the Logitech C270 camera. If you have a different camera, change /dev/video0 to your supported camera (see: https://github.com/dusty-nv/jetson-inference/blob/master/docs/aux-streaming.md).
WebRTC may take a bit to initialize and load on the first couple tries. Keep trying and it will eventually pop up.

### *You can even mix live recognition with plain videos!*

Example:
detectnet   --model=$NET/ssd-mobilenet.onnx   --labels=$NET/labels.txt   --input-blob=input_0   --output-cvg=scores   --output-bbox=boxes /dev/video0 theoutputtedvideo.mp4

^ This code records and detects, and outputs as a .mp4 file.

### **To end the livestream or video, go to the console and press Ctrl + C.**

[View a video explanation here](video link) VIDEO WILL GO HERE LATER, THIS IS A WIP GIVE ME A BREAK PLEASE D:

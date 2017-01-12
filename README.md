# pi-home-surveillance
Motion Detection Surveillance System Implementing OpenCV3 (Motion 2.0)
Table of Contents

Part 1 User manual for operating the surveillance system               
Step #1 Setting up								              
Step #2 Run the Home Surveillance System & Save the Files locally                        
Step #3 Run the Home Surveillance System & Save the Files in Dropbox                 
Step #4 Run the Home Surveillance System & Save the Files in Local Shared Folder in the Same Network							                          5
PART 2:  Installation Manual for Pi Surveillance System	
Section#1 Installation Manual for OpenCV3					              
Section#2 Installation for Camera Module 					             
Section#3 Installation for Home Surveillance					 
Appendix for pi_surveillance.py				 			  


PART 1:  User Manual for Operating the Surveillance System
Step #1 Setting up
Once you insert the microSD with the home surveillance system installed. After it booted up.
Run the following code to open up the virtual environment cv
$source ~/.file
$workon cv
In the microSD I setted up, under /home/pi/
Below is the directory that is necessary for the project
|--- picamerafolder
|--- pi-home-surveillance
|    |--- pi_surveillance.py
|    |--- conf.json
|    |--- pyimagesearch
Step #2 running the home surveillance system & saving the files locally
we need to modify the conf.json file
$cd pi-home-surveillance
$sudo nano conf.json
Set the “use_dropbox” from true to false as below
"use_dropbox": false,
Execute the following code:
$ cd pi-home-surveillance/
$ python pi_surveillance.py --conf conf.json
We should see following lines to remind the activation of the camera system
[INFO] warming up...
[INFO] starting background model...
 
This is the live stream, and it indicated that the room status is “unoccupied”
Then I tried to grab the coffee
In the terminal, information like “[localsave] Tuesday 10 January 2017 07:38:54pm” appears.
After I pressed “q” to break from the livestream, there is one extra image capture in the picamerafolder I set up as the “file_base_path” in conf.json file.
 
I use feh to open up the image, and the image shows the room status is “occupied”
 

Step #3 Run the Home Surveillance System & Save the Files in Dropbox
This method is something else. As long as the camera is running and connected to the internet , it sends images that it captures to dropbox online through Dropbox API. Users can access to the files anywhere with internet. 
we need to modify the conf.json file
$cd pi-home-surveillance
$sudo nano conf.json
Set the “use_dropbox” to true as below
"use_dropbox": true,
Execute the following code:
$ cd pi-home-surveillance/
$ python pi_surveillance.py --conf conf.json
Following Info should appear on your terminal. (B.S. I did the following by connecting the pi with a monitor and a keyboard, since for some reason I could not access to live stream by ssh)
[INFO] Authorize this application: https://www.dropbox.com/1/oauth2/authorize?response_type=code&client_id=0sxthsipdcukhjh
Enter auth code here:
Copy the website address into a browser, and click giving permission
 
The Dropbox should automatically generate a code for you to have access to the dropbox.
 
I choose to type all those letter onto the RaspberryPi terminal and hit enter
The following information showed up, which means the system is working:
[SUCCESS] dropbox account linked
[INFO] warming up...
[INFO] starting background model...
In order to test it, I placed three different cups during the process
And following information appeared:
[UPLOAD] Tuesday 10 January 2017 08:05:24pm
[UPLOAD] Tuesday 10 January 2017 08:05:34pm
[UPLOAD] Tuesday 10 January 2017 08:05:58pm
I installed the dropbox app on my phone, and it gives me notifications each time something uploaded to my dropbox.
 

 
The “resolution” value could be changed from [640, 480] to [1024, 768]to increase image quality

Step #4 Run the Home Surveillance System & Save the Files in Local Shared Folder in the Same Network
To share network folders to a Windows computer we need to install some special software on the Raspberry Pi. The software providing the secret sauce this time is called Samba. The Samba software package implements the SMB protocol and provides support for the Windows naming service (WINS) and for joining a Windows Workgroup.
Installing the software is easy – login to your Raspberry Pi and run:                                                        $ sudo apt-get install samba samba-common-bin                                                                               After installation configure the software by opening the file/etc/samba/smb.conf using the command: etc folder is in the same level as home folder. Remember to cd ../.. before executing the below if you are in pi directory                                                                                                                                    $ sudo nano /etc/samba/smb.conf                                                                                                               Read through the file and make sure you have the following parameters set:                                     workgroup = WORKGROUP                                                                                                                      wins support = yes                                                                                                                                       Next step is to create the folder you would like to share. To create a folder called “share” in your pi directory do the following:                                                                                                                     $mkdir ~/share                                                                                                                                           With the folder created we can now tell the Samba software to share it on the network. Open the file /etc/samba/smb.conf using the command:                                                                                            $sudo nano /etc/samba/smb.conf                                                                                                             Scroll to the bottom and add the following: Notice how we tell Samba that public access is not allowed via “public=no” – this means that anyone wanting to access the shared folder must login with a valid user.                                                                 In this case the valid user is the user called “pi”. To let Samba know that “pi” is a network user run the command:                                                                                                                                                  $sudo smbpasswd –a pi                                                                                                                               And enter pi’s password twice (default: raspberry).                                                                                            I have a mac system. From Finder, I was able to connect to raspberrypi through “Connect as..” on the top right corner.                                                                                                                                           After I entered the user and password for pi, I was able to access to all the files in pi directory
 
Just like step #2, we need to modify the conf.json file
$cd pi-home-surveillance
$sudo nano conf.json
Set the “use_dropbox” from true to false as below
"use_dropbox": false,
and new file_base_path: 
“file_base_path” :”/home/pi/share”,
Execute the following code:
$ cd pi-home-surveillance/
$ python pi_surveillance.py --conf conf.json
Similar information appeared:
[INFO] warming up...
[INFO] starting background model...
[localsave] Tuesday 10 January 2017 09:03:23pm
As you can see, I was able to access to the pi/share folder for the image. However, the image name changed during the sharing.


PART 2:  Installation Manual for Pi Surveillance System
1.	Installation Manual for OpenCV3
Step #1 Expand the fileystem 
Expand the filesystem to include all available space on your micro-SD card:
$sudo raspi-config
Once prompted, you should select the first option “1.Expand File System” and hit enter to ensure that all of the SD card storage is available to the Operating System, and then reboot your pi:
$ sudo reboot
After rebooting, your system should have been expanded to include all available space. You can check it by 
$df –h 
Step#2 Install dependencies
The first step is to update and upgrade any existing packages:
$sudo apt-get update
$sudo apt-get upgrade
Timing: 1 min 26s
Next, we need to install some developer tools, including CMake, which helps us configure the OpenCV build process:
$sudo apt-get install build-essential cmake pkg-config
Timing: 40 s 
Next, we need to install some image I/O packages that allow us to load various image file formats from disk. Examples of such file formats include JPEG, PNG, TIFF, etc.:
$sudo apt-get install libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev
Timing: 32s
Just as we need image I/O packages, we also need video I/O packages. These libraries allow us to read various video file formats from disk as well as work directly with video streams:
$ sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
$ sudo apt-get install libxvidcore-dev libx264-dev
Timing: 34s
The OpenCV library comes with a sub-module named highgui  which is used to display images to our screen and build basic GUIs. In order to compile the highgui  module, we need to install the GTK development library:
$sudo apt-get install libgtk2.0-dev
Timing: 3m6s
Many operations inside of OpenCV (namely matrix operations) can be optimized further by installing a few extra dependencies:
$sudo apt-get install libatlas-base-dev gfortran
Timing: 46s
Lastly, let’s install both the Python 2.7 and Python 3 header files so we can compile OpenCV with Python bindings:
$sudo apt-get install python2.7-dev python3-dev
Timing: 45s
Step #3: Download the OpenCV source code
Now that we have all the dependencies install, let us grab the 3.1.0 archive of OpenCV from the official OpenCV repository.
$cd ~
$wget –O opencv.zip https://github.com/Itseez/opencv/archive/3.1.0.zip
$unzip opencv.zip
Timing: 1 m 26s
We will also want the full install of OpenCV 3 to have access to all the features , so we also need to grab the opencv_contrib repository as well:
$wget –O opencv_contrib.zip https://github.com/Itseez/opencv_contrib/archive/3.1.0.zip
$unzip opencv_contrib.zip
Timing: 43s
Step #4 Python 2.7 or Python 3
Before we can start compiling OpenCV on our Raspberry Pi 3, we first need to install pip, a Python package manager:
$wget https://boostrap.pypa.io/get-pip.py
$sudo python get-pip.py
A virtual environment is a special tool used to keep the dependencies required by different projects in separate places by creating isolated, independent Python environments for each of them.
Let us install virtualenv and virtualenvwrapper:
$sudo pip install virtualenv virtualenvwrapper
$sudo rm –rf ~/.cache/pip
Timing : 9s
Now that both virtualenv  and virtualenvwrapper  have been installed, we need to update our ~/.profile  file to include the following lines at the bottom of the file:
Use the following to access the file 
$sudo nano ~/.profile
and add the following lines:
#virtualenv and virtualenvwrapper
export WORKON_HOME=$HOME/.virtualenvs
source /usr/local/bin/virtualenvwrapper.sh
Try using $sudo reboot and then $source ~/.profile
I recommend running the source ~/.profile  file each time you open up a new terminal to ensure your system variables have been setup correctly.
Next, let’s create the Python virtual environment that we’ll use for computer vision development:
This command will create a new Python virtual environment named cv using Python 2.7.
$mkvirtualenv cv –p python2
How to check if you are in the “cv” virtual environment 
If you ever reboot your Raspberry Pi; log out and log back in; or open up a new terminal, you’ll need to use the workon command to re-access the cv  virtual environment.
$source ~/.profile
$workon cv
 
Installing NumPy on your Raspberry Pi
$pip install numpy
Timing: 9m 39s
Step #5: Compile and Install OpenCV
We are now ready to compile and install OpenCV! Double-check that you are in the cv virtual environment. Once you have ensured you are in the cv virtual environment, we can setup our build using CMake:
$cd ~/opencv-3.1.0/
$ mkdir build
$ cd build
$ cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D INSTALL_PYTHON_EXAMPLES=ON \
    -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib-3.1.0/modules \
    -D BUILD_EXAMPLES=ON ..
Timing: 1m 57s
Now, before we move on to the actual compilation step, make sure you examine the output of CMake! Start by scrolling down the section titled Python 2 and Python 3. If you are compiling OpenCV 3 for Python 2.7, then make sure your Python 2  section includes valid paths to the Interpreter , Libraries , numpy  and packages path , similar to my screenshot below:
 
Finally, we are now ready to compile OpenCV:
$make –j4
Timing: 1h 12m
If there is error occurred, simply execute $make clean and $make again
 
From there, all you need to do is install OpenCV 3 on your Raspberry Pi 3:
$sudo make install
$sudo ldconfig
Timing: 52s
Step #6: Finish installing OpenCV on your Pi
Provided your Step #5 finished without error, OpenCV should now be installed in/usr/local/lib/python2.7/site-pacakges . You can verify this using the ls  command:
$ ls -l /usr/local/lib/python2.7/site-packages/
total 1852
-rw-r--r-- 1 root staff 1895772 Mar 20 20:00 cv2.so
Our final step is to sym-link the OpenCV bindings into our cv virtual environment for Python 2.7:
$ cd ~/.virtualenvs/cv/lib/python2.7/site-packages/
$ ln -s /usr/local/lib/python2.7/site-packages/cv2.so cv2.so
Step #7 Testing your OpenCV 3 install 
Open up a new terminal, execute the source  and workon  commands, and then finally attempt to import the Python + OpenCV bindings:
$ source ~/.profile 
$ workon cv
$ python
>>> import cv2
>>> cv2.__version__
'3.1.0'
>>>
 
Above are installation manual for OpenCV3. Reference: http://www.pyimagesearch.com/2016/04/18/install-guide-raspberry-pi-3-raspbian-jessie-opencv-3/


2.	Installation for Camera Module 
Step#1 Enable your camera module
First, we should have our Raspberry Pi camera installed.  Open up a terminal and execute the following command:
$ sudo raspi-config
Use your arrow keys to scroll down to Option 5: Enable camera, hit your enter key to enable the camera, and then arrow down to the Finish button and hit enter again. Lastly, you’ll need to reboot your Raspberry Pi for the configuration to take affect.
Step #2 Test out the camera module
$raspistill -o output.jpg
 
This command activates your Raspberry Pi camera module, displays a preview of the image, and then after a few seconds, snaps a picture, and saves it to your current working directory as output.jpg .  
Clearly Raspberry Pi camera module is working correctly! 
Step #3: Installing PiCamera
Before installing picamera , be sure to activate our cv  virtual environment:
$ source ~/.profile
$ workon cv
From there, we can install picamera by utilizing pip:
$ pip install "picamera[array]"
Step #4: Accessing a single image of Raspberry Pi using Python and OpenCV
Open up a new file, name it image_testcam.py , and insert the following code:
# import the necessary packages
from picamera.array import PiRGBArray
from picamera import PiCamera
import time
import cv2
 
# initialize the camera and grab a reference to the raw camera capture
camera = PiCamera()
rawCapture = PiRGBArray(camera)
 
# allow the camera to warmup
time.sleep(0.1)
 
# grab an image from the camera
camera.capture(rawCapture, format="bgr")
image = rawCapture.array
 
# display the image on screen and wait for a keypress
cv2.imshow("Image", image)
cv2.waitKey(0)
I have attached the code name: image_testcam.py
From there, we initialize our PiCamera object on Line 8 and grab a reference to the raw capture component on Line 9. This rawCapture  object is especially useful since it (1) gives us direct access to the camera stream and (2) avoids the expensive compression to JPEG format, which we would then have to take and decode to OpenCV format anyway.
To execute this example, open up a terminal, navigate to cv environment, and issue the following command:
$python image_testcam.py 
 
Step #5: Accessing the Video Stream of Raspberry Pi using OpenCV and Python
Let’s go ahead and take a look on how we can access the video stream. Open up a new file, name it test_pivideo.py , and insert the following code:
# import the necessary packages
from picamera.array import PiRGBArray
from picamera import PiCamera
import time
import cv2
 
# initialize the camera and grab a reference to the raw camera capture
camera = PiCamera()
camera.resolution = (640, 480)
camera.framerate = 32
rawCapture = PiRGBArray(camera, size=(640, 480))
 
# allow the camera to warmup
time.sleep(0.1)
 
# capture frames from the camera
for frame in camera.capture_continuous(rawCapture, format="bgr", use_video_port=True):
	# grab the raw NumPy array representing the image, then initialize the timestamp
	# and occupied/unoccupied text
	image = frame.array
 
	# show the frame
	cv2.imshow("Frame", image)
	key = cv2.waitKey(1) & 0xFF
 
	# clear the stream in preparation for the next frame
	rawCapture.truncate(0)
 
	# if the `q` key was pressed, break from the loop
	if key == ord("q"):
		break
I have attached the code. To execute our script, just open a terminal (making sure you are in the cv virtual environment, of course) and issue the following command:
$ python test_pivideo.py
You can press “q” to break from the loop.
Above are some tests for pi camera module. Reference: http://www.pyimagesearch.com/2015/03/30/accessing-the-raspberry-pi-camera-with-opencv-and-python/






3.	Installation for Home Surveillance
Step #1 Install Necessary Packages:
Make sure you are in cv environment, and execute the following command
$pip install imutils
$pip install dropbox 
        Step #2 Install the pi-home-surveillance package
I have attached the package code.  
 
Use scp to transfer files, example
$ scp weizhong@weizhongdeMBP:\Desktop/camera_pic_setup.py /home/pi
Let us see the directory structure of our project:
|--- pi_surveillance.py
|--- conf.json
|--- pyimagesearch
|    |--- __init__.py
|    |--- tempimage.py
Step#3 Setting up Dropbox API
Since our motion detection system has the implementation of using dropbox to store images raspberry pi captures, we have to also set up dropbox.
Once you have a dropbox account. Go to https://www.dropbox.com/developers
 
Click the Create your app, select Dropbox API, choose the type of access we need App folder
It gives raspberry pi access to a single folder created specifically for your app.
Name my app PiCameraFolderhuang
Now if you go to My apps on the left corner
 
Click the folder, it shows up all the information about the folder.
Please copy those two information. (It is used for json file)
App key 0sxthsipdcukhjh
App secret w7gcv0264adj9n6

If you go to https://www.dropbox.com/home  The normal setting of the dropbox. You can see this folder created.
 

Step #4 JSON configuration file
Let us take a look at conf.json
{
	"show_video": true,
	"use_dropbox": true,
	"dropbox_key": "0sxthsipdcukhjh",
	"dropbox_secret": "w7gcv0264adj9n6",
	"dropbox_base_path": "YOUR_DROPBOX_APP_PATH",
	"file_base_path":"/home/pi/picamerafolder",
	"min_upload_seconds": 3.0,
	"min_motion_frames": 8,
	"camera_warmup_time": 2.5,
	"delta_thresh": 5,
	"resolution": [640, 480],
	"fps": 16,
	"min_area": 5000
}
As you can see, we replace the “dropbox_key”, “dropbox_secret” with the value you have in dropbox setting. There are 13 values that could be changed
show_video : A boolean indicating whether or not the video stream from the Raspberry Pi should be displayed to our screen.
use_dropbox : Boolean indicating whether or not the Dropbox API integration should be used
dropbox_key : Your public Dropbox API key.
dropbox_secret : Your private Dropbox API key.
dropbox_base_path : The name of your Dropbox App directory that will store uploaded images
file_base_path : The name of your local directory that will store uploaded images
min_upload_seconds : The number of seconds to wait in between uploads. For example, if an image was uploaded to Dropbox 5m 33s after starting our script, a second image would not be uploaded until 5m 36s. This parameter simply controls the frequency of image uploads.
min_motion_frames : The minimum number of consecutive frames containing motion before an image can be uploaded to Dropbox.
camera_warmup_time : The number of seconds to allow the Raspberry Pi camera module to “warmup” and calibrate.
delta_thresh : The minimum absolute value difference between our current frame and averaged frame for a given pixel to be “triggered” as motion. Smaller values will lead to more motion being detected, larger values to less motion detected
resolution : The width and height of the video frame from our Raspberry Pi camera.
fps : The desired Frames Per Second from our Raspberry Pi camera.
min_area : The minimum area size of an image (in pixels) for a region to be considered motion or not. Smaller values will lead to more areas marked as motion, whereas higher values of min_area  will only mark larger regions as motion
Step #5 several changes on the main python code
If you are using online original code, you will have some errors like
cv2.CHAIN_APPROX_SIMPLE)
ValueError: too many values to unpack
It is because you are using OpenCV 3, I fixed the bug by changing
#(cnts, _) = cv2.findContours(thresh.copy(), cv2.RETR_EXTERNAL,
#	cv2.CHAIN_APPROX_SIMPLE)
to
(_, cnts, _) = cv2.findContours(thresh.copy(), cv2.RETR_EXTERNAL,
                cv2.CHAIN_APPROX_SIMPLE)
I have also implemented the functionality to save files locally.
After the section below, I added an else condition:
if motionCounter >= conf["min_motion_frames"]:
	# check to see if dropbox sohuld be used
	if conf["use_dropbox"]:
		# write the image to temporary file
		t = TempImage()
		cv2.imwrite(t.path, frame)
# upload the image to Dropbox and cleanup the tempory image
		print "[UPLOAD] {}".format(ts)
		path = "{base_path}/{timestamp}.jpg".format(
		base_path=conf["dropbox_base_path"], timestamp=ts)
		client.put_file(path, open(t.path, "rb"))
		t.cleanup()
	else:
		print "[LOCALSAVE] {}".format(ts)
		path = "{base_path}/{timestamp}.jpg".format(
		base_path=conf["file_base_path"],timestamp=ts)
                       cv2.imwrite(path, frame)	
		# update the last uploaded timestamp and reset the motion
		# counter
	lastUploaded = timestamp
	motionCounter = 0
Besides, I add the “file_base_path” in the conf.json file
Above are all the installation instructions for this motion home surveillance system. Please go to Use Manual for how to operate the system




















Appendix for pi_surveillance.py
# USAGE
# python pi_surveillance.py --conf conf.json

# import the necessary packages
from pyimagesearch.tempimage import TempImage
from dropbox.client import DropboxOAuth2FlowNoRedirect
from dropbox.client import DropboxClient
from picamera.array import PiRGBArray
from picamera import PiCamera
import argparse
import warnings
import datetime
import imutils
import json
import time
import cv2

# construct the argument parser and parse the arguments
ap = argparse.ArgumentParser()
ap.add_argument("-c", "--conf", required=True,
	help="path to the JSON configuration file")
args = vars(ap.parse_args())

# filter warnings, load the configuration and initialize the Dropbox client
# filters warning notifications from Python, specifically ones generated from urllib3  and the dropbox  packages
warnings.filterwarnings("ignore")
conf = json.load(open(args["conf"]))
client = None

# check to see if the Dropbox should be used
if conf["use_dropbox"]:
	# connect to dropbox and start the session authorization process
	flow = DropboxOAuth2FlowNoRedirect(conf["dropbox_key"], conf["dropbox_secret"])
	print "[INFO] Authorize this application: {}".format(flow.start())
	authCode = raw_input("Enter auth code here: ").strip()

	# finish the authorization and grab the Dropbox client
	(accessToken, userID) = flow.finish(authCode)
	client = DropboxClient(accessToken)
	print "[SUCCESS] dropbox account linked"

# initialize the camera and grab a reference to the raw camera capture
camera = PiCamera()
camera.resolution = tuple(conf["resolution"])
camera.framerate = conf["fps"]
rawCapture = PiRGBArray(camera, size=tuple(conf["resolution"]))

# allow the camera to warmup, then initialize the average frame, last
# uploaded timestamp, and frame motion counter
# initialize the average background frame, along with some bookkeeping variables
print "[INFO] warming up..."
time.sleep(conf["camera_warmup_time"])
avg = None
lastUploaded = datetime.datetime.now()
motionCounter = 0

# capture frames from the camera
for f in camera.capture_continuous(rawCapture, format="bgr", use_video_port=True):
	# grab the raw NumPy array representing the image and initialize
	# the timestamp and occupied/unoccupied text
	frame = f.array
	timestamp = datetime.datetime.now()
	text = "Unoccupied"

	# resize the frame, convert it to grayscale, and blur it
	frame = imutils.resize(frame, width=500)
	gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
	gray = cv2.GaussianBlur(gray, (21, 21), 0)

	# if the average frame is None, initialize it
	if avg is None:
		print "[INFO] starting background model..."
		avg = gray.copy().astype("float")
		rawCapture.truncate(0)
		continue

	# accumulate the weighted average between the current frame and
	# previous frames, then compute the difference between the current
	# frame and running average
	
	cv2.accumulateWeighted(gray, avg, 0.5)
	frameDelta = cv2.absdiff(gray, cv2.convertScaleAbs(avg))

	# threshold the delta image, dilate the thresholded image to fill
	# in holes, then find contours on thresholded image
	# We’ll then threshold the frameDelta to reveal regions of the image that 
	# only have significant changes in pixel intensity values. 
	# If the delta is less than delta_thresh, we discard the pixel and set it to black (i.e. background). 
	# If the delta is greater than delta_thresh, we’ll set it to white (i.e. foreground).
	thresh = cv2.threshold(frameDelta, conf["delta_thresh"], 255,
		cv2.THRESH_BINARY)[1]
	thresh = cv2.dilate(thresh, None, iterations=2)
	#Given this thresholded image, it’s simple to apply contour detection to to 
	#find the outlines of these white regions (Line 56).
	(_, cnts, _) = cv2.findContours(thresh.copy(), cv2.RETR_EXTERNAL,
                cv2.CHAIN_APPROX_SIMPLE)
	# Incorrect format that does not work with python 3
	#(cnts, _) = cv2.findContours(thresh.copy(), cv2.RETR_EXTERNAL,
	#	cv2.CHAIN_APPROX_SIMPLE)

	# loop over the contours
	for c in cnts:
		# if the contour is too small, ignore it
		if cv2.contourArea(c) < conf["min_area"]:
			continue

		# compute the bounding box for the contour, draw it on the frame,
		# and update the text
		# If the contour area is larger than our supplied --min-area , we’ll draw the 
		# bounding box surrounding the foreground and motion region. 
		# We’ll also update our text  status string to indicate that the room is “Occupied”.
		(x, y, w, h) = cv2.boundingRect(c)
		cv2.rectangle(frame, (x, y), (x + w, y + h), (0, 255, 0), 2)
		text = "Occupied"

	# draw the text and timestamp on the frame
	ts = timestamp.strftime("%A %d %B %Y %I:%M:%S%p")
	cv2.putText(frame, "Room Status: {}".format(text), (10, 20),
		cv2.FONT_HERSHEY_SIMPLEX, 0.5, (0, 0, 255), 2)
	cv2.putText(frame, ts, (10, frame.shape[0] - 10), cv2.FONT_HERSHEY_SIMPLEX,
		0.35, (0, 0, 255), 1)

	# check to see if the room is occupied
	if text == "Occupied":
		# check to see if enough time has passed between uploads
		if (timestamp - lastUploaded).seconds >= conf["min_upload_seconds"]:
			# increment the motion counter
			motionCounter += 1

			# check to see if the number of frames with consistent motion is
			# high enough
			if motionCounter >= conf["min_motion_frames"]:
				# check to see if dropbox sohuld be used
				if conf["use_dropbox"]:
					# write the image to temporary file
					t = TempImage()
					cv2.imwrite(t.path, frame)

				# upload the image to Dropbox and cleanup the tempory image
					print "[UPLOAD] {}".format(ts)
					path = "{base_path}/{timestamp}.jpg".format(
					base_path=conf["dropbox_base_path"], timestamp=ts)
					client.put_file(path, open(t.path, "rb"))
					t.cleanup()
				else:
					print "[LOCALSAVE] {}".format(ts)
					path = "{base_path}/{timestamp}.jpg".format(
					base_path=conf["file_base_path"],timestamp=ts)
                    				cv2.imwrite(path, frame)	
				# update the last uploaded timestamp and reset the motion
				# counter
				lastUploaded = timestamp
				motionCounter = 0

	# otherwise, the room is not occupied
	else:
		motionCounter = 0

	# check to see if the frames should be displayed to screen
	if conf["show_video"]:
		# display the security feed, along with the frame delta 
# and thresholded image so we can debug our script.
		cv2.imshow("Security Feed", frame)
		# uncomment the next line to give access to the Thresh image
		# cv2.imshow("Thresh", thresh)
		# uncomment the next line to give access to the delta image in b/w
		# cv2.imshow("Frame Delta", frameDelta)
		key = cv2.waitKey(1) & 0xFF

		# if the `q` key is pressed, break from the lop
		if key == ord("q"):
			break

	# clear the stream in preparation for the next frame
	rawCapture.truncate(0)

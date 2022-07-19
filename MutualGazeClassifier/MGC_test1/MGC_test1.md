## MGC_test #1 
The source code for this test is from [mutual-gaze-classifier-demo](https://github.com/MariaLombardi/mutual-gaze-classifier-demo) repository.

## Goal
In this test, I will try to run the application `mutual-gaze-classifier-localhost-realsense` with the `mutualgaze-classifier.py` Python code.

## Steps to follow:

1. clone the repository on your local machine. 
2. Run `yarpserver`
3. Run the Python code from terminal.
    ```
    mutualgaze-classifier.py --from /home/shanifi/code/mutual-gaze-classifier-demo/app/config/classifier_conf.ini
    ```
    ### ERROR!
    There was an error since the Python code is using SKlearn and it was not installed. pip install it to solve the problem.
    ```
    pip3 install scikit-learn
    ```

    After that you will be able to sucessfully run the Python code.

4. In order to use the ``mutual-gaze-classifier-localhost-realsense`` application, there are few changes to be applied. 
    -  The path for the YARP device parameters (RealSense camera parameters) have to be modified. Open the related application's XML file (From `mutual-gaze-classifier-demo/app/scripts`), in the `yarpdev` module's definition replace the path for the parameters with the path in which you save the RealSense camera initialization file. 
    - Add also a path for the parameters of the `yarpopenpose` module. The path where you have saved the `yarpopenpose.ini` file.
    - Add a new module for the Python file. In case you add this module, there would be no need to run the code seperately in the begining since it will automatically run the code.

        ```
        <module>
        <name>mutualgaze-classifier.py</name>
        <parameters>--from /home/shanifi/code/mutual-gaze-classifier-demo/app/config/classifier_conf.ini</parameters>
        <node>localhost</node>
        </module>
        ```
    - The port name has been changed but the XML file has not been updated. Therefore, you have to replace the port called `classifier` with `mutualgaze`.
        ### Note: To check which ports are being used by the yarp you can look aty the name list. 
        ```
        yarp name list
        ```
    - There is another important change to be made. There will always be a difference in the frame rate of the depth camera and the yarpopenpose module. To keep them synchronized, We will connect the classifier to the yarpopenpose module and not directly to the depth camera.

        To be more clear, we will use the input from depth camera only to be displayed at the `view/rgb` port and also as an input for the yarpopenpose i.e. `yarpopenpose/image:i`. Therefore, we will depthcamera with the yarpopenpose in other connections.
    ```
            <connection>
                <from>/yarpOpenPose/image:o</from>
                <to>/view/skeleton</to>
                <protocol>mjpeg</protocol>
            </connection>

            <connection>
                <from>/yarpOpenPose/propag:o</from>
                <to>/mutualgaze/image:i</to>
                <protocol>mjpeg</protocol>
            </connection>

            <connection>
                <from>/yarpOpenPose/target:o</from>
                <to>/mutualgaze/data:i</to>
            </connection>

            <connection>
                  <from>/yarpOpenPose/float:o</from>
                  <to>/mutualgaze/depth:i</to>
                  <protocol>fast_tcp</protocol>
            </connection>

    ```
4. Open the `yarpmanager` and add the XML discription of the application `mutual-gaze-classifier-localhost-realsense.xml`.
5. Run the modules, and then connect them to see the results. The classifier view is supposed to specify whether you are looking at the camera or not.
    ### ERROR
    The `mjpeg` protocol needs to be installed seperately and you will recieve connection error if you have not installed them beforehand. To solve the problem you can change the protocol  in 2 different ways. The permenant way would be to change them in the XML file, but to change them temporarily, you can just use yarpmanager and modify the carriers from the yarpmanager.

![no mutual gaze](MGC_test1/pictures/MGC1No.png)
![mutual gaze](MGC_test1/pictures/MGC1Yes.png)
### Note
The face keypoints has to be enabled in the yarpopenpose module for the classifier to work properly. (you can check it from the `yarpopenpose.ini` file.)

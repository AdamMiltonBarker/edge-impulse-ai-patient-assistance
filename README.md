
# AI Patient Assistance With Edge Impulse & Arduino Nano 33 BLE Sense
![AI Patient Assistance With Edge Impulse & Arduino Nano 33 BLE Sense](assets/images/ai-patient-assistance.jpg "AI Patient Assistance With Edge Impulse & Arduino Nano 33 BLE Sense")

Project by [Adam Milton-Barker](https://www.AdamMiltonBarker.com)

Edge Impulse Project: [AI-Powered Patient Assistance](https://docs.edgeimpulse.com/experts/prototype-and-concept-projects/ai-patient-assistance)

Github Repo: [AI Patient Assistance With Edge Impulse & Arduino Nano 33 BLE Sense](https://github.com/AdamMiltonBarker/edge-impulse-ai-patient-assistance)

# Introduction

When hospitals are busy it may not always be possible for staff to be close when help is needed, especially if the hospital is short staffed. To ensure that patients are looked after promptly, hospital staff need a way to be alerted when a patient is in discomfort or needs attention from a doctor or nurse.

# Solution

A well known field of Artificial Intelligence is voice recognition. These machine learning and deep learning models are trained to recognize phrases or keywords, and combined with the Internet of Things can create fully autonomous systems that require no human interaction to operate.

As technology has advanced, it is now possible to run voice recognition solutions on low cost, resource constrained devices. This not only reduces costs considerably, but also opens up more possibilities for innovation. The purpose of this project is to show how a machine learning model can be deployed to a low cost IoT device (Arduino Nano 33 BLE SENSE), and used to notify staff when a patient needs their help.

The device will be able to detect three keywords **Doctor**, **Nurse**, and **Help**. The device also acts as a BLE peripheral, BLE centrals/masters such as a central server for example, could connect and listen for data coming from the device. The server could then process the incoming data and send a message to hospital staff or sound an alarm.

## Hardware

- Arduino Nano 33 BLE Sense [Buy](https://store.arduino.cc/products/arduino-nano-33-ble-sense)

## Platform

-  Edge Impulse [Visit](https://www.edgeimpulse.com)

## Software

- [Edge Impulse CLI](https://docs.edgeimpulse.com/docs/edge-impulse-cli/cli-installation)
- [Arduino CLI](https://arduino.github.io/arduino-cli/latest/)
- [Arduino IDE](https://www.arduino.cc/en/software)

## Project Setup

Head over to [Edge Impulse](https://www.edgeimpulse.com) and create your account or login. Once logged in you will be taken to the project selection/creation page.

### Create New Project
Your first step is to create a new project. From the project selection/creation you can create a new project.

![Create Edge Impulse project](assets/images/new-project.jpg "Create Edge Impulse project")

Enter a **project name**, select **Developer** and click **Create new project**.

![Choose project type](assets/images/project-type.jpg "Choose project type")

We are going to be creating a voice recognition system, so now we need to select **Audio** as the project type.

### Connect Your Device

![Connect device](assets/images/create-dataset.jpg "Connect device")

You need to install the required dependencies that will allow you to connect your device to the Edge Impulse platform. This process is documented on the [Edge Impulse Website](https://docs.edgeimpulse.com/docs/development-platforms/officially-supported-mcu-targets/arduino-nano-33-ble-sense) and includes installing:

- [Edge Impulse CLI](https://docs.edgeimpulse.com/docs/edge-impulse-cli/cli-installation)
- [Arduino CLI](https://arduino.github.io/arduino-cli/latest/)

Once the dependencies are installed, connect your device to your computer and press the **RESET** button twice to enter into bootloader mode, the yellow LED should now be pulsating.

Now download the the [latest Edge Impulse firmware](https://cdn.edgeimpulse.com/firmware/arduino-nano-33-ble-sense.zip) and unzip it, then double click on the relevant script for your OS either `flash_windows.bat`, `flash_mac.command` or `flash_linux.sh`.

![Edge Impulse firmware installed](assets/images/edge-impulse-installed.jpg "Edge Impulse firmware installed")

Once the firmware has been flashed you will should see the output above, hit enter to close command prompt/terminal.

Open a new command prompt/terminal, and enter the following command:

```edge-impulse-daemon```

If you are already connected to an Edge Impulse project, use the following command:

```edge-impulse-daemon --clean```

Follow the instructions to log in to your Edge Impulse account.

![Device connected to Edge Impulse](assets/images/device-connected.jpg "Device connected to Edge Impulse")

Once complete head over to the devices tab of your project and you should see the connected device.

## Data Acquisition

We are going to create our own dataset, using the built in microphone on the Arduino Nano 33 BLE Sense. We are going to collect data that will allow us to train a machine learning model that can detect the words/phrases **Doctor**, **Nurse**, and **Help**.

We will use the **Record new data** feature on Edge Impulse to record 15 sets of 10 utterences of each of our keywords, and then we will split them into individual samples.

Ensuring your device is connected to the Edge Impulse platform, head over to the **Data Aqcquisition** tab to continue.

![Data acquisition](assets/images/data-acquisition.jpg "Data acquisition")

In the **Record new data**, make sure you have selected your Arduino Nano 33 BLE Sense, then select **Built in microphone**, set the label as **Doctor**, change the sample length to 20000 (20 seconds), and leave all the other settings as.

Here we are going to record the data for the word **Doctor**. Make sure the microphone is close to you, click **Start sampling** and record yourself saying **Doctor** ten times.

![Recorded sample](assets/images/data-captured.jpg "Recorded sample")

You will now see the uploaded data in the **Collected data** window, next we need to split the data into ten individual samples.

![Data split](assets/images/data-split.jpg "Data split")

Click on the dots to the right of the sample and click on **Split sample**, this will bring up the sample split tool. Here you can move the windows until each of your samples are safely in a window. You can fine tune the splits by dragging the windows until you are happy, then click on **Split**

![Split data](assets/images/split-data-collected.jpg "Split data")

You will see all of your samples now populated in the **Collected data** window. Now you need to repeat this action 14 more times for the **Doctor** class, resulting in 150 samples for the Doctor class. Once you have finished, repeat this for the remaining classes: **Nurse** and **Help**. You will end up with a dataset of 450 samples, 150 per class.

![Main data](assets/images/data-collected.jpg "Main data")

Now we have all of our main classes complete, but we still need a little more data. We need a **Noise** class that will help our model determine when nothing is being said, and we need an **Unknown** class, for things that our model may come up against that are not in the dataset.

For the noise class we will mix silent samples, and some other general noise samples. First of all record 100 samples with no speaking and store them in an **Noise** class.

![Data upload](assets/images/data-upload.jpg "Data upload")

Next download the [Microsoft Scalable Noisy Speech Dataset](https://github.com/microsoft/MS-SNSD) and extract the data. Navigate to the **Noise** directory and copy 50 random samples. Next go to the **Data Acquisition** tab and upload the new data into the **Noise** class. Finally copy 100 samples from the unknown class and upload to the Edge Impulse platform as an **Unknown** class.

### Split Dataset

![Split dataset](assets/images/split-data.jpg "Split dataset")

We need to split the dataset into test and training samples. To do this head to the dashboard and scroll to the bottom of the page, then click on the **Perform train/test split**

![Recorded dataset](assets/images/recorded-data.jpg "Recorded dataset")

Once you have done this, head back to the data acquisition tab and you will see that your data has been split.

## Create Impulse

![Create Impulse](assets/images/create-impulse.jpg "Create Impulse")

Now we are going to create our network and train our model.

![Add processing block](assets/images/add-processing-block.jpg "Add processing block")

Head to the **Create Impulse** tab and change the window size to 2000ms. Next click **Add processing block** and select **Audio (MFCC)**, then click **Add learning block** and select **Clasification (Keras)**.

![Created Impulse](assets/images/impulse.jpg "Created Impulse")

Now click **Save impulse**.

### MFCC Block

#### Parameters

![Parameters](assets/images/save-parameters.jpg "Parameters")

Head over to the **MFCC** tab and click on the **Save parameters** button to save the MFCC block parameters.

#### Generate Features

![Generate Features](assets/images/generate-features.jpg "Generate Features")

If you are not automatically redirected to the **Generate features** tab, click on the **MFCC** tab and then click on **Generate features** and finally click on the **Generate features** button.

![Generated Features](assets/images/generate-features-2.jpg "Generated Features")

Your data should be nicely clustered and there should be as little mixing of the classes as possible. You should inspect the clusters and look for any data that is clustered incorrectly (You don't need to worry so much about the noise and unknown classes being mixed). If you find any data out of place, you can relabel or remove it. If you make any changes click **Generate features** again.

## Training

![Training](assets/images/training.jpg "Training")

Now we are going to train our model. Click on the **NN CLassifier** tab then click **Auto-balance dataset**, **Data augmentation** and then **Start training**.

![Training complete](assets/images/training-complete.jpg "Training complete")

Once training has completed, you will see the results displayed at the bottom of the page. Here we see that we have 99.2% accuracy. Lets test our model and see how it works on our test data.

## Testing

### Platform Testing

![Test model](assets/images/test-model.jpg "Test model")

Head over to the **Model testing** tab where you will see all of the unseen test data available. Click on the **Classify all** and sit back as we test our model.

![Test model results](assets/images/test-model-results.jpg "Test model results")

You will see the output of the testing in the output window, and once testing is complete you will see the results. In our case we can see that we have achieved 96.62% accuracy on the unseen data, and a high F-Score on all classes.

### On Device Testing

![Live testing](assets/images/live-testing-doctor.jpg "Live testing")

Now we need to test how the model works on our device. Use the **Live classification** feature to record some samples for clasification. Your model should correctly identify the class for each sample.

## Performance Callibration

![Performance Callibration](assets/images/perfcal.jpg "Performance Callibration")

Edge Impulse has a great new feature called **Performance Callibration**, or **PerfCal**. This feature allows you to run a test on your model and see how well it will perform in the real world. The system will create a set of post processing configurations for you to choose from. These configurations help to minimize either false activations or false rejections

![Turn on perfcal](assets/images/perfcal-turn-on.jpg "Turn on perfcal")

Once you turn on perfcal, you will see a new tab in the menu called **Performance callibration**. Navigate to the perfcal page and you will be met with some configuration options.

![Perfcal settings](assets/images/perfcal-settings.jpg "Perfcal settings")

Select the **Noise** class from the drop down, and check the Unknown class in the list of classes below, then click **Run test** and wait for the test to complete.

![Perfcal configs](assets/images/perfcal-config.jpg "Perfcal configs")

The system will provide a number of configs for you to choose from. Choose the one that best suits your needs and click **Save selected config**. This config will be deployed to your device once you download and install the libray on your device.

## Versioning

![Versioning](assets/images/versioning.jpg "Versioning")

We can use the versioning feature to save a copy of the existing network. To do so head over to the **Versioning** tab and click on the **Create first version** button.

![Versions](assets/images/versions.jpg "Versions")

This will create a snapshot of your existing model that we can come back to at any time.

## Deployment

Now we will deploy an Arduino library to our device that will allow us to run the model directly on our Arduino Nano 33 BLE Sense.

![Build](assets/images/build.jpg "Build")

Head to the deployment tab and select **Arduino Library** then scroll to the bottom and click **Build**.

![Build optimizations](assets/images/build-optimizations.jpg "Build optimizations")

Note that the EON Compiler is selected by default which will reduce the amount of memory required for our model.

![Arduino library](assets/images/arduino-library.jpg "Arduino library")

Once the library is built, you will be able to download it to a location of your choice.

## Arduino IDE

![Arduino library](assets/images/arduino-ide.jpg "Arduino library")

Once you have downloaded the library, open up Arduino IDE, click **Sketch** -> **Include library** -> **Upload .ZIP library...**, navigate to the location of your library, upload it and then restart the IDE.

### Non-Continuous Classification

![Non-continuous classification](assets/images/non-continuous-classification.gif "Non-continuous classification")

Open the IDE again and go to **File** -> **Examples**, scroll to the bottom of the list, go to **AI_Patient_Assistance_inferencing** -> **nano_ble33_sense** -> **nano_ble33_sense_microphone**.

Once it opens copy the contents of [libraries/ai_patient_assistance/ai_patient_assistance.ino](libraries/ai_patient_assistance/ai_patient_assistance.ino "libraries/ai_patient_assistance/ai_patient_assistance.ino") into the file and upload to your board. This may take some time.

Once the script is uploaded, open up serial monitor and you will see the output from the program. The green LED on your device will turn on when it is recording, and off when recording has ended.

![Classified keyword](assets/images/arduino-nano-33-ble-sense.jpg "Classified keyword")

Now you can test your program by saying any of the keywords when the green light is on. If a keyword is detected the red LED will turn on.

### Continuous Classification

![Continuous classification](assets/images/continuous-classification.gif "Continuous classification")
0.9540173622437398
Now open **AI_Patient_Assistance_inferencing** -> **nano_ble33_sense** -> **nano_ble33_sense_microphone_continuous**, copy the contents of [libraries/ai_patient_assistance/ai_patient_assistance_continuous.ino](libraries/ai_patient_assistance/ai_patient_assistance_continuous.ino "libraries/ai_patient_assistance/ai_patient_assistance_continuous.ino") into the file and upload to your board.

Once the script is uploaded, open up serial monitor and you will see the output from the program. The red LED will blink when a classification is made.

#### BLE

This program acts as a BLE peripheral which basically advertises itself and waits for a central to connect to it before pushing data to it. In this case our central/master is a smart phone, but in the real world this would be a BLE enabled server that would be able to interact with a database, send SMS, or forward messages to other devices/applications using a machine to machine communication protocol such as MQTT.

![nRF Connect BLE](assets/images/ble.jpg "nRF Connect BLE")

You can use a free BLE app such as [nRF Connect desktop](https://www.nordicsemi.com/Products/Development-tools/nrf-connect-for-desktop) or [nRF Connect Mobile](https://play.google.com/store/apps/details?id=no.nordicsemi.android.mcp&hl=en_GB&gl=US) to connect to your device and read the data published by it.

When your BLE app connects to the program, the LED light will turn blue, once the app disconnects the LED will turn off.

# Conclusion

Here we have created a simple but effective solution for detecting specific keywords that can be part of a larger automated patient assistance system. Using a fairly small dataset we have shown how the Edge Impulse platform is a useful tool in quickly creating and deploying deep learning models on edge devices.

You can train a network with your own keywords, or build off the model and training data provided in this tutorial. Ways to further improve the existing model could be:

- Record more samples for training
- Record samples from multiple people

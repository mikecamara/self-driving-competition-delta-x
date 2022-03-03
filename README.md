# Self-driving competition Delta-X

In January 2022 the University of Tartu held the DeltaX Self-driving competition. Students were challenged to train deep neural networks to drive a 1:10 scale autonomous vehicle in a circuit and complete specific tasks such as avoiding fixed obstacles and following determining routes in the track.

This repository contains the [code](./code), the [data](./data) and the [models](./models) that I used as my solution for the challenges. [Here is the contest official website](https://courses.cs.ut.ee/t/DeltaXSelfDriving/).

Here is my experience:

# The rewarded teams

### Task 1: object avoidance and lane following
* 1st place 🥇 Rustam Abdumalikov and Aral Açıkalın
* 2nd place 🥈 Mike Camara
* 3rd place 🥉 Kristjan Roosild 


#### **Hats off to the winners** 🎩

The comp winners also made [their code open source here](https://github.com/rabdumalikov/self-driving-donkey-car).


# The car

Donkeycar S1 platform was used in the competition. Donkeycar is an open source, easy-to-use, and well-documented Python library that can be used with a self-driving 1:10 scale remote control car.

The scaled autonomous vehicle used in the competition shown in the figure below comes pre-assembled with a Raspberry Pi 4, frontal camera, remote-controlled car chassis, battery, and a sensor hat. The frontal camera was the only sensor used in the competition. It was used to collect the data, which means collecting labeled images used to train a CNN supervised classifier autopilot using behavioral cloning.

![thecar](https://user-images.githubusercontent.com/8085864/156641685-faf4b0a0-f444-4144-bbdc-90a55569a5db.png)

Donkeycar provides tools to collect and label expert demonstrations, clean inaccurate records from the dataset, and train models using deep neural networks. The Donkeycar autopilot machine learning models are created using a form of supervised learning called behavioral cloning. The behavior of an expert is recorded and associated with labeled data and then used to train the model. The model is the output of the neural network and consists of an array of decision-making algorithms.

In the video below, I was driving the task one track to collect data to train a model.

## TASK 1(imitation learning):
https://user-images.githubusercontent.com/8085864/156644024-0f76f1a4-f149-48d4-a59f-f1c8ed3217f2.mp4


The Donkeycar software was built upon open source deep learning libraries such as OpenCV for image processing and Keras for training and running the machine learning models. Keras is a lightweight python neural network library that runs on TensorFlow, which the Donkeycar libraries use to train and run autopilot models. From creating to driving autopilot models, Donkeycar offers a variety of tools that allow fast experimentation. 

As shown in the figure below, creating models with Donkeycar involves three steps:

a) To collect data using the car.
b) To transfer the data to a host computer for cleaning and training the model.
c) Transferring the autopilot model back to the car ready to be tested.

![Screen Shot 2022-03-03 at 10 09 49 pm](https://user-images.githubusercontent.com/8085864/156644820-5b8f0f42-7fcc-414f-88c7-2fed0b81ab63.png)

The competition goal was to create a software solution that could autonomously drive the toy car based on visual inputs.

I was responsible for the entire process, from data collection to multiple deployment iterations and learning from failures.

I had a solution that was successfully performed in the circuit, and I ended up taking second place in the competition. Below is the video of the winners' run for task 1.

## TASK 1(winners):
https://user-images.githubusercontent.com/8085864/156645502-006c9dbd-2dc8-415a-94c5-cacf1d9657e5.mp4


Below is the information to run my solution for task 1. The only prerequisite to running my solutions is that you have the latest Donkeycar library version 4.3 installed, that was the version I used during the competition, and some of the features used require that version. 

In other words, to run the following solution, you must have the Donkeycar library version 4.3 installed both in the vehicle itself and your personal computer or a simple Google Colab repository. 

If you don't have version 4.3 installed yet, you have to checkout to the Donkeycar dev branch.

If you would like more information on to create a Donkeycar from scratch you can read (my blog post)[https://softwareengineering.netlify.app/donkey-car/] in which I go in detail about how to install the libraries in both the vehicle and your computer, you can always check the (Donkeycar official documentation)[https://docs.donkeycar.com/]. 


### Running my solution for challenge 1

With the Donkeycar version 4.3 installed, run the models available in this repo. 

For task 1 run the linear model available in this repo and copy it to your local Donkeycar folder on your computer. Make sure you put inside the folder `mycar/models`. Then to copy it to the Raspberry Pi in your car, use the following command. The rsync command is used to sync the folder models from your computer with the folder models from your Raspberry Pi. 

````
rsync -rv --progress --partial ~/mycar/models/pilot-10k-linear-morning10am.h5 pi@raspberrypi:~/mycar/models/
````

With the model transferred to the car, you are ready to experiment with the self-driving model.

Make sure you connect to your Raspberry Pi from your computer by connecting via SSH. 

````
ssh pi@raspberrypi
````

Then connected to the security shell of your Raspberry Pi you can run the following command to run the model. 

````
python manage.py drive --model ~/mycar/models/pilot-10k-linear-morning10am.h5
````

For task 2, you will need to update the `myconfig.py file inside your Raspberry Pi as specified below and run the behavioral model.

For task 1 use [this myconfig file](./code/task-1/myconfig.py)

For task 2 use [this myconfig file](./code/task-2/myconfig.py)

The changes in the config file are to instruct Donkeycar libraries to use a different approach for deep neural networks training called conditional imitation learning. It is very similar to behavioral cloning, in which driving policies are given to create the models. However, a high-level command input is added to the imitation learning policy [this article](https://arxiv.org/abs/1710.02410) explains the method in detail. In other words, the behavioral models let us train on a single model with different behaviors that can be switched and respond to navigational commands. Using the joystick, you can change the behaviors you would like to train or see performing. For instance, you could prepare a model with a behavior to drive in the left lane and a different behavior to drive only in the right lane, and you can use the joystick to switch between these two behaviors.

The labeling is now more sophisticated and has the additional behavior/label, straight, left, right, etc., and the respective behavior/one_hot_state_array, an array with three values [0.0, 0.0, 1.0].

For task 2 run the behavioral model available in this repo and copy it to your local Donkeycar folder on your computer. Make sure you put inside the folder `mycar/models`. Then to copy it to the Raspberry Pi in your car, use the following command. 

````
rsync -rv --progress --partial ~/mycar/models/pilot-7k-behaviour-2-133.h5 pi@raspberrypi:~/mycar/models/
````

Then you can run the model with the following command. Notice that the `--js` joystick command is passed because you need to press the button R1 to change the behaviors during the run.
 
````
python manage.py drive --model ~/mycar/models/pilot-7k-behaviour-2-133.h5 --type=behavior --js
````

It is essential to notice that you don't have to train these models added to this repo. They had already been trained. The process of training a neural network involves providing examples of labeled data as input and applying mathematical and statistical concepts such as feed-forward and error backpropagation. Those functions will gradually reduce the error between the predicted output and the desired output across several iterations over the dataset. Each cycle of complete training iterations is called an epoch. The training session is composed of as many epochs needed until the model can no longer improve its prediction performance.

Before the training, you need to collect data. Below is the data collection video for task 2 of the challenge. 

## TASK 2(data-collection):
https://user-images.githubusercontent.com/8085864/156651337-61443148-be9c-4ec4-a7d0-11681e7447da.mp4

The data collection results, including all the generated labeled images, are available in this repository in the folder data. So, in case you would like to use the data I've collected to train new models you can copy the data to the Donkeycar folder `mycar/data` in your computer and run the following command to train the model for task 1. You don't need to specify the model type because the linear model used is the standard model used by Donkeycar. 

````
donkey train --tub data/tub_558_22-01-25 --model ./models/my-new-model-task-1.h5
````

For task 2 then, you can use the following from your computer. Notice that you now need to specify the model type as an argument.

````
donkey train --tub data/tub_585_22-01-25,data/tub_586_22-01-25 --model ./models/my-new-model-task-2.h5 --type=behavior
````

Then copy all the models back to the Raspberry Pi with the following command.

````
rsync -rv --progress --partial ~/mycar/models/ pi@raspberrypi:~/mycar/models/
````

With the models back to your Raspberry Pi, you should be ready to run the models on the track. 

# Lessons learned

- Train your models a few days in advance, definitely not on the day of the competition day. You might encounter all sorts of issues, such as connecting to the WIFI that can easily steal your time.
- Make sure your car is appropriately calibrated, making sharp 30 degree turns for both sides, and can also drive straight. You will have to tune both the hardware and the software for this.
- Your models must be robust, so consider collecting a lot of data, I mean, around 50k to 150k, in a different range of tracks and conditions.
- I strongly recommend using image augmentation techniques such as cropping, image flipping, blurring, gradient, and brightness corrections. It's more likely that your model will generalize better to unseen track and overfit less to the lighting conditions. 
- For task two, you will have to customize your joystick to have multiple buttons to select each state of the behavioral modal. The standard one button only to quickly switch the models won't help you win the challenge. 
- Have fresh batteries for your car, and make sure you don't mix them with other inefficient batteries that might be lying around. 



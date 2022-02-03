# Self-driving competition Delta-X

This is the [code](./code), the [data](./data) and the [models](./models) that I used in the DeltaX Self-driving competition in January 2022 held with 1:10 scale cars called DonkeyCar. [Here is the contest official website](https://courses.cs.ut.ee/t/DeltaXSelfDriving/).

The competition goal was to create a software solution that can autonomously drive a toy car based on visual inputs.

I was responsible for the entire process from data collection to multiple iterations of deployment and learning from failures.

I had a solution that was successfully deployed in the real world and I ended up taking the second place in the competition. 

## The rewarded teams were:

### Task 1: object avoidance and lane following
* 1st place 🥇 Rustam Abdumalikov and Aral Açıkalın
* 2nd place 🥈 Mike Camara
* 3rd place 🥉 Kristjan Roosild 

### Running the solution

In case you have already DonkeyCar version 3.0 installed then just run the models available in this repo.

For task 1 run the linear model:

````
python manage.py drive --model ~/mycar/models/pilot-10k-linear-morning10am.h5
````

For task 2, update myconfig.py file as specified [below](#running-models) and run the behavioral model:

````
python manage.py drive --model ~/mycar/models/pilot-7k-behaviour-2-133.h5 --type=behavior --js
````

### To install DonkeyCar from scratch:

I used Leo's repository because it had the DonkeyCar version 3.0 required for task 2. The official autorope version has version 4.2 which does not have the behavioral models available. 

Install DonkeyCar in both your computer and the car

````
git clone https://github.com/schobele/donkeycar
````

````
cd donkeycar
````

````
git checkout dev
````

````
pip install -e .[pi]
````

````
pip install numpy --upgrade
````

````
curl -sc /tmp/cookie "https://drive.google.com/uc?export=download&id=1DCfoSwlsdX9X4E3pLClE1z0fvw8tFESP" > /dev/null
````

````
CODE="$(awk '/_warning_/ {print $NF}' /tmp/cookie)"
````
````
curl -Lb /tmp/cookie "https://drive.google.com/uc?export=download&confirm=${CODE}&id=1DCfoSwlsdX9X4E3pLClE1z0fvw8tFESP" -o tensorflow-2.2.0-cp37-cp37m-linux_armv7l.whl
````

````
pip install tensorflow-2.2.0-cp37-cp37m-linux_armv7l.whl
````

Create a set of files to control your Donkey with this command:

````
donkey createcar --path ~/mycar
````

Configure options. Look at myconfig.py in your newly created directory, ~/mycar

````
cd ~/mycar
sudo nano myconfig.py
````

# Running models

For task one use [this myconfig file](./code/task-1/myconfig.py)

For task two use [this myconfig file](./code/task-2/myconfig.py)
and use it for data colletion, training and running the model.

The file is in the mycar folder.


````
cd ~/mycar
sudo nano myconfig.py
````

To collect new data using a joystic.

````
cd ~/mycar
python manage.py drive --js
````

Copy the data folder from the Raspberry Pi to the computer. 

````
rsync -rv --progress --partial pi@<your_pi_ip_address>:~/mycar/data/  ~/mycar/data/
````

### To train a new model

````
donkey train --tub data/tub_8_21-10-22 --model ./models/mypilot.h5
````

Copy the model back to the Raspberry Pi.

````
rsync -rv --progress --partial ~/mycar/models/ pi@raspberrypi:~/mycar/models/
````

### To run the model in the track

````
python manage.py drive --model ~/mycar/models/mypilot.h5
````
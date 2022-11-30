---
title: "Engineering Physics Robot Compitition"
excerpt: "<br/><img src='/images/70392644_2051523764949376_1583569878147661824_n.jpg'>"
collection: portfolio
---

![Skin the Bark of Man ...](images/70392644_2051523764949376_1583569878147661824_n.jpg)

Every year the Engineering Physics Department at UBC hosts Summer robotics competition where 15-16 student teams are tasked with creating autonomous robots to preform a series of tasks. 
This year's competition featured two robots positioned at the starting points on opposite sides of the table.

Both robots have the same objective: each has to follow a line up the ramp, after which point thier goal is to retreive the stones from the towers. Simply follow the black tape, locate the posts and retrive the stones from their perch. Easy. 


<a href="https://imgflip.com/gif/3awpf2"><img src="https://i.imgflip.com/3awpf2.gif" title="made at imgflip.com"/></a>

### Chassis design

The chassis is one piece of water-jet cut aluminum was, bent and bolted together. While accessing the inner electronics was a bit of a pain, this otherwise made for a light-weight and durable robot. 

![Skin the Brak of Man ...](images\70869839_545051122969047_2336191664739057664_n.jpg)

At the front of the chassis there is a semi-circular cutout with sloped walls leading to it. The idea here was that we could easly align ourselve with the posts, by simply driving in to them. We have a mechanical switch in the cavity that allows us to know when we have hit the post. This design choice proved a life saver - many other teams struggled to locate the stone, whereas the stone were always right where we expected/                                                     


# Micro controllers

In order to talk about the software, we first need to talk about our processors.

## Raspberry Pi:
The pi is controlled using Raspian operating system which is a linux based OS for the Raspberry Pi. All of our programming in the Pi is done with **Python**.

## BluePill:
We were provided with free stm32 BluePills for the purpose of controlling the robot. These chips are small adn cheap and they can be programmed using **C and C++**.

## Raspberry Pi Camera:
We use a version 2, 8 MegaPixel Raspberry Pi Camera. The reason we used the version two model was because it had a higher framerate and with our algorithms, we were able to increse the framerate to 50 fps. Since we were planning on finding plushies and ther marks on the field, we needed to have the ability to change the angle of the camera. To this end, we attached the camera on a servo and controlled the servo with the Pi.  

![Skin the Brak of Man ...](images\70392644_2051523764949376_1583569878147661824_n.jpg)

Every team was given a budget of 150 dollars and since we decided to use a camera , we needed to use a stronger controller board. Which is why we decided to use a Raspberry Pi in addition to the bluepill boards. Our controller hierarchy look something like this :

This way, the Raspberry Pi acts as the main controller. However, the raspberry pi did not have enough pwm pins and we preferred to control our servos and motors with the BluePills. The Pi processes the information from the camera frames and then sends commands to the motor controller BluePill through UART communication. The communication between these two boards is imperative since the raspberry pi constantly check which pwm signals is needed to follow the black tape. The other BluePill is only used to move the robot arm for picking up the Infinity Stones. Therefore the communication between the Pi and the arm BluePill is through two pins. The high and low states of those two pins are used to indicate the state of the arm (open, close, dron in dispensing). In addition to the arm, this second BluePill controlls the dispensing servos as well. 


# Software

The code for both of out BluePills were mostly simple. The codes and comments are included in our repository. However the main focus of our code was on the our vision code implementation of the. Usually with QRDs , people tend ot use PID control. For our system, we use a similar concept. Our image processing works somethin like this:
1. Take Grayscale frame from camera.
2. Use a Gaussian Kernel to add to the continuity of the pixel intensity values and reduce black spots.
3. Use a custom kernel to intensify line features and further reduce the black spectacles.
4. Have a binary filter for turning the pixel values to black and white.
5. Take out 5 or 6 horizontal lines out of the frame.
6. Find the points on those lines where the derivative of hte color intensity changes a lot. In other words, we find the edges of the black tape.
7. based on the position of the line edges, we find the position of the robot with respect to the center of the blakc line.
8. From there on we have a PID controller just like any other line following robot.


## Image of what we see from the black lines (take it offf online if we don't have one )

A couple of notes for our PID control:
1. We knew that for a normal line follower, we only technically need the Proportional and the Derivative, meaning a PD controller would do. However, since we were moving at a high velocity and the track had some rough turns, we discovered that we would need something like the Integral of the errors. For that purpose, we added statements to exponentially increase the error if the center of the black tape is too far out of the range, the error increases drastically to account for the sharp turns.
2. One of the problems with the black tape is that it reflects the light. Meaning it is highly likely for the camera to see white circle around and on the black tape. This effects the line following controller specially at high speeds. In order to combat this, you need to reduce the speed of the robot and to play around with the binary cutoff value for blakc and white. It is important to keep this in mind and make sure no irregularities are seen with the binary cutoff value on the path.
3. It is important to keep in mind the maximum, minimum and base speeds have a significant effect on the PID controller. To top that off, so does the battery voltages. You would use battery regulators to keep the voltage provided constant but then you may risk capping out the current provided. So we needed to make sure we are having our batteries at a know range of voltages.
4. Keep in mind the speed of the algorithm. Given that most PID controllers with line followers use a dedicated microcontrollers and run in a range of 1000 updates per second. 

<a href="https://imgflip.com/gif/3awp7v"><img src="https://i.imgflip.com/3awp7v.gif" title="made at imgflip.com"/></a>

One other important piece in the code is the detection of intersection. To this end, we always checked the length of the black line. If the length of the black line is larger than normal, we count that as a detected intersection. Again, the speed of the robot and update time for the algorithm really effect the detection of intersections.

We did have time to test out a couple of other ideas. For example, We used colorfiltering to find the plshies. However, since time was short we never actually ended up creating a full system for dropping off the plushies on the droping zone. There were marking on the posts and the gauntlet however they were damaged during the practive runs and we decided to use fixed turn and rotations for going into the posts. 

Time to talk about the arm

# Arm and Dispensing

The arm itself was coposed of three servo motors. One for changing the Z-axis angle, anotehr for the XY-plane angle and the other for closing and openning the arm. One of the main reasons we decided to use this arm was its reliablitiy. 


## Image of the arm

Given that we arrive at the posts, we were almost always able to pick up the stones. One main reason was the use of arduinos and the fact that the arm itself was rigid. The gens were all in different hshapes and sizes so the arm grip and its position were important, making servos a viable option. The arm itself was made out of *I FORGOT* to make it as lightweight as possible.

<a href="https://imgflip.com/gif/3awpl2"><img src="https://i.imgflip.com/3awpl2.gif" title="made at imgflip.com"/></a>



After the arm picks up the stones, it drops them into two yellow funnels. These funnels have servos at the bottom which open when we arrive at the gauntlet in order to drop the stones into the shallow holens on the gauntlet.
The entire arm and dispensing servoes are controlled by one BluePill which communicates with the Raspberry Pi.

<a href="https://imgflip.com/gif/3awpqg"><img src="https://i.imgflip.com/3awpqg.gif" title="made at imgflip.com"/></a>


And now, lets move on to the electrical components of the robot.

# Electrical Circuits and Connections 

Here are a list of the electrical components:


- **H_bridge Circuit**: 

## Image of the circuit and the schematics
This circuit is our main drive controller board. The circuit simply takes ina PWM signal from the BluPill and send it to the motors. One component of the circuit includes an OptoIsolator which disconnects the ground of the mtotr from the geound of the circuit. This is because the motor creates a lot of noise on its channel and a could damage the BluePill. Therefore, it is imperative to seperate the two from each other. **More Explanation requierd here**. This circuit is powered with a 16 Volt lipo battery.


![Skin the Brak of Man ...](images\70817571_901591420211422_8514916339565985792_n.jpg)


- **OptoIsolator Circuit**:
The concept of this circuit is simple. We use this circuit to both power the servos and sends the pwm signals. One major reason behind using this circuit is because we decided to use the same 16 Volt lipo battery with a regulated voltage. Since that battery grounds with the Barber Colman motor, we needed to make sure the ground of the motor doesn't connect with the BluePills. Therefore we have 6 optoisolators. 3 for the arm and two for the dispensing tubes.

![Skin the Brak of Man ...](images\70422108_2471018806507541_7287727707880685568_n.jpg)

- **BluePill circuits**:
This is a simple circuit which powers the two BluePills and has a regulator to bring down the voltage for 5 volts. This circuit is powered by a 8v lipo.

![Skin the Brak of Man ...](images\71049407_380775019508606_944748313830227968_n.jpg)

- **Additional Components** :
The only other components are a debounced wsitch which is used to detect when we are at the post and the Raspberry Pi. The Pi and is conneted to the camera servo, switch and the two BluPills. The Pi itself is powered by another 8 volt lipo. There is a power switch connected to the 8 volt lipo for the Pi which regulates the voltage to 5 volts.

![Skin the Brak of Man ...](images\70960511_465331177659830_429396592437493760_n.jpg)

# BallBalancer
RobotC project for a ball balancing robot 

you need:
- robotC ... you can download a trial version under 'http://www.robotc.net/' ... make sure to activate the 'natural language' option
- a '4-wheel balancer robot'
- two gyro sensors (one for the forward-backward axis the other one for the left-right axis)
- 4 motors driving four 58mm omni-wheels ( http://www.robotshop.com/en/58mm-plastic-omni-wheel-lego-nxt-servo.html )
- a basketball (size 5)

I have not found any construction plans for a ball-balancing robot. I have choosen a very simple mechanical approach.
just watch the video https://youtu.be/KPKlMlJ5BVk or https://youtu.be/swp7uNFtmfo to see the basic idea ...

the following approach is using 4 parallel working PID controllers for each axis (forward-backward==pitch and left-right==roll)

(1) 'gyro_speed': contolling the gyro speed
(2) 'gyroAngle': controlling the gyro angle
(3) 'robot_position': controlling the robot position
(4) 'robot_speed': controlling the robot speed

this approach gives a lot of room for parameter optimizations and is a very good example how a
combination of several PID controllers can work. The basic idea is explained below in more detail.
generally this 'challenge' is very similar to the gyroboy/segway example. The main difference is that
you need a full set of controllers for each axis (so you end up with 8 PID controllers)
why do we need all these parameters to balance a robot (lets focus just one axis for now)?

I am sure that there are a lot of robot fans out there that have tried to balance
a robot just by using a simple control loop using the gyro.
Imagine the robot is balanced and starts to fall forward. A simple PID conntrol loop approach
could use just the gyro angle to calculate the motor power that is needed to reduce the angle
(the error) again (driving the ball 'under' the robot again). If the parameters are chosen properly the robot will be in the balanced status
again (anytime soon). The problem is that there is no need (from the physical point of view) to reduce
(no 'force' that is reducing) the speed of the robot (the speed is the result of the acceleration phase
that was necessary to get the robot back into the balanced status).
As a consequence the robot is balanced for a while but the speed/position is not controlled
and so you end up with a robot at full speed (after a quite short time) without the possibility to control the balancing anymore.
So it is clear that we have to control the speed/position of the robot somehow.
the gyro control loops in this solution are nothing special. The motor power is 'proportional' (depending on the PID parameters)
to the gyro angle/speed (obviously the reference for these PID controllers is 0 here).

for the speed/position controller loop we have to consider the following fact:
imagine that the robot is a few mm behind the ideal reference position in a 'perfectly' balanced status.
So we have to move forward to correct the position
(to minimize the position error).
to move forward it is not 'allowed' just to move forward (driving the ball forward) because the robot would fall onto the backside.
to move forward the robot must move backwards first (driving the ball backward) and than accelerate the ball forward (here the gyro control loop is doing the job)
to balance again == driving the ball under the robot again (this is the way a balancing robot can make a 'step' forward).
In other words we have to set the motor power to end up with a positive feedback (instead of a negative) to be able to move 
forward afterwards to reach the reference position
(that means that we are increasing the position PID error in order to get the possibility to minimize the same error afterwards.
The bigger the distance to the reference point in our example
(the bigger the error) the more we must accelerate/move into the opposite direction to make a bigger step forward afterwards (the acceleration forward is 'triggered' by
the gyro control loops as soon the robot starts to fall forward)
(--> so a 'classic' PID approach should work fine but the output of the position and speed PID must be weighted
negative to the motor power)
the same concept/background is valid for the robot speed ...

if we are doing the same for both axis we end up with a robot that is able to balance on a ball. 

to find working PID parameter sets is quite painfull and more tricky compared to the segway problem. The main reason for that is the fact that the robot wheels are small
compared to the ball which is going hand in hand with a quite small maximum speed of the ball. This is limiting the possible 'dynamic' of the PID controllers significantly.

for those who are interested in the mathematical background I can recommend
https://en.wikipedia.org/wiki/PID_controller
the wiki article is covering some complex mathematical theories and you might have leran a lot more to be able to undertand everything in detail.
a very nice 'easy to understand' artircle about PID controllers you can find under
http://www.inpharmix.com/jps/PID_Controller_For_Lego_Mindstorms_Robots.html


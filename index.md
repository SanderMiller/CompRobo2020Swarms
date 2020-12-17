<iframe id="NetlogoModel" width="1200" height="1400" src="https://sandermiller.github.io/CompRobo2020Swarms/boidsFlocking.html" frameborder="0"></iframe>

## Project Overview
The goal of our project was to gain a deeper understanding of decentralized multi-agent systems and collective behaviour through the implementation 
of Boid's Swarming algorithm. 

## Selecting a Modeling Platform
We were primarily interested in analyzing the collective behaviour of a swarm. In order to minimize unnecessary complexity we looked for a platform made for agent-based modeling, rather than a physics based platform such as Gazebo. we also wanted to abstract the system a bit to be able to focus on the broad actions rathr than having to deal with the particulars of which sensors the robots would have and how to communicate between the sensors and the motors thought ROS commands. We ended up choosing [NetLogo](https://ccl.northwestern.edu/netlogo/) for its built in graphical user interface and visualization. Net logo also alows computations to occur spacially beased on agents in proximity so it provided a good platform for abstracting the sensor readings and implementing a distributed swarm robotics algorithm. These features allowed us to begin working on the model behaviour on day one. Although NetLogo uses its own programming language, we found that the NetLogo [programming guide](https://ccl.northwestern.edu/netlogo/5.3.0/programming.html) and [dictionary](http://ccl.northwestern.edu/netlogo/docs/index2.html) made the transition to a new language relatively easy.

## Our Agents


<p align="center">
  <img src="Images/MultipleAgents.png" />
</p>

The agents used in our simulation have three properties, **color**, **speed**, and **heading**. They have no sense of their absolute position within the map.
They are also able to **sense, localize, and transfer information** to any other agents within a given radius. It should be noted that the agents localize one another in their respective coordinate systems rather than in a global reference frame.

As we are investigating decentralized systems, all of our agents run identical code.



## Implementation of Boids Algorithm

### Separation
The first rule of Boids algorithm says that **agents too close together will turn away from each other** to spread out.

### Alignment
The second rule of Boids algorithm is that **agents within a flock should turn towards the average heading** of the flock. 

To do this we calculated the average heading of all agents within the visibility radius of each agent, and steered the agent accordingly. In NetLogo headings are given in degrees between 0 and 360. 
To calculate the average heading of the flock we first found the corresponding average x and y values of the heading angles by taking the sum of the cos and sin of each heading, and then dividing by the number visible agents within the flock. Then we took the inverse tangent of the average y value over the average x value. An example of calculating the average flock heading can be shown below.
<p align="center">
  <img height= 400 img src="Images/CalculatingAvgHeading.jpg" />
</p>

Once the average heading is calculated we must determine whether the agent must turn left or right. To do this we first find the difference between the agent's current heading and the calculated heading `currentHeading - avgHeading`. Then this difference is mapped into the range of -180 to 180 degrees.

        if difference < -180
          difference += 360
        elif difference > 180
          difference -= 360

Finally, if this remapped difference is negative we turn the agent right, and if the remapped difference is positive we turn the agent left.

### Cohesion
The third rule of Boids algorithm says that **agents should steer toward the flock's center of mass**.

This consisted of first calculating the center of mass of the visible agents. Due to the decentralized architecture of the swarm the aents are unaware of their absolute position. Instead we can track visible flockmates positions as a polar coordinates (distance and angle) with respect to a given agent. These polar coordinates are converted to cartesian coordinates using the equations _x = r * cos(θ)_ and _y = r * sin(θ)_.

## Adding Predators

### Inspiration

Once we got the base implementation of the flocking behavior implemented we wanted to extend the simulation. The Boid's flocking algorithm effectively mimics the flocking/schooling of fish. We decided to add in the behavior of avoiding predators as in real fish schooling this behavior significantly impacts the shape of schools.

<iframe id"FishSchoolingVideo" width="800" height"500" src="https://www.youtube.com/watch?v=tjSxHNhtTyE&ab_channel=CGTN" frameborder="0"></iframe>

We can see the real world behavior we were attempting to replicate in the video above. As we can see the fish school kind of forms large open tunnels where the sharks swim through the school. 

### Implementation

The fundamental behavior of avoiding predators is implemented in the same manner as the separation component of the base boid's algorithm. When a predator gets too close to the prey, the prey will correct towards the heading which points directly away form the predator. To mimic the panicked behavior of the fish when this occurs we added two components to this. Firstly the correction way from the predator is of a more severe angle then any of the flocking corrections which results in a more dramatic behavior rather then the gradual course adjustment underlying the flocking behaviors. Secondly we made escaping the predator take precedence over the flocking alignment behaviors. This means that the prey will prioritize corrections away from the predator as a replacement for corrections towards their flock or away from other prey until the fish is sufficiently far away from the predator that it returns to normal behavior. 

To allow the prey to distinguish the predators the predators are initialized separately and stored as a separate agent set. This means that the prey will perform flocking behaviors only with other prey and this prevents fish attempting to flock or color match with predators. Predators are agents which follow separate rules and so we denoted predators with a red arrow, a different color and shape from the prey. We had predators follow the same random pathing behavior that the fish have when not flocking so that the predators will naturally wander the space and the prey flocks will avoid the predators.

### Difficulties

Firstly we ran into the problem that predators and prey had the same speed this led to behaviors where flocks of prey would get 'stuck' in front of the predator constantly running away in the direction of the predator's movement but the state would continue until the predator turned away. IN the real world predators, especially sharks of the inspiration material, are stronger and faster than their prey so they tend to outpace the prey and catch them. To rectify this we made the predator move at a multiple of 1.2 times the speed of the prey. This meant that the predator would catch up with the prey in a chase scenario. As a result of this the prey would be forced to veer of course more to escape the predator.

Secondly, we noticed that the prey were not really behaving in the same manner as we had observed in the video. The predators tended to 'snowplough' the prey out of the way running into them and forcing them out of the way , rather than the wide swaths around the predators we see in the inspiration.
[TODO: gif of snowplough behavior]
 To solve this we realized that predators are often larger than the prey, and so prey are able to spot them from further away than they are able to recognise fellow flock members. To replicate this we increased the recognition radius for the predators relative to the recognition radius for fellow flockmates. The result of this was that prey seemed to part in swaths for the predator similar to how we can see in the inspiration.
[todo gif of normal behavior]  

## Collective Behaviours

## Next Steps

## Conclusion


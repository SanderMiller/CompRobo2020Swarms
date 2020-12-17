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
They are also able to **sense, localize, and transfer information** to any other agents within a given radius. it should be noted that the agents localize one another in thier respective cordinate systems rather than in a global reference frame.



## Implementation of Boids Algorithm

## Adding Predators

## Collective Behaviours

## Next Steps

## Conclusion


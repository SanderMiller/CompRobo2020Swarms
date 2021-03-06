## Blog 2

Hello Everyone!

Well we are starting to dig into the project now. Neither of us have much experience with working in NetLogo so this project has been a bit of an adventure to learn the NetLogo syntax. NetLogo has a function for most behaviors you want to enact; however it is hard to find these functions as documentation is limited. Much of our time has been spent reading through example code and trying to reverse engineer the components of the algorithms. Despite these challenges we have been able to implement a significant portion of the Boids flocking algorithm.

The Boids algorithm tells agents that if they are close to another agent/set of agents it should correct its heading towards the headings of its neighbors. This way all of the agents ‘flock’ together and start to collect to move in the same direction. This can be adjusted via the coherence value. To do this we have agents turn towards the average heading of their neighbors though we ran into some challenges with this as we will explain later. Boids algorithm also has agents correct to turn away from each other if they are too close. This way agents try not to fly through each other. Lastly if an agent is not near any others the agent will move in generally one direction with some perturbations in its heading (currently ± 2° with each time step).

Up to this point we have implemented the correcting of an agent’s heading towards the average heading of its neighbors. The first issue that we ran into is that the NetLogo simulation is organized on a grid system called patches. Collisions and sensor measurements are projected onto this grid system, so collisions and sensing only have the sensitivity of one patch. The initial NetLogo world had relatively large patches compared to the size of the agents. This meant our proximity sensors were not very effective because they would select either an overabundance of neighbors (such that most if not all agents were selected as neighbors in a given operation) or they would not select any at all. To fix this we had to learn about how to adjust the patch size and the drawing scale of the agents. This allowed us to increase the density of patches in the simulation, thus increasing our sensing fidelity and leading to more accurate proximity detection.

Once we had proximity detection in place we had to calculate an average heading for the agents within a flock which the agent would correct towards. We could not use a simple average formula because headings in Netlogo are stored as angles relative to zero. Thus a simple average formula would yield strange results when working with values near 0 or 360 degrees due to the ‘wrap around’ nature of the angles. After some iterations in trying to find a formula in NetLogo, general mathematical brainstorming, and research we discovered the following formula:

<p align="center">
  <img width="600" height="400" src="./Images/CalculatingAvgHeading.jpg">
  
The above formula takes the inverse tangent (atan2) of the average sin and average cosine to build the new heading. This method works mathematically to average angles, however it was challenging to implement in NetLogo as NetLogo does not have a dedicated `atan2` function. To yield the atan2 behavior one must use the normal `atan` function but switch the order of the arguments. The full implementation of this in NetLogo can be seen below.

      ask other turtles in-radius 5
        [
          set xSum (xSum + (cos heading)) ;; Add up all the heading cosines (x)
          set ySum (ySum + (sin heading)) ;; Add up all the heading sines (y)
          set numTurtles (numTurtles + 1) ;; Count the total number of agents
        ]
        
        ;; Take the inverse tangent of averageY/ averageX
        ifelse (xSum = 0) and (ySum = 0)[set avgHeading 0] [set avgHeading atan (ySum / numTurtles) (xSum / numTurtles)]
        if numTurtles > 1 and initHeading - avgHeading < 0 [rt .5 ] ;; Turn right
        if numTurtles > 1 and initHeading - avgHeading > 0 [lt .5 ] ;; Turn left
    
Once we had the average heading, we simply subtracted the agent heading from the average heading to get an indicator of which direction the agent should turn (a negative difference indicates a right turn is needed, and a positive difference means a left turn is needed.) Taken together, agents now correct towards the average heading of their neighbors for any agents inside their sensing range.

We are currently working on implementing the next step of the algorithm: turning away from neighbors who are too close. We struggled significantly with this task as their didn't seem to be a way to get the relative positions of two agents (as each agent sits at the origin of its respective coordinate frame, so it was difficult to find relative positions without relying on building a global coordinate frame (a task which is not readily available in a distributed computational system like the ones we are trying to model). This was until we found a NetLogo function `toward()` which returns the heading from one agent to another. Currently we are struggling with retrieving the nearest neighbor agent from the agentset of neighbor agents due to lack of experience with objects in NetLogo. Once we have a handle to the nearest agent we should be able to create a `headingToNearest()` function that returns the heading between an agent and its closest neighbor. Once we have that heading we simply need to correct away from that heading and will just about have completed our implementation of Boid’s algorithm.

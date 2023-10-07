# indoorCont
ABM simulation for indoor contact


## License
indoorContact / version 0.1.0
- install:

```python
!pip install indoorContact
```


### Purpose

###### The purpose of this model is to observe the patterns of individual spatiotemporal contact in indoor space, which are shaped by the interactions between humans (agents) and their environment (e.g., other agents and indoor spatial structures).

### Simulation process

<img src="/indoorContact/screenshot/Fig1.png" alt="Overview of simulation framework" width="700"/>

###### The simulation process, as depicted in Figure 1, begins with the creation of an indoor space, followed by the initialization of parameters. The indoor space is determined by specifying the width and height in meters, with the option to add a specified number of obstacles. These obstacles, each measuring 1m by 1m, are randomly deployed within the space. Entrances are created based on the specified number and coordinates, allowing agents to enter and exit at these positions. Furthermore, we can import the indoor space in a grid format that includes shape, obstacle placement and size, and entrance specifications, which offers flexibility in simulating various spatial configurations for users.
###### Following the creation of the indoor space, global parameters, such as group proportion, population, and simulation duration, are set. Subsequently, 'n' agents are generated, each endowed with individual attributes like speed and activeness. The simulation starts when the first agent enters the space, setting the beginning of the simulation time. Concurrently, a time-stamped table is generated, recording attributes such as agent position, group association, and contact count for each time unit. This data can be visualized in a movement animation, illustrating how individuals form groups and move within the indoor space over time.


### Path selection avoiding obstacles


<img src="/indoorContact/screenshot/Fig1.png" alt="Overview of simulation framework" width="500"/>

###### The algorithm emphasizes a straight route to a designated destination. If obstacles block this direct path, the agent chooses one of eight directions closest to the target. By repetitively applying this method, the algorithm avoids barriers and finds an optimal or nearly optimal route.
###### Specifically, the agent's main action is 'Check Path'. Here, it ascertains if any barriers <i>O</i> lie between its current spot <i>P<sub>current</sub></i> and the goal <i>P<sub>dest</sub></i>. If no obstructions exist, it indicates a straight path. When there's a direct route, the vector <i>ΔP</i>, which showcases the agent's subsequent location when traveling at speed <i>s</i>, turns into a unit direction vector normalized by the true distance from the current to the target spot. In essence, the agent shifts by <i>ΔP</i> from its current spot, concluding the algorithm. 
###### Should obstacles block the straightforward route, the algorithm sets the minDist variable (indicating the distance to <i>P<sub>dest</sub></i>) to infinity. For each of the eight direction vectors <i>d</i>, it formulates the potential direction vector <i>ΔP</i> as the product of speed <i>s</i> and direction <i>d</i>. If <i>ΔP</i> gets the agent nearer to <i>P<sub>dest</sub></i> without any obstacle <i>O</i> interference, the optimal direction <i>d<sub>best</sub></i> updates to the present direction <i>d</i>, and minDist gets updated. Post evaluating every direction, the agent advances in the <i>d<sub>best</sub></i> direction at speed <i>s</i>. This loop continues until the agent arrives at <i>P<sub>dest</sub></i> or no viable routes remain.













## Usage (Run simulation and export movie clip)

### 1. add space from data or make space

``` python
import indoorContact as ic


# -- with data --
# with entrance
space, FDD = ic.makeSpace(DFMap= ic.space_no_ent, FDD = True) #entrance = 2, obstacles = 1

# without entrance
space, entrance, FDD = ic.makeSpace(DFMap= ic.space_ent, FDD = True)

# -- without data --
# no obstacles
space = ic.makeSpace(space_x = 10, space_y = 10)

# deploy obstacles
space, obstacles_loc = ic.makeSpace(space_x = 10, space_y = 10, obstacles= 10)

# with chair
space, obstacles_loc = ic.makeSpace(space_x = 10, space_y = 10, obstacles = 10, chairs = 5)

# with entrance
space, entrance, FDD, obstacles_loc = ic.makeSpace(space_x= 15, space_y = 10, obstacles= 10, FDD = True, entrance = {'x': [15,15], 'y': [0,3]}) #x [from: to] / y: [from: to]

print(space)
```

![space](/indoorContact/screenshot/space.png)

This space is made of 0 and 1. 1 is obstacle (2: chair, 3: wall)


<img src="/indoorContact/screenshot/Fig2.png" alt="FDD (Flow Disturbance Degree)" width="450"/>

###### FDD represents the degree of disturbance caused by obstacles to people's movement, ranging between 0 and 1. A higher value signifies more obstruction to smooth movement. This is depicted in Equation 1. Here, T signifies the total indoor area, O denotes the obstacle area, P is the total passage area excluding obstacles, and <i>n</i> represents the number of passage segments. For instance, in Figure 2, with no obstacles, FDD is 0, whereas a fully obstructed space yields an FDD of 1. If there's a single passage, FDD equals 0.5, and with three passages, it's 0.833.






### 2. run contact simulation and count contact

``` python

# no scenario
result_df = ic.contact_Simulation(speed = [0.75, 1.8], activity = 5, totalPop = 10, space = space, entrance = entrance, total_time =100)
result_df = ic.countContact(result_df)

# adding chair scenario
space, obstacles_loc = ic.makeSpace(space_x = 10, space_y = 10, obstacles = 10, chairs = 5)
result_df = ic.contact_Simulation(speed = [0.75, 1.8], activity = 5, chair_scenario = [3, 10, 20], totalPop = 10, space = space, entrance = entrance, total_time =100)

# adding group scenario
space, obstacles_loc = ic.makeSpace(space_x = 10, space_y = 10, obstacles= 10)
result_df = ic.contact_Simulation(speed = [0.75, 1.8], activity = 5, group_scenario = [0.5, [2,3], [50, 10]], totalPop = 15, space = space, total_time =100)

```


![df result](/indoorContact/screenshot/result_df.head().png)

result dataframe of simulation. 
- time: total simulation time
- ID: unique ID of agent
- Sec: each second that each agent stay for
- X, Y: coordinates of agents
- Contact_count: the number of contact
- Vertex: verteces of trajectories
- Speed: speed of agents
- sit: sit or not (chair scenario)
- exit: 1 once agent go out
- STUCK: if agent is stuck and lose the way
- totalP: total population
- Chair: chair location where the agent sit
- group: group (1) or not (0)
- groupedP: population who are in the same group
- Contact_Sec: duration (second) of contact
- Contact_IDs: ID who encounter
- Contact_Pop: population that the agent encounter (physically contact)


### 3. export movie clip of simulation

``` python

# movie clip
ic.simul_clip_export('C:/Users/', result_df, space, 'result_clip.mp4')

```

![screenshot](/indoorContact/screenshot/contact_exper.png)

movie clip:
![movie clip of ABM simulation](/indoorContact/screenshot/contact_exper.mp4)


---

## Related Document: 
 will be added

## Author

- **Author:** Moongi Choi
- **Email:** u1316663@utah.edu

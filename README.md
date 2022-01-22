## Replication Package For Paper: Learning CNN-Encoded Propositions of PDDL-based Robotic Programs from Few Demonstrations

## [View on Github](https://github.com/LCEPRPFD/LCEPRPFD.github.io)

--- 

### Code

We implement the code of this project, which can be found [here](https://github.com/LCEPRPFD/learn_cnn_encoded_propositions).

---


#### Video

[![video_cover](/img/video_cover.png)](https://www.youtube.com/watch?v=FlgmvNA_D7Y)

---

### capNet

<div align="center">
    <img src="/img/capnet.png"/>
</div>

The input to the ***capNet*** is a 200×65 trajectory fragment. The first layer consists of two stacking LSTMs which is bidirectional and contain 128 hidden states.
It is followed by two fully-connected network blocks, each is composed of a dense layer, a batch normalization layer and a ReLU activation function layer. The hidden sizes are 256×128 and 128×64 respectively.
The output layer consists of a 64×4 dense layer followed by a dropout layer with p = 0.5. 
The multiple classification result is generated by a softmax layer.
During training the ***capNet***, the Batch size is 256 and learning rate is 0.0003.

---

### baseNet and propNet

<div align="center">
    <img src="/img/propnet.png"/>
</div>

The input to the ***baseNet*** or ***propNet*** is a 50×65 trajectory action states.
The basic block is composed of a convolutional layer, followed by a batch normalization layer, a ReLU activation function layer and a max pooling layer with kernel size 2. 
The detail is shown as following.

| layer |  filter  | stride | padding |
| :---: | :------: | :----: | :-----: |
|   1   | 128×50×1 |   1    |    1    |
|   2   | 64×64×3  |   1    |    1    |
|   3   | 64×32×3  |   1    |    1    |
|   4   | 32×32×3  |   1    |    1    |

The output layer consists of a 64×2 dense layer and a softmax layer.

---

### Program Generation
Once the CNN-encoded propositions of a Π<sub>task</sub> are learned, the PDDL program is generated in this step as explained in figure.

<div align="center">
    <img src="/img/synthesis.png"/>
    <p>Figure: The scope of program generation</p>
</div>

The \texttt{domain.pddl} is generated.
The propositions marked by the blue background related to the durative-actions and predicates are specified using the symbolic identifiers.
In particular, it needs to enumerate all propositions in the **predicates** part to ensure it is valid grammatically.
The **problem.pddl** is generated according to the task goal.
The **init** and the **goal** sections are specified by the first proposition prop<sub>0</sub> and the last proposition prop<sub>m</sub> of Π<sub>task</sub> respectively.

The configuration for ROSPlan is generated at the same time.
Each action in the PDDL program is associated with a corresponding action implementation, e.g., action<sub>i</sub> is associated with **actionImplementationi.py**.
In addition, each post-condition of the actions is associated with a corresponding sensing implementation, e.g., \textit{$prop_i$} with **sensingImplementationi.py**.
The correspondence is defined in the LaunchFile.
The action implementation is generated by appending the statement for invoking the parameterized API (i.e., **cAPI(params)**) of the corresponding capability.
The sensing implementation, on the other hand, is generated based on a template with two statements.
The first statement is to invoke an interface to obtain the real-time state vectors.
The second statement is to request a service which is responsible for evaluating a specified proposition through applying the corresponding ***propNet***.

---

### Feasibility Study under a Basic Setting

Among the set of capabilities, we design a pick-and-place task.
Suppose there are two tables, e.g., TableA and TableB in the simulation environment.
TableA is outside a room and there is a cube on the table.
TableB is inside the room.
The Fetch robot is first guided to move close to TableA and then to pick up the cube.
After catching the cube, the robot is guided to move to TableB and finally put the cube on the table.
We perform demonstrations five times from the viewpoint of an end user.

<div align="center">
    <img src="/img/TaskA_scenario.png"/>
    <p>Figure: Scenario of the Task</p>
</div>

### Robot and Scenario
We present the implementation of the approach on a ***Fetch*** robot in a simulation environment.
***Fetch*** robot is mainly equipped with a mobile base and a robotic arm.
The mobile base is made of two hub motors and four casters, while the arm is a seven-degree-of-freedom arm with a gripper.
The capabilities of a ***Fetch*** used in the case study are listed in table below.

<div align="center">
    <p>Table: Capabilities of Fetch used in the case study</p>
</div>

<table>
<thead>
  <tr>
    <th>Capability Type</th>
    <th>Description</th>
    <th>ROS API</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>move</td>
    <td>The robot moves to a target by the mobile base and the arm is free.</td>
    <td>move_base(target)</td>
  </tr>
  <tr>
    <td>transport</td>
    <td>The robot moves to a target by the mobile base while keeping the arm holding an object</td>
    <td>move_base(target)</td>
  </tr>
  <tr>
    <td>pick</td>
    <td>The robot grabs a target object without body moving.</td>
    <td>pick(target)</td>
  </tr>
  <tr>
    <td>place</td>
    <td>The robot puts the object held by the arm onto a target without body moving.</td>
    <td>place(target)</td>
  </tr>
</tbody>
</table>

We implement the approach in a ***Gazebo9*** simulation environment, which is high-fidelity and offers the ability to accurately and efficiently simulate populations of robots in complex indoor and outdoor environments.
The execution of the capabilities in the simulation environment shows uncertainty due to the physics engine or the probability-based algorithms. For example, an object slips away from the gripper of a robot, or a robot follows different moving paths by the SLAM navigation algorithm.
The feature increases the value of our approach since the robot behavior in the simulation environment can reflect similar variability as in the real environment with real physical hardware.

The states of the trajectories related to the robot features and the environment features can be obtained by subscribing to ROS topics.
One is the ***joint_states*** topic which provides 13 joints of the robot features including the arm and the mobile base.
They are 

> l_wheel_joint, r_wheel_joint, torso_lift_joint, bellows_joint, shoulder_pan_joint, shoulder_lift_joint, upperarm_roll_joint, elbow_flex_joint, forearm_roll_joint, wrist_flex_joint, wrist_roll_joint, l_gripper_finger_joint, r_gripper_finger_joint.

Each joint has 3 properties including position, velocity and effort.
There are 3×13 joint features from fetch itself.
The other is the ***/gazebo/model_states*** topic which provides 13 features associated with the position and the orientation of the robot and the cube as well.
They are

> fetch_pose_position_x, fetch_pose_position_y, fetch_pose_position_z, fetch_pose_orientation_x, fetch_pose_orientation_y, fetch_pose_orientation_z, fetch_pose_orientation_w, fetch_twist_linear_x, fetch_twist_linear_y, fetch_twist_linear_z, fetch_twist_angular_x, fetch_twist_angular_y, fetch_twist_angular_z.

There are 13+13 joint features from ***Gazebo***.
Thus, a state vector is composed of total 65 features.
The state vectors of a trajectory are sampled with a 100Hz frequency.

#### Dataset

- [traj_capability](https://1drv.ms/u/s!AtQlfXL28GxeajG8PychjufKca8?e=bMah0V): a large training set of robot's capability execution. This is a serialized dictionary dataset with four keys: "move", "pick_cube", "transport" and "place_cube". The value of each key contains a list of trajectories for an capability.
- [traj_demonstration](https://1drv.ms/u/s!An_sqEOHEaVaalbqMgGmadqPvqI?e=6wKFlF): a set of trajectories by executing the task. This is a serialized list dataset. Each element in the list is a dictionary object with four keys: "move", "pick_cube", "transport" and "place_cube". The value of each key is a trajectory of an capability.

#### Offline Training
We train the ***capNet*** based on the data set obtained from executing the four capabilities randomly.
For example, to make the robot move around in a space, and to make the robot pick a cube which is placed in different positions on a table.
We execute each capability more than one thousand times and obtain the data set containing more than 4,000 trajectories.
The training lasts for 50,000 epochs.
Each epoch contains 10 batch data and each batch data is selected when assembling a task.
We construct a meta-learner to help train the ***baseNet***.
The meta-learner manages the weights of the ***baseNet***.
In each epoch, it computes the gradients by training the tasks each by five gradient update steps.
The weights of the ***baseNet*** are updated by backpropogation from the meta-learner after accumulating the loss of all the tasks.
We use meta-learning to train the ***baseNet***.
The training of ***baseNet*** lasts for 50,000 epochs.
***propNet<sub>prop<sub>i</sub></sub>*** is learned by fine-tuning on ***baseNet***.
The training for the ***capNet*** and the ***baseNet*** is conducted on a machine with an AMD Ryzen 9 3950X processor and an RTX3090 GPU running with 128 GB RAM.

#### Action Segmentation
We set a sliding window of length 200.
Each trajectory is input to the ***capNet*** to compute the likelihood of the action label of each fragment, as presented in the figure below.

<div align="center">
    <img src="/img/segmentation.png"/>
    <p>Figure: Action segmentation of the 5 demonstrations</p>
</div>

<!-- \begin{figure}[!htb]
    \centering
    \includegraphics[width=0.7\textwidth]{drafts/segmentation.png}
%    \vspace{-15pt}
    \caption{Action segmentation of the 5 demonstrations
        \label{fig:segment}}
\end{figure} -->

The sequences of the five demonstration trajectories are largely the same with the task scenario.
The Π<sub>task</sub> is formulated as
<div align="center">
    <tr>⟨(move<sub>1</sub>, move, prop<sub>0</sub>, prop<sub>1</sub>),</tr>
    <tr>(pick<sub>1</sub>, pick, prop<sub>1</sub>, prop<sub>2</sub>), </tr>
    <tr>(transport<sub>1</sub>, transport, prop<sub>2</sub>, prop<sub>3</sub>), </tr>
    <tr>(place<sub>1</sub>, place, prop<sub>3</sub>, prop<sub>4</sub>)⟩.</tr>
</div>

#### ***propNet*** learning and program generation
The ***propNet*** related to the four symbols (i.e., ***prop<sub>1</sub>*** to ***prop<sub>4</sub>***) in the ***Π<sub>task</sub>*** is trained subsequently.
For example to train the ***propNet<sub>prop<sub>i</sub></sub>*** for ***prop<sub>i</sub>***, we collect the trajectory fragments corresponding to the first action from the five demonstration trajectories, i.e., ***{T<sub>move<sub>1</sub></sub>}***.
The fragment of the last 150 state vectors covering the end stage of the trajectory is labeled 1 and the remaining fragment is labeled 0.
The ***propNet<sub>prop<sub>i</sub></sub>*** is finetuned by a 15-step gradient descent.

The PDDL program as well as the ROSPlan implementation is generated according to the templates.
In this case, the five symbolic identifiers including ***prop<sub>0</sub>*** and ***prop<sub>4</sub>*** are specified in the **predicates** section of the **domain.pddl**, and the condition and the effect of the four durative-actions are represented by the identifiers.
As for ROSPlan, four action implementation files are generated by appending the corresponding APIs, e.g., **pick(target)**, or **place(target)**.
Four sensing implementation files each corresponding to a symbolic identifier are generated according to the template.
In addition, the relationships between the identifiers and the implementation files are specified in the LaunchFile.

#### Testing
We test the generated robotic program by proposing a new request with the same goal of the task but under a slightly changed environment setting.
We ask the robot to pick up a cube on TableA but located in a different position from the demonstrations, and then to transport and place the cube on TableB which is in another position in the room.
The request is realized by customizing the parameters of the capability APIs, and thus the actions can be executed as expected.
For example, the position of TableB can be obtained directly from the simulation environment. The position is specified as the actual parameter of the API, i.e., the parameter target of move_base.

ROSPlan ingests the PDDL program and produces a four-step plan which is composed of the sequence of actions, i.e., (move1, pick1, transport1, place1).
The TaskDispatching module manages the plan execution.
When a proposition is specified in the LaunchFile, the dispatching module invokes the corresponding sensing implementation to determine whether to execute the next action by evaluating the ***propNet***.

---

### Accuracy Evaluation under Different Settings

- _TP_ (true positive) represents the correct evaluation of a proposition (i.e., evaluate the truth value) to an action which is completed correctly
- _TN_ (true negative) represents the correct evaluation of a proposition (i.e., evaluate the false value) to an action which is executed abnormally
- _FP_ (false positive) represents the incorrect evaluation of a proposition (i.e., evaluate the false value) to an action completed correctly.
- _FN_ (false negative) represents the incorrect evaluation of a proposition (i.e., evaluate the truth value) to an action executed abnormally.
- _cnt_ represents the total count of proposition evaluation performed during the program execution in an experiment, the accuracy is calculated by \#TP+\#TN/_cnt_, where \# means the count of the verdict.
- _SeR_ is short for stationary-environmental request. _VeR_ is short for variable-environmental request.
- _\#ES_ represents the count of the success during the executions. _\#EF_ represents the count of the failure.

The raw data is provided in [folder](/data/).

---

#### General Accuracy

<div align="center">
    <p>Table: Accuracy of proposition verdict</p>
</div>

<table align="center">
    <thead>
      <tr align="center">
        <th></th>
        <th></th>
        <th>cnt</th>
        <th>#TP</th>
        <th>#TN</th>
        <th>#FP</th>
        <th>#FN</th>
        <th>Accuracy</th>
        <th>#ES</th>
        <th>#EF</th>
      </tr>
    </thead>
    <tbody>
      <tr align="center">
        <td rowspan="2">Our method (5)</td>
        <td>SeR</td>
        <td>152</td>
        <td>130</td>
        <td>8</td>
        <td>8</td>
        <td>6</td>
        <td>90.8%</td>
        <td>28</td>
        <td>8</td>
      </tr>
      <tr align="center">
        <td>VeR</td>
        <td>151</td>
        <td>120</td>
        <td>11</td>
        <td>15</td>
        <td>5</td>
        <td>86.8%</td>
        <td>19</td>
        <td>11</td>
      </tr>
      <tr align="center">
        <td rowspan="2">Our method (10)</td>
        <td>SeR</td>
        <td>169</td>
        <td>148</td>
        <td>11</td>
        <td>10</td>
        <td>0</td>
        <td>94.1%</td>
        <td>29</td>
        <td>11</td>
      </tr>
      <tr align="center">
        <td>VeR</td>
        <td>177</td>
        <td>151</td>
        <td>4</td>
        <td>15</td>
        <td>7</td>
        <td>87.6%</td>
        <td>24</td>
        <td>4</td>
      </tr>
      <tr align="center">
        <td rowspan="2">Baseline (5)</td>
        <td>SeR</td>
        <td>137</td>
        <td>95</td>
        <td>12</td>
        <td>30</td>
        <td>0</td>
        <td>78.0%</td>
        <td>2</td>
        <td>14</td>
      </tr>
      <tr align="center">
        <td>VeR</td>
        <td>79</td>
        <td>29</td>
        <td>3</td>
        <td>47</td>
        <td>0</td>
        <td>40.5%</td>
        <td>0</td>
        <td>3</td>
      </tr>
      <tr align="center">
        <td rowspan="2">Baseline (50)</td>
        <td>SeR</td>
        <td>154</td>
        <td>119</td>
        <td>14</td>
        <td>10</td>
        <td>11</td>
        <td>86.3%</td>
        <td>15</td>
        <td>14</td>
      </tr>
      <tr align="center">
        <td>VeR</td>
        <td>81</td>
        <td>35</td>
        <td>3</td>
        <td>43</td>
        <td>0</td>
        <td>46.9%</td>
        <td>4</td>
        <td>3</td>
      </tr>
      <tr align="center">
        <td rowspan="2">Baseline (100)</td>
        <td>SeR</td>
        <td>182</td>
        <td>153</td>
        <td>12</td>
        <td>10</td>
        <td>7</td>
        <td>90.7%</td>
        <td>21</td>
        <td>12</td>
      </tr>
      <tr align="center">
        <td>VeR</td>
        <td>78</td>
        <td>32</td>
        <td>6</td>
        <td>40</td>
        <td>0</td>
        <td>48.7%</td>
        <td>4</td>
        <td>6</td>
      </tr>
      <tr align="center">
        <td rowspan="2">Baseline (150)</td>
        <td>SeR</td>
        <td>184</td>
        <td>163</td>
        <td>19</td>
        <td>2</td>
        <td>0</td>
        <td>98.9%</td>
        <td>29</td>
        <td>19</td>
      </tr>
      <tr align="center">
        <td>VeR</td>
        <td>84</td>
        <td>34</td>
        <td>7</td>
        <td>43</td>
        <td>0</td>
        <td>48.8%</td>
        <td>0</td>
        <td>7</td>
      </tr>
    </tbody>
</table>


This Table shows the comparision between our method and baseline. 
Each method is evaluated in both _SeR_ and _VeR_.
We uses the robotic program which is learned and generated from five demonstrations. 
The experiments is executed for 50 times.
<!-- The raw data is provided by [5-shot_SeR.csv](/data/5-shot_SeR.csv), [5-shot_VeR.csv](/data/5-shot_VeR.csv), [baseline_SeR.csv](/data/baseline_SeR.csv), [baseline_VeR.csv](/data/baseline_VeR.csv). -->

---

#### Accuracy Impacted by the Number of Demonstrations

<div align="center">
    <p>Table: Accuracy of proposition verdict for SeR</p>
</div>

<table align="center">
    <tr align="center">
        <td>#demonstration</td>
        <td>cnt</td>
        <td>#TP</td>
        <td>#TN </td>
        <td>#FP</td>
        <td>#FN</td>
        <td>Accuracy</td>
        <td>#ES</td>
        <td>#EF</td>
    </tr>
    <tr align="center">
        <td>1</td>
        <td>140</td>
        <td>100</td>
        <td>13</td>
        <td>19</td>
        <td>8</td>
        <td>80.7%</td>
        <td>10</td>
        <td>13</td>
    </tr>
    <tr align="center">
        <td>5</td>
        <td>152</td>
        <td>130</td>
        <td>8</td>
        <td>8</td>
        <td>6</td>
        <td>90.8%</td>
        <td>28</td>
        <td>8</td>
    </tr>
    <tr align="center">
        <td>10</td>
        <td>169</td>
        <td>148</td>
        <td>11</td>
        <td>10</td>
        <td>0</td>
        <td>94.1%</td>
        <td>29</td>
        <td>11</td>
    </tr>
    <tr align="center">
        <td>20</td>
        <td>159</td>
        <td>136</td>
        <td>15</td>
        <td>4</td>
        <td>4</td>
        <td>95.0%</td>
        <td>27</td>
        <td>15</td>
    </tr>
</table>

This table evaluates the impact of the number of demonstrations on our approach. 
To this end, we learn from 1, 5, 10 and 20 demonstrations in _SeR_.
The experiments is executed for 50 times.
<!-- The raw data is provided by [1-shot_SeR.csv](/data/1-shot_SeR.csv), [5-shot_SeR.csv](/data/5-shot_SeR.csv), [10-shot_SeR.csv](/data/10-shot_SeR.csv), [20-shot_SeR.csv](/data/20-shot_SeR.csv). -->

---

#### Accuracy Impacted by the Demonstrations under Difference Environment Settings

<div align="center">
    <p>Table: Accuracy of proposition verdict</p>
</div>

<table align="center">
    <tr align="center">
        <td>#demonstration</td>
        <td>cnt</td>
        <td>#TP</td>
        <td>#TN </td>
        <td>#FP</td>
        <td>#FN</td>
        <td>Accuracy</td>
        <td>#ES</td>
        <td>#EF</td>
    </tr>
    <tr align="center">
        <td>10</td>
        <td>162</td>
        <td>134</td>
        <td>11</td>
        <td>8</td>
        <td>9</td>
        <td>89.5%</td>
        <td>22</td>
        <td>11</td>
    </tr>
</table>

This table shows the evaluation of the impact of demonstrations conducted in different environment settings on the approach accuracy. 
<!-- The raw data is provided by [10-shot_RQ3.csv](/data/10-shot_RQ3.csv). -->

<div align="center">
    <img src="/img/rq3_scenario.png"/>
    <p>Figure: Scenario of three tables</p>
</div>

As shown in the figure, we change the environment by adding a new Table TableC located outside the room.
We perform ten demonstrations in the new environment. 
Half of the demonstrations are performed in which the robot deliveries a cube from TableA to TableB.
The rest demonstrations are performed to delivery a cube from TableA to TableC.
By applying our approach, the action sequence is formulated to delivery a cube from TableC to TableB.
The experiments is executed for 50 times.

---




**********************************
Semantics of Describing Scenes
**********************************

In this section, we define the semantics of AVUnit(Scenario Description). Even though AVUnit only  describes  the  motion  task  of  the  ego vehicle, the predefined motions of NPC vehicles and pedestrians, and other environment related information, such as time and  weather,  it  describes  a  concrete  scenario  implicitly  as the  trajectory  of  the  ego  vehicle  is  determined  by  the  ADS under  testing  according  to AVUnit.  Hence,  we  define  the semantics  of AVUnit as  a ``trace``,  which  is  a  sequence  of scenes.  Here  a  scene  is  a  snapshot  of  the  physical  world, including  the  status  of  all  objects  defined  in AVUnit(e.g.,the ego vehicle, NPC vehicles, pedestrians, and weather) and other traffic conditions (e.g., traffic signs), and the ADS world, including the perception results of NPC vehicles, pedestrians, obstacles, and traffic conditions.


Basic Defination of scene
==================================

``Defination 1``: A scene is a mapping θ such that:

* θ(map) is the map (i.e., a map name);
* θ(time) is the time;
* θ(weather) is the weather.
* θ(ego) is  the  status  of  the  ego  vehicle,  including  the position, orientation, velocity, and acceleration;
* θ(npc,truth) maps each NPC vehicle ``npc`` to its status, including position, orientation, velocity, acceleration, andsize, in the real world;
* θ(ped) maps  each  pedestrian ``ped`` to  its  status,  i.e.,  its position,  orientation,  velocity,  acceleration,  and  size,  in the real world;
* θ(obs) maps each static obstacle ``obs`` to its position andsize in the real world;
* θ(traffic) returns the state of different traffic signs, suchas traffic lights, stop sign, and speed limit sign, in the real world.
* θ(npc,perception) maps  each  NPC  vehicle ``npc`` to  itsstatus in the view of the ego vehicle;
* θ(ped,perception) maps each pedestrian ``ped`` to its status in the view of the ego vehicle;
* θ(obs,perception) maps  each  static  obstacle ``obs`` to  its status in the view of the ego vehicle;
* θ(traffic,perception) returns the state of different traffic signs in the view of the ego vehicle.



Feasible Regions
==================================

At each scene, the objects, i.e., the ego vehicle, npc vehicles, pedestrians  and  static  obstacles,  should  be  on  the  feasible region  of  the  map,  which  means  their  positions  should  not exceed  the  region  of  the  defined  map.  Additionally,  since vehicles should move along lanes, their positions should also be limited to roads. Note that pedestrians can move around the feasible region of the map. Moreover, a vehicle or pedestrianhas  its  own  limit  on  the  maximal  speed.  Specifically,  for  the initial scene :math:`\theta_0`, we should also guarantee the the objects are located  at  different  positions.  Note  that  during  the  execution of the scenario, we do not require and also cannot guarantee collision avoidance of the ego vehicle or other objects. Hence,the constraints for each scene can be defined as follows.

``Defination 2``: Let :math:`A(\theta(map))` and :math:`L(\theta(map))` be  the  whole feasible  region  and  the  lane  region  of  the  map,  respectively, and :math:`OBJ` be  the  set  of  the  ego  vehicle,  all  NPC  vehicles, pedestrians and static obstacles in AVUnit. Let :math:`p(\theta(object))` and :math:`vs(\theta(object))`, :math:`object \in OBJ` denote  the  position  and speed of an object at a scene. The maximal speed of an objectis  denoted  as :math:`vs_{\max}(object)`.  Then, :math:`\forall \theta`,  it  should  satisfy  the following constraints:

.. math::

    p(\theta(object))\in A(\theta(map)) \label{c1}\\
    vs(\theta(object)) \leq vs_{\max}(object)\label{c2}\\
    p(\theta(npc)) \in L(\theta(map))\label{c3}

The initial scene :math:`\theta_0` should further satisfy:

.. math::

	\forall e_1, e_2\in OBJ, p(\theta_0(e_1)) \neq p(\theta_0(e_2)) \label{c4}.


Unchangeable Parameters
==================================

In AVUnit ,  we  do  not  allow  the  change  of  maps  during the  execution  of  a  scenario.  A  vehicle  is  also  not  allowed  to move  back  during  its  motion  on  a  road.  Hence,  we  have  the following constraints.

``Defination 3``: Given two successive scenes :math:`\theta_1` and :math:`\theta_2` of a trajectory, and let :math:`l(\theta(npc))` be the lane where the vehicle :math:`npc` is running along at scene :math:`\theta`. Then, we have:

.. math::

	\theta_1(map) = \theta_2(map);  \label{c5}\\
    l(\theta_1(npc)) \neq  l(\theta_2(npc)) \vee \nonumber\\
    d(p(\theta_1(npc)), l(\theta_1(npc))) \leq d(p(\theta_2(npc)), l(\theta_2(npc))) \label{c6}.


Overall Defination
==================================

``Defination 4``: A trace of AVUnit is a sequence of scenes, denoted as :math:`T=\langle \theta_0, \theta_1, \ldots, \theta_n \rangle`, satisfying the constraints described in Equations above.

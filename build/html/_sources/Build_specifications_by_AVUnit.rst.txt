###############################
Define Specifications by AVUnit
###############################

This section summarises the syntax of AVUnit (Specification Part), our langauge for specifying AV correctness properties. In contrast to existing AV testing approaches, which tend to focus on weak oracles (e.g.~no collisions, reach destination eventually), AVUnit allows for the specification of richer correctness conditions about how the ego vehicle behaves across the multitude of scenarios it encounters during its journey.

*****************************
Trajectories
*****************************

Given a trace :math:`\langle \theta_0, \theta_1, \ldots, \theta_n \rangle` of scenes, represented by the variable :math:`trace`, we can use AVUnit to assert properties about the trajectories of objects along the trace.
In this section, we present the syntax of trajectories and the five types of expressions involving them: perception difference, position, velocity, speed, and acceleration.

.. _objecttrajectory:

Object Trajectories
========================
AVUnit can be used to denote the trajectories of the ego vehicle, the trajectories of agents (e.g. NPCs, pedestrians, obstacles) as perceived by the ADS, and the true trajectories of those agents.

BNF Defination:
::

  objectTrajectory ::= egoTrajectory | agentPerceivedTrajectory | agentTrueTrajectory

.. note:: 

  The ``egoTrajectory`` , ``agentPerceivedTrajectory`` , ``agentTrueTrajectory`` will be introduced in the following part. The BNF defination here is a simplified one. For the complete version, please refer to :doc:`here<Overall_BNF>`.


Ego Trajectory
----------------

The trajectory :math:`\langle \theta_0(ego), \ldots \theta_n(ego)\rangle` of the ego vehicle (:math:`egoTrajectory`) relative to a :math:`trace` is obtained as follows:

BNF Defination:
::

  egoTrajectory ::= trace'['ego']'

.. note:: 

   The BNF defination here is a simplified one. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:

::

	ego_state = trace[ego];

Agent Perceived Trajectory
------------------------------

The trajectory :math:`\langle \theta_0(agent, perception)`, :math:`\ldots`, :math:`\theta_n(agent, perception)\rangle` of an agent as perceived by the ego vehicle is obtained using AVUnit code of the following form:

BNF Defination:
::

  agentPerceivedTrajectory ::= trace'[perception]['(npcVehicle|pedestrian|obstacle)']'

.. note:: 

   Note that the agent can be an NPC vehicle, pedestrian, or obstacle as specified by the user in a scenario. The BNF defination here is a simplified one. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:

::

	npc1_state = trace[perception][npc1];
	npc2_state = trace[perception][npc2];
	ped_state = trace[perception][ped];

Agent Truth Trajectory
------------------------------

Finally, the actual (ground truth) trajectory :math:`\langle \theta_0(agent, truth), \ldots \theta_n(agent, truth)\rangle` of an agent is obtained using code of the following form:

BNF Defination:
::

  agentTrueTrajectory ::= trace'[truth]['(npcVehicle|pedestrian|obstacle)']'

.. note:: 

   Note that the agent can be an NPC vehicle, pedestrian, or obstacle as specified by the user in a scenario. The BNF defination here is a simplified one. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:

::

	npc1_truth = trace[truth][npc1];
	npc2_truth = trace[truth][npc2];
	ped_truth = trace[truth][ped];

*****************************
Expressions
*****************************

.. _expressions:

Perception Difference Expressions
====================================

The perception systems of ADSs are currently far from perfect, which makes it important to be able to specify properties involving expressions over the difference (``perceptionDiffExpr``) between some trajectory that it perceives (e.g. the position and velocity of an NPC) and what that trajectory actually is. 

BNF Defination:
::

  perceptionDiffExpr ::= 'diff('agentPerceivedTrajectory','agentTrueTrajectory')'

.. note:: 

   Note that the agent can be an NPC vehicle, pedestrian, or obstacle as specified by the user in a scenario. The BNF defination here is a simplified one. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:

::

	diff_npc1 = diff(npc1_state, npc1_truth);
 	diff_npc2 = diff(npc2_state, npc2_truth);
 	diff_ped = diff(ped_state, ped_truth);

.. note:: 
	
	The ADS's perception difference with respect to some NPC vehicles and a pedestrian can be obtained as above code.


Distance Expression
============================================

One can write an expression over the distance (``distanceExpr``) between two objects or between objects and specific positions (:ref:`Position<type_position>`) in the scene. AVUnit treats the latter as special single-point polygons.

BNF Defination:
::

  distanceExpr ::= 'dis('(position|objectTrajectory)','(position|objectTrajectory)')'

.. note:: 

   The ``position`` here is type of :ref:`Position<type_position>`. The ``objectTrajectory`` here is type of :ref:`Object Trajectories<objecttrajectory>`. The BNF defination here is a simplified one. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:

::

	testing_p = (20, 214);
	dis1 = dis(ego_state, npc1_truth);
	dis2 = dis(ego_state, testing_p);

.. note:: 
	
	The code denotes the distance between the ego vehicle and an NPC vehicle (Line 2) as well as the ego vehicle and and a specific point (Line 3).

Speed Expression
============================================

The language can express the difference between two speeds (``speedExpr``), which are real scalars without directions.

BNF Defination:
::

  speedExpr ::= 'spd('(speed|objectTrajectory)','(speed|objectTrajectory)')'

.. note:: 

   The ``speed`` here is type of :ref:`Real Value<type_realvalue>`. The ``objectTrajectory`` here is type of :ref:`Object Trajectories<objecttrajectory>`. The BNF defination here is a simplified one. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:

::

	speed_dis = spd(ego_state, npc2_state);

.. note:: 
	
	The code expresses the difference between the speed of the ego vehicle and an NPC vehicle.

Velocity Expression
============================================

AVUnit can express the difference between velocities (``velocityExpr``), which unlike speeds, are vectors that include direction.

BNF Defination:
::

  velocityExpr::='vel('(velocity|objectTrajectory)','(velocity|objectTrajectory)')'

.. note:: 

   The ``velocity`` here is type of :ref:`Coordinate<type_coordinate>`. The ``objectTrajectory`` here is type of :ref:`Object Trajectories<objecttrajectory>`. The BNF defination here is a simplified one. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:

::

	velocity_dis = vel(ego_state, ped_truth);

.. note:: 
	
	The code expresses the actual difference in velocity between the ego vehicle and a pedestrian.

Acceleration Expression
============================================

The language supports acceleration expressions (``accelerationExpr``; also vectors), which are important for specifying properties about hard breaking and sudden acceleration during travel, which in general should not be acceptable behaviour of AVs. 

BNF Defination:
::

  accelerationExpr ::= 'acc('(velocity|objectTrajectory)','(acceleration|objectTrajectory)')'

.. note:: 

   The ``velocity`` here is type of :ref:`Coordinate<type_coordinate>`. The ``objectTrajectory`` here is type of :ref:`Object Trajectories<objecttrajectory>`. The BNF defination here is a simplified one. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:

::

	acceleration_dis = acc(npc1_state, npc2_state)

.. note:: 
	
	The code expresses the distance between the acceleration vectors of the two NPC vehicles.

.. _Combination_of_Expression:

Expression Arithmetic Operators
============================================

Note that while not shown in the grammar above (For details, please refer to :doc:`here<Overall_BNF>`), basic operators can also be applied to the above expressions. AVUnit applies ``.+``, ``.-``, ``.*`` and ``./`` to describe element-wise arithmetic operators on different expressions. 

For example, the following code:

::

	ave_diff = (diff_np1 .+ diff_npc2 .+ diff_ped) ./ 3;

.. note:: 
	
	The code expresses the average perception error of the ADS perception module with respect to two NPC vehicles and a pedestrian.

*****************************
Predicates and Assertions
*****************************

AVUnit supports general assertions over the previously defined expressions, e.g. properties about the ego vehicle's acceleration behaviour.

.. _Atomic_Predicates:

Atomic Predicates
====================

The simplest assertions we can specify in AVUnit are atomic predicates defined over :ref:`expressions<expressions>` and relational operators (e.g. ==, !=, >, >=). This is the basic elements of General Assertions.

BNF Defination:
::

  	atomicPredicate ::= specExpr compareOperator specExpr

.. note:: 

   The ``specExpr`` here is type of :ref:`Expressions<expressions>`, :ref:`Combination of Expression<Combination_of_Expression>`, or :ref:`Real Value<type_realvalue>`. The ``compareOperator`` here can be one of {==, !=, >, >= }. The BNF defination here is a simplified one. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:

::

	predicate_0 = dis1 > 1;
	predicate_1 = speed_dis > 0;
	predicate_2 = ave_diff <= 0.5;

.. note:: 
	
	The above three atomic predicates respectively assert that some distance is larger than one, that some object is not stationary, and that some average difference in distance perception is less than 0.5.


General Assertions
====================

In AVUnit, atomic predicates(``atomicPredicate``) can be combined with temporal operators---always' (``G``), eventually (``F``), until (``U``), and next (``X``)---in order to specify rich assertions over the traces of testing scenarios. Furthermore, the resulting assertions can be combined with the standard logical operators: and (``&``), or (``|``), not (``~``), and implies (``->``).

BNF Defination:
::

  	assertion ::= atomicPredicate | assertion('&' | '|' | '->') assertion|'~' assertion | ('G'|'F'|'X'|'U')assertion | ('G'|'F'|'U')'['realExpr':'realExpr']'assertion

.. note:: 

   The ``atomicPredicate`` here is type of :ref:`Atomic Predicates<Atomic_Predicates>`. The ``realExpr`` is type of :ref:`Real Value<type_realvalue>`. The BNF defination here is a simplified one. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:

::

	assertion1 = dis(ego_state, npc1_truth) <= 50 -> diff(npc1_state, npc1_truth) < {0.5};
	assertion2 = dis(ego_state, npc1_truth) >= 1;
	assertion3 = G(assertion1 & assertion2);

.. note:: 
	
	These respectively specify that: 

	(1) if the true distance between the ego vehicle and NPC1 is less than 50, then perception difference is less than 0.5; 
	
	(2) the actual distance between the ego vehicle and NPC1 is greater than or equal to 1; 

	(3) the previous two assertions should always hold along the trace.


***********************
A Complete Example
***********************

For the scenario defined :ref:`here<scenario_a_complete_example>`, we can specify different assertions, such as collision avoidance with ``npc1``, i.e., at any time, the distance between the ego vehicle and ``npc1`` is larger than a predefined safety distance. It can be described using AVUnit as follows.

::

	Trace trace = EXE(scenario0);
	ego_vehicle_state = trace[ego];
	npc_vehicle1_truth = trace[truth][npc1];

	dis1 = dis(ego_vehicle_state, npc_vehicle1_truth);
	statement1 = dis1 >= 1.0;
	trace |= G (statement1);

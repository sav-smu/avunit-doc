.. _special_types:

Special Types
==============

The following types are specially designed for AVUnit, they are based on the types of :ref:`basic types<basic_types>`. These types can be utilized to describe the scenarios by details. 

.. index:: ! position ! lane ! laneposition ! coordposition

.. _type_position:

Position
--------

``Position``: This type can describe position of objects. In AVUnit, the positions of different objects (including the car under test, NPCs, and pedestrians) in scenes are specified using one of two kinds of positional references:
(1) coordinates, i.e. a 2D or 3D vector expression; or (2) lanepositions, a position relative to a lane’s  starting point.

CoordPositions
^^^^^^^^^^^^^^^^
``CoordPositions`` can directly describe the position of objects. When specifying a coordinate position, one of three different 'frames' can be chosen:

* IMU: the vehicle coordinate system (forward-left-up), in which the origin is the position of the ego vehicle.
* ENU: East-north-up, in which coordinates are relative tothe map origin. If no coordinate frame (coordFrame) isspecified, then ENU is used by default.
* WGS84: World geodetic system, whose coordinate originis the Earth’s center of mass. It is the reference coordinatesystem used by the GPS (Global Positioning System).


BNF Defination:
::
  coordPosition ::= [coordFrame] coordExpr
  coordFrame ::= 'IMU' | 'ENU' | 'WGS84'

.. note::

  The ``coordExpr`` here is type of :ref:`Coordinate<type_coordinate>`. The BNF defination here is a simplified one. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:
::
  ego_init_position = (4.5, 214, -2);
  npc1_init_position = IMU(2, 4);
  pedestrian_init_position = WGS84(1.28,103.8);
  // The ego_init_position is set to 'ENU' by default.

.. note::

  Line 1 of the example specifies  the  initial  position  of  the  ego  vehicle  as (4.5, 214)  east-north  relative  to  the  map  origin.  Note  that the ENU coordinate  system  is  used  by  default  as  no  framewas  specified.  Line  2,  which  declares the IMU coordinatesystem, specifies the initial position of an NPC vehicle as (2,4) forward-left relative to the ego vehicle. Note that the positioncan  only  be  applied  after  the  ego  vehicle  has  been  formally generated. Line 3 defines  the  initial  position  of  a  pedestrianusing the WGS84 format. Note that this coordinate system can only be applied to a map that provides latitude and longitude information.


LanePositions
^^^^^^^^^^^^^^^^
``lanePositions`` are relative, and describe how far an object is from the starting point of its lane (whichis specified using a laneID). If the user are unclear about the map coordinates, the ``lanePositions`` are convenient.

BNF Defination:
::
  lanePosition ::= laneID '->' realExpr
  laneID ::= stringExpr

.. note::

  The ``realExpr`` here is type of :ref:`Real Value<type_realvalue>`. The BNF defination here is a simplified one. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:
::
  npc3_init_position = "lane_38" -> 0.0;
  //specifies that the initial position of NPC3 is at the start pointof the lane with ID lane38.


.. index:: ! real, ! value

.. _type_heading:

Heading
--------------

``Heading`` describes the orientation of an object by specifying  its  deviation  relative  to  a  predefined  direction.  In AVUnit, predefined directions can be the orientation of a lanepoint,  ego  vehicle,  NPC  vehicle,  and  or  a  pedestrian.  

BNF Defination:
::

  heading ::= angleVal unit ['related to' direction]
  direction ::= lanePosition | egoID | npcID | pedestrianID | 'EGO'

.. note::

  In  the grammar rule for heading, ``angleVal`` is the real angle value, unit is either degrees (deg) or radians (rad), and ``direction`` is  the  predefined  direction  that  the  angle  is  relative  to. The BNF defination here is a simplified one. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:
::

  heading1 = 10 deg related to EGO;
  heading2 = 40 deg related to npc1;


.. _type_state:

State 
--------------

``State``: Every  object  in  a  scene  has  some  internal  state  that contains their position, the current direction they are heading, and their current speed. When mutating the state of an object, the position must always be specified, but the heading and  speed  are  optional,  defaulting  to  zero  if  omitted. 

BNF Defination:
::

  state ::= '(' position[',' [heading] [','speed] ')'

.. note::

  The ``position`` here is type of :ref:`Position<type_position>`.The ``heading`` here is type of :ref:`Heading<type_heading>`. The ``speed`` here is type of :ref:`Real Value<type_realvalue>`. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:
::

  npc1_init_state = ("lane_1" -> 0.0);

.. note::

  The example assigns the position of NPC1 the starting point of lane "lane_1".  As  the  direction  headed  and  speed  are  omitted, AVUnit will assign default values. In this case, the NPC will be  oriented  at  0  degrees  from  the  direction  of lane_1,  and will have an initial speed of 0

.. _type_objectype:

ObjectType
--------------

``ObjectType``: Objects  on  the  road,  whether  they  are  NPCvehicles or static obstacles, are associated with an object type. AVUnit defines three different object types as follows:

* Vehicle  types  (vehicleType),  which  are  utilised  to  distinguish different  vehicles.  Vehicle  objects  can  take  a  specifictype  (e.g.  a  detailed  vehicle  model),  or  a  more  generictype  (e.g.  car,  bus,  van,  bicycle),  which  can  be  further customised according to colour and material.
* Pedestrian types (pedType), which describe the different kinds of human beings in the scene. Similar to vehicles, pedestrians  can  take  the  type  of  a  predefined  pedestrian model,  or  a  more  general  form  described  by  height  andcolour.
* Static types (staticType), which describe the shape and size  of  static  objects  (e.g.  boxes,  cones,  cylinders)  that serve as obstacles in the scene. 

BNF Defination:
::

  vehicleType ::= '(' type [,<color>] ')'
  type ::= specificType | genType
  pedType ::= specificType | pedGenType
  genType ::= car|bus|van|truck|bicycle|motorcycle|tricycle
  pedGenType ::= '(' height ',' color ')'
  staticType ::= sphere | box | cone | cylinder
  sphere ::= '(' sphere ',' naturalVal ')' 
  box ::= '(' box ',' size ')' 
  cone ::= '(' cone ',' size ')'
  cylinder ::= '(' cylinder ',' size ')'


.. note::

  The ``specificType`` here is type of :ref:`String<type_string>`. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:
::

  car_model = car;
  car_color = green;
  vehicle0_type = (car_model, car_color);
  ped0_type = "Howard";
  ped1_type = ( 1.8 , blue);
  ob0_type = (sphere, 2)


.. note::

  The example's first three lines describe the type of vehicle0. Vehicle0 is a green car. The fourth line illustrates that the ped0 is "Howard" (a predefined model of pedestrian). The fifth line defines a pedestrian whose height is 1.8m and in blue clothes. The last line describes a sphere whose diameter is 2 meters.

.. _type_weather:

Weather
--------------

``Weather``: AVUnit can  be  used  to  specify  combinations  of weather in  the  environment,  including  different  amounts  of sun,  rain,  fog,  wetness,  and  snow.  Weather  can  be  quantified using a real value between[0,1] or a set of labels (e.g. light,middle, heavy) which are mapped to predefined values. Note that different kinds of weather can exist at the same time.

BNF Defination:
::

  weather ::= '{' weatherStatements '}'
  weatherStatements ::= weatherStatement | weatherStatements , weatherStatement
  weatherStatement :: =kind ':' (continuousIndex | discreteLevel )
  kind ::= sunny | rain | snow | fog | wetness | cloudiness


.. note::

  The ``continuousIndex`` can be numbers ranging from 0 to 1. The ``discreteLevel`` is a set of labels(e.g. light,middle, heavy).  For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:
::

  weather = {rain: 0.5, wetness: 0.7, fog: heavy};


.. note::

  The example results in rain of medium intensity, as well as high levels of wetness and fog.

.. _type_time:

Time
--------------

``Time``: Time is an intrinsic component  of  the  environment.  It  is  specified  straight forwardly using hours (0-23) and minutes (0–59).

BNF Defination:
::

  timeExpr ::= time | timeExpr vectorOperator time
  time ::= hour ':' minute
  hour ::= digit |1 digit | 2 digit3
  digit3 ::= 0|…|3
  minute ::= digit5 digit
  digit5 ::= 0|…|5

.. note::

  Note that expressions(computation of time) over time are supported by AVUnit. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:
::

  time = 12:00;
   //This is for midday.



.. _type_motion:

Motion
--------------

``Motion``: Motion can be either uniform (``uniformMotion``) or based on  waypoints  (``waypointMotion``).  In  uniform  motion,  theNPC vehicle or pedestrian moves with a fixed speed as defined in  the state.  In  waypoint-based  motion,  the  object  moves towards the target position via a sequence of points as specified by the user. This type is useful when the want to speicfy the trace of a pedestrian or background vehicle.

BNF Defination:
::

  motion ::= uniformMotion | waypointMotion
  uniformMotion ::= 'Uniform(' state ')'
  waypointMotion ::= ('Waypoint' | 'WP' | 'W' ) '(' stateList ')'
  stateList ::= state | stateList ',' state

.. note::

  The ``state`` here is type of :ref:`State<type_state>`. For the complete version, please refer to :doc:`here<Overall_BNF>`.

Related Examples:
::

  motion1 = Uniform(npc_init_state);
  npc_state = (("lane_2" -> 0.0, , 1.0), ("lane_2" ->50.0, ,1.0), ("lane_2" -> 100.0, , 1.0), ("lane_3" -> 0.0, , 1.0), ("lane_3" -> 10.0, ,1.0), ("lane_4" -> 0.0, , 1.0),( "lane_4" ->50.0, , 1.0) ), ("lane_4" -> 100, , 0.0));
  motion2 = Waypoint(npc_state);
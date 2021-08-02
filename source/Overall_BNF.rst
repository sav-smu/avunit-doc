###############################
Overall BNF
###############################

Backus-Naur notation (shortly `BNF <https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form>`_) is a formal mathematical way to describe a language, (to describe the syntax of the programming languages). In this section, we will provide the detailed BNF file for reference. All the grammar rules of AVUnit can be traced in the BNF document.

The overall grammar rules can be devided into a few parts: :ref:`Basic Symbols and Arithmatic Operations<Basic_Arithmatic_Operations_BNF>` , :ref:`Scenario Description<Scenario_Part_BNF>` , :ref:`Specification Description<Specification_Part_BNF>` and :ref:`Overall Expression Assignment <Expression_Assignment_BNF>`. 

.. note::
    This section's document is more suitable for developers. If you are new to BNF, please refer to :doc:`Generate Scenarios <Generate_scenarios_by_AVUnit>` and :doc:`Build Specifications <Build_specifications_by_AVUnit>`.

.. ***********************
.. BNF Document
.. ***********************


.. _Basic_Arithmatic_Operations_BNF:

Basic Symbols and Arithmatic Operations
==================================

.. code::

	<letter> ::= a|…|z|A|…|Z
	<digit> ::= 0|<non zero digit>
	<non zero digit> ::= 1|…|9
	<symbol> ::= " " | "~" | "`" | "!" | "@" | "#" | "$" | "%" | "^" | "&" | "*" | "(" | ")" | "_" | "-" | "+" | "=" | "\" | "|" | "]" | "}" | " [" | "{" | ";" | ":" | "’" | "”" | "/" | "?" | ">" | "<" | "," | "."
	<identifier> ::= <first character>|<variable name>,<character>
	<character>::=<first character>|<digit>
	<first character>::=<letter>|“_”

	<operator1> ::= + | - 
	<operator2> ::= * | / |
	<operator3> ::= ^

	<string> ::= <input character>|<string><input character>
	//input character is the set of basic characters encoded by ASCII
	<input character> ::= <letter> | <digit> | <symbol>
	<real value> ::= [<signal>]<number>“.”<number>
	<signal> ::= + | -
	<number>::= <digit>|<number><digit>
	<coordinate> ::= “(”<real value>, <real value>[,<real value>] “)”
	<string expression> ::= <string>|<string expression> “+” <string expression>
	<real value expression> ::= <term1>|<real value expression> <operator1> <term1>
	<term1> ::= <term2> | <term1> <operator2> <term2>
	<term2> ::= <term3>|<term2> <operator3><term3>
	<term3> ::= <real value>| “(”real value expression“)”
	<coordinate expression> ::= <coordinate>|<coordinate expression> <operator1> <coordinate>

	<real value expression> ::= <term1> |<real value expression>




.. _Scenario_Part_BNF:

Scenario Part
==================================

.. code::

	<scenario> ::= CreateScenario”{”<map>;
	<ego vehicle>;
	<npc vehicles>;
	<pedestrians>;
	<obstacles>;
	{[<Env>]};”}”

	<map> ::= load(<map name>)
	<map name> ::= “””<string expression>”””

	<ego vehicle> ::= AV“(”[<parameter_list_ego>]”)”
	<parameter_list_ego> ::= <state>,<state>[,<vehicle type>]
		//first <state> is the initial state of the ego vehicle
		//second <state> is the target state of the ego vehicle
		//without <vehicle type>, use the default one.
	<state> ::= “(”<position>[,[<heading>][,<speed>]]”)”
	// the format can be: (position), (position, heading), (position, heading, speed), (position, ,speed)
	<position> ::= <coordinate position>|<lane position>
	<coordinate position> ::= [<coordinate frame>]<coordinate expression>
	<coordinate frame> ::= IMU | ENU | WGS84
		// IMU: vehicle coordinate system, right-forward-up, origin is the position of the vehicle
	// ENU: east-north-up, map origin is the origin of the coordinate
	// WGS84: world geodetic system
	// default coordinate frame: ENU

	<lane position> ::= <lane ID> “-““>” <real value expression>
	// <lane ID> -> <real value>: the distance of the point to the start point of <lane ID> is <real value>
	<lane ID> ::= <string>|[[<signal>]<number>”.”] [<signal>]<number>

	<heading> ::= <angle value> <unit> [related to <direction>]
	// default value: the direction to the lane where the position is on
	<angle value> ::= <real value expression>|<pi value>
	<pi value> ::= [<real value expression>]pi
	<unit> ::= deg | rad
	<direction> ::= <lane position>| <ego identifier>|<npc identifier>|<pedestrian identifier> |EGO

	<speed> ::= <real value expression>

	<vehicle type> ::= “(”<type>[,<color>]”)”
	<type> ::= <specific type>|<general type>
	<specific type> ::= “””<string expression>”””
	//model of the vehicle
	<general type> ::= car|bus|Van|truck|bicycle|motorbicycle|tricycle
	<color> ::= <color list>|<rgb color>
	<color list> ::= red|green|blue|black|white
	<rgb color> ::= “(“<rgb_value>,<rgb_value>,<rgb_value>“)”
	<rgb_value> ::= <digit>|<non zero digit><digit>|1<digit><digit>|2<digit_2><digit>|25<digit_3>
	<digit_2> ::= 0|…|4
	<digit_3> ::= 0|…|5

	<npc vhielces> ::= ”{”[<multiple npc vehicles>]”}”
	<multiple npc vehicles> = <npc vehicle>|<multiple npc vehicles>,<npc vehicle>
	<npc vehicle> ::= Vehicle”(”[<parameter_list_npc>]”)”
	<parameter_list_npc> ::= <state> [,[<vehicle motion>] [,[<state>] [,<vehicle type>]]]
	//first <state> is the initial state of a vehicle
	//second <state> is the target state of a vehicle
	//default motion: uniform form motion along paths
	<vehicle motion> ::= <uniform motion>|<waypoint motion>
	<uniform motion> ::= <uniform index>”(”< state>”)”
		// move with the given speed in <state>
	<uniform index> ::= uniform|Uniform
	<waypoint motion> ::= <waypoint index>”(”<state_list>”)”
	<waypoint index> ::= Waypoint|W|WP|waypoint|w|wp
	<state_list> ::= <state>|<state_list>,<state>

	<pedestrians> ::= ”{”[<multiple pedestrians>]”}”
	<multiple pedestrians> = <pedestrian>|<multiple pedestrians>,<pedestrian>
	<pedestrian> ::= Pedestrian”(”<parameter_list_ped>|*”)”
	<parameter_list_ped> ::= <state>[,[<pedestrian motion>][,[< state>][,<pedestrian type>]]]
	<pedestrian motion> ::= <uniform motion>|<waypoint motion> |<random motion>
	<random motion> ::= Random(<state>)
	<pedestrian type> ::= <specific type>|<pedestrian general type>
	<pedestrian general type> ::= “(”<height>,<color>”)”
	//color means the color of cloth
	<height> ::= <real value expression>

	<obstacles> ::= “{“[<multiple obstacles>]”}”
	<multiple obstacles> ::= <obstacle>|<multiple obstacles>,<obstacle>
	<obstacle> ::= Obstacle“(”[<parameter_list_obs>]”)”
	<parameter_list_obs> ::= <position>[,<shape>]
	<shape> ::= <sphere> | <box> | <cone> | <cylinder>
	<sphere> ::= “(“ sphere, <real value>”)”
	<box> ::= “(“box, <real value>, <real value>, <real value>”)”
	<cone> ::= “(“cone, <real value>, <real value>, <real value>”)”
	<cylinder> ::= “(“cylinder, <real value>, <real value>, <real value>”)”

	<Env> ::= Environment“(“[<parameter_list_env>]”)”
	<parameter_list_env> ::= <time expression>,<weather>
	<time expression> ::= <time>|<time expression> <vector operator> <time>
	<time> ::= <hour>:<minute>
	<hour> ::= <digit>|1<digit>|2<digit3>
	<digit3> ::= 0|…|3
	<minute> ::= <digit5><digit>
	<digit5> ::= 0|…|5

	<weather> ::= “{”<weather statements>”}”
	<weather statements> ::= <weather statement>|<weather statements>,<weather statement>
	<weather statement> ::= “‘”<kind>:“’” <weather continuous index>|<weather discrete level>;
	<kind> ::= sunny|rain|snow|fog|wetness|cloudiness
	//this should be set based on simulator capability
	<weather continuous index> ::= <proper fraction>|1
	<proper fraction> ::= 0.<number>
	<weather discrete level> ::= light|middle|heavy




.. _Trace_Part_BNF:

Trace Part
==================================

.. code::

	<trace declaration> ::= Trace <trace> = EXE(<scenario>)
		Note that the Trace object is defined as a dictionary in the following format:
		[{
			“time”: <time stamp>, “ego”: <ego state>,
			“perception”: {<agent identifier>: < perceived state>,
			<agent identifier>: < perceived state>, … ,
			traffic’:{‘light’: (light status, distance), ‘stop sign’: [stop sign status, distance], ‘speed limit’: [speed range, distance]}
		},	
		“truth”: {<agent identifier>: <ground truth state>,
			<agent identifier>: < ground truth state>, … ,
			‘traffic’:{‘light’: (light status, distance), ‘stop sign’: [stop sign status, distance], ‘speed limit’: [speed range, distance]}
		},
		}]
		Example: 
		[{
			‘time’: 0,
			’ego’: ((laneID, offset), orientation, velocity, acceleration),
			‘perception’: {
			‘npc’: ((laneID, offset), orientation, velocity, size),
			…
			‘pedestrian’: ((laneID, offset), orientation, velocity, size),
			…
			‘traffic’: {‘TrafficLight’: [red, 30.0], ‘StopSign’: [0, infinit], ‘SpeedLimit’: [(0, 60), 20.0]}},
		‘truth’: {
			‘npc’: ((laneID, offset), orientation, velocity, size),
			…
			‘pedestrian’: ((laneID, offset), orientation, velocity, size),
			…
			‘traffic’: {‘TrafficLight’: [status={None, red, green, yello}, distance to ego],
			‘StopSign’: [status={0, 1}, distance to ego],
			‘SpeedLimit’: [(lower bound, upper bounder),
			distance to ego]}
		},
		…
		]


.. _Specification_Part_BNF:

Specification Part
==================================


.. code::

	//declare <trace>	
	<trace declaration> ::= Trace <trace> = EXE(<scenario>)
	<ego declaration> ::= <ego identifier> = <ego vehicle>
	<npc declaration > ::= <npc identifier> = <npc vehicle>
	<pedestrian declaration> ::= <pedestrian identifier> = <pedestrian>
	<obstacle declaration> ::= <obstacle identifier> = <obstacle>
	<ego identifier> ::= <identifier>
	<npc identifier> ::= <identifier >
	<pedestrian identifier> ::= <identifier>
	<obstacle identifier> ::= <identifier>

	<ego state> ::= <trace>“[‘”ego“’]”
	<agent ground truth> ::= <trace>“[‘”truth“’]” “[‘”<agent identifier>“’]”
	<agent state> ::= <trace>“[‘”perception “’]”“[‘”<agent identifier>“’]”
	<agent identifier> ::= <ego identifier>|<npc identifier>|<pedestrian identifier>|<obstacle identifier>


	<compare operator> ::= ==|<|<=|>|>=|!=
	<temporal operator1> ::= G | F |X|G<I>|F<I>| X<I>
	<temporal operator2> ::= U | U<I>
	<I> ::= “[”<real value>”:”<real value>”]”
	<trace> |= <general assertion>|<traffic rules>

	<general assertion> ::= <first assertion> | <general assertion> “->” <first assertion>
	<first assertion> ::= <second assertion> | <first assertion> & <second assertion> |<first assertion> “|” <second assertion>  
	<second assertion> ::= <third assertion> | <second assertion> U <third assertion> | <second assertion> U<I> <third assertion>
	<third assertion> ::= <fourth assertion> | ~<third assertion> | <temporal operator1> <third assertion>
	<fourth assertion> ::= <atom predicate> | “(”general assertion“)”

	<atom predicate> ::= <spec expression> <compare operator> <spec expression>
	<spec expression> ::= <spec expresstion1> |<spec expression><operator1><spec expression1>
	<spec expression1> ::= <spec expression2>|<spec expression1> <operator2> <spec expression2>
	<spec expression2> ::= <atom expression>| “(”spec expression“)”

	<atom expression> ::= <perception difference expression >|<distance expression>|<velocity expression>|<speed expression>|<acceleration expression>|<real value>

	<perception difference expression> ::= diff “(”<agent state>, <agent ground truth>“)”

	<distance expression> ::= dis“(”<position element>, < position element >“)”
	<position element> ::= <object state> |<position>
	<object state> ::= <ego state>|<agent state>|<agent ground truth>

	<velocity expression> ::= vel“(”<velocity element>, <velocity element>“)”
	<velocity element> ::= <object state> |<velocity>
	<velocity> ::= <coordinate>

	<speed expression> ::= spd“(”<speed element>, <speed element>“)”
	<speed element> ::= <object state> |<speed>

	<acceleration expression> ::= acc“(”<acceleration element>, <acceleration element> “)”
	<acceleration element> ::= <object state> |<acceleration >
	<acceleration> ::= <coordinate>

.. _Expression_Assignment_BNF:

Overall Expression Assignment
==================================

.. code::

	<variable declaration> ::= <identifier> = <expression>
	<expression> ::= <string>|<real value>|<number>|<coordinate>|<string expression>|
	<real value expression>|<coordinate expression>|<scenario>|<map>|<map name>|
	<ego vehicle>|<state>|<position>|<lane position>|<lane ID>|<heading>|<angle value>|
	<direction>|<speed>|<vehicle type>|<type>|<color>|<npc vhielces>|<npc vehicle>|
	<vehicle motion>|<state_list>|<pedestrians>|<pedestrian>|<pedestrian motion>|
	<pedestrian type>|<height>|<obstacles>|<obstacle>|<shape>|<Env>|<time expression>|
	<time>|<weather>|<weather statement>|<weather continuous index>|<weather discrete level>|
	<general assertion>|<atom predicate>|<atom expression>|<perception difference expression>|
	<auto perception difference expression>|<distance expression>|<auto distance expression>|
	<position element>|<velocity expression>|<auto velocity expression>|<velocity element>|
	<velocity>|<speed expression>|<auto speed expression>|<speed element>|< acceleration expression>|
	<auto acceleration expression>| acceleration element> |<acceleration>

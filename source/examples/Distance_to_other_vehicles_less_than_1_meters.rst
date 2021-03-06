*************
No Collision
*************

In this section, we will show how easy it is to generate a scenario and make assertions: no collision should happen.  We will start with an scenario defination then make suitable assertions.

Scenario Generation
===================

In the following scenario, we will show how easy it is to describe a scenario. The initial state, target state, and vehicle type of ego vehicle (``ego_vehicle``) is defined in few lines.
Also, we set the state and trajetories of NPC vehicles(``npc1``, ``npc2``,``npc3``) and pedestrians(``ped``) in details easily. There is no ``obstacles`` defined in this scenario. The environment (``evn``) here sets the time of middle day and make it a raining day (``rain``:0.5, ``snow``: 0.1, ``wetness``: heavy). 

::

	map_name = "san_francisco";

	car_model = "gt_sensors";
	car_color = green;
	ego_init_position = "lane_39"->10;
	ego_init_state = (ego_init_position);
	ego_target_position = (552898.9957, 4182687.8989561526); //default coordinate frame is ENU    10.1325969696045,
	ego_target_state = (ego_target_position);
	vehicle_type = (car_model, car_color);
	ego_vehicle = AV(ego_init_state, ego_target_state, vehicle_type);

	heading1 = 10 deg related to ego_vehicle;
	npc_init_state = ("lane_39"->20, , 6.0);
	npc1_type = "Sedan";
	npc_state1 = (("lane_759"->1, ,6), ("lane_759"->20, ,3),("lane_77"->1, ,6), ("lane_77"->30, ,5));
	npc1_destination = ("lane_77"->150);
	npc1 = Vehicle(npc_init_state, Waypoint(npc_state1), npc1_destination, (npc1_type));

	heading2 = 50 deg related to npc1;
	npc_init_state2 = ("lane_38"->0.0, ,1.0);
	npc_state2 = (("lane_38"->1.0, ,10.0), ("lane_38"->50.0, , 4.0), ("lane_755"->0.0, , 4.0), ("lane_41"->10.0, ,5.0));
	npc_target_state2 = ("lane_41"->100.0);
	npc2_type = "SUV";
	npc2 =Vehicle(npc_init_state2, Waypoint(npc_state2), npc_target_state2, (npc2_type));

	npc_init_state3 = ("lane_779"->0.0, , 1.0);
	npc3 = Vehicle(npc_init_state3);

	npc = {npc1, npc2, npc3};

	time = 12:00;
	weather = {rain:0.5, snow: 0.1, wetness: heavy};

	evn = Environment(time, weather);

	ped_position = (553020.54, 4182693.69);
	ped_heading = 90 deg related to "lane_38"->30;
	ped_init_state= (ped_position, ped_heading, 1.0);
	ped_state_list = (((553020.54,4182693.69), ped_heading, 1.0),((553023.26, 4182671.82), ped_heading, 1.0));
	ped_motion = Waypoint(ped_state_list);
	ped = Pedestrian(ped_init_state, ped_motion);


	scenario0 = CreateScenario{load(map_name);
				        ego_vehicle;
				        npc; // no other vehicles;
				        {ped}; // no pedestrians;
				        {}; // no obstacles;
				        evn; // default environment
	};


Specification Defination
=========================

In the following specification defination, we describe a "no-collision" specification, which means the distance of ego vehicle and npc vehicles(also pedestrians) should be larger than a real value(here is 1 meter).

::

	Trace trace = EXE(scenario0);

	ego_vehicle_state= trace[ego];
	npc_vehicle1= trace[perception][npc1];
	npc_vehicle1_truth= trace[truth][npc1];
	npc_vehicle2 = trace[perception][npc2];
	npc_vehicle2_truth = trace[truth][npc2];
	npc_vehicle3= trace[perception][npc3];
	npc_vehicle3_truth =  trace[truth][npc3];

	// assertion related to npc_vehicle1
	dis1 = dis(ego_vehicle_state, npc_vehicle1_truth);
	dis2 = dis(ego_vehicle_state, npc_vehicle2_truth);
	dis3 = dis(ego_vehicle_state, npc_vehicle3_truth);
	vel1 = vel(ego_vehicle_state, npc_vehicle1_truth);
	vel2 = vel(ego_vehicle_state, npc_vehicle2_truth);
	vel3 = vel(ego_vehicle_state, npc_vehicle3_truth);
	spd1 = spd(ego_vehicle_state, npc_vehicle1_truth);
	spd2 = spd(ego_vehicle_state, npc_vehicle2_truth);
	spd3 = spd(ego_vehicle_state, npc_vehicle3_truth);
	error1 = diff(npc_vehicle1, npc_vehicle1_truth);
	error2 = diff(npc_vehicle2, npc_vehicle2_truth);
	error3 = diff(npc_vehicle3, npc_vehicle3_truth);

	statement1 = {dis1} >= {1.0};
	statement2 = {dis2} >= {1.0};
	statement3 = {dis3} >= {1.0};


	// assertion 0
	trace |= G (statement1 & statement2 & statement3);
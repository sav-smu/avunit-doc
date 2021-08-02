###############################
Introduction to AVUnit
###############################


***********************
What is AVUnit?
***********************

AVUnit (think jUnit for Java) is a high-level descriptive language designed for systematically testing autonomous vehicles (AV). AVUnit is agnostic to existing AV  simulators. It consists  of a language for descripting scenes and scenarios (e.g. changing pedestrian behaviour after waypoints) and a rich assertion language for specifying correctness of AV's behaviors. AVUnit further supports multiple algorithms for automatically generating test cases accordingly to the descriped scenarios and assertions. In this document, we present details of different components of AVUnit systematically. 

***********************
Why we need AVUnit?
***********************

AVs are  set  to  play  an  essential role  in  significantly reducing  traffic  congestion  and accidents in  intelligent  transportation  systems.  To  provide  a  high  level of  automation,  AVs  are  controlled  by  autonomous  driving systems  (ADSs),  which  take  over  various  driving  tasks  from human  drivers  by  interpreting  and  acting  on  sensory  information  (e.g.  lidar  data).  Before  AVs  can  be  fully  deployed on public roads, however, it is imperative that their ADSs are comprehensively tested to ensure they (almost) always behave correctly and safely, even in rare or unlikely scenarios, e.g., when pedestrians and human-controlled vehicles behave unpredictably.

The  state  of  the  art  in  AV  testing  can  be  broadly  dividedinto  two  categories: real-world  testing and simulator-based testing. Extensive and rigorous real-world road testing for AVs is  necessary,  and  often  used  as  a  measure  of  the  technology’s progress, e.g. the number of kilometres covered without human  intervention.  However,  it  is  also  costly,  risky,  and most  importantly,  insufficiently  covers  all  the  situations  thatAVs must be able to safely react to (e.g. accident scenarios).This  motivates  the  need  for  simulator-based  testing,  wheredevelopers  and  safety  engineers  can  systematically  assess ADSs against a broader range of scenarios that may occur on the road.

For   simulation-based   testing,   researchers   and   engineersrely   on   high-fidelity   simulators   such   as   `LGSVL <https://www.svlsimulator.com/docs/>`_   and   `CARLA <https://carla.readthedocs.io/en/latest/>`_, which can simulate not only sensors and vehicle dynamics   but   also   controlled   traffic   scenarios.   While equipped  with  APIs  for  configuring  scenarios,  these  differ significantly   between   simulators,   and   are   not   specifically designed  to  support  systematic  testing  and  analysis. With the help of AVUnit, we can not only configure scenarios across different simulators efficiently but also evaluate the correctness of traces due to the specification the user defined. 

.. ***************************
.. What is AVUnit used for?
.. ***************************

.. To facilitate ADS testing, we propose AVUnit, a script language specifically designed for ADS testing, which can describe not only various scenarios but also different properties in terms of STL. 



*******************************
What can you do with AVUnit?
*******************************

Basic functions of AVUnit:

* Describe Different Scenes
* Define Different specifications

Here are a few possible extensions of AVUnit:

* Build Fuzzing algorithm to generate "critical" scenes
* Connect to different simulators and autonomous  driving systems (ADSs) for evaluation of different ADSs
* Find "bugs" of ADSs
* Participate in the development process of ADSs


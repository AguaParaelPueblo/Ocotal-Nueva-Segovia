#Head loss check between the sed tanks and filter for Ocotal
 The design produced for Ocotal incorrectly draws one of three sed-to-filter pipes such that it does not connect to the filter inlet channel:   

![drawing_error](images/sed-to-fi-mistake.jpg)  

The easiest thing is just to delete that pipe, but that could lead to too much head loss between the sed exit channel and filter inlet. The maximum allowable head loss is 16cm.
```python
# %%
from aide_design.play import *

Q_plant = 60*u.L/u.s
pipe_ND= 8*u.inch
length = 2.5*u.m
K_minor = 2*exp.K_MINOR_EL90+exp.K_MINOR_PIPE_ENTRANCE+exp.K_MINOR_PIPE_EXIT
temp = 20*u.degC
SDR = 26

headloss_two = pc.headloss(Q_plant/2, pipe.ID_SDR(pipe_ND,SDR), length, pc.viscosity_kinematic(temp), mat.PIPE_ROUGH_PVC, K_minor)

headloss_three = pc.headloss(Q_plant/3, pipe.ID_SDR(pipe_ND,SDR), length, pc.viscosity_kinematic(temp), mat.PIPE_ROUGH_PVC, K_minor)

print('The head loss for two pipes is {}.'.format(headloss_two))
print('The head loss for three pipes is {}.'.format(headloss_three))
```
The head loss for two pipes is 0.1575 meter.  
The head loss for three pipes is 0.0702 meter.  

We need to use all three pipes in the case of Ocotal. 

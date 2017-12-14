#Aerator Design for Ocotal

```python
# %%
from aide_design.play import *
import matplotlib.pyplot as plt
from scipy import interpolate
Q_plant = 60*(u.L/u.s)
```
## Cascade

![cascade_weir_dims](images/cascade_weir_dims.JPG)

```python
# %%
#max unit flow from TU Delft paper
Q_unit_casc_max = 100*(u.m**3/u.hour)/u.m
L_weir_total = (Q_plant/Q_unit_casc_max).to(u.m)
L_weir_total


DO_initial = 2*(u.mg/u.L)
DO_sat = 8*(u.mg/u.L) #approximate average sat. conc. based on temperature

#K values from table in TU Delft paper
h = np.array([0,0.2,0.4,0.6,0.8,1.0,1.2,10])
K = np.array([0,.14,.25,.36,.46,.51,.55,.99])
interp_k = interpolate.interp1d(h,K,'slinear',fill_value='extrapolate')

#assume a reasonable total height, determine the number of steps to divide that
#height into given the influent and desired effluent DO concentration

h_total = 5*u.m
n_steps_array = np.array(np.arange(1,10,1))
h_steps_array = h_total/(n_steps_array+4/3)
#Get K values from interpolation
K_array = interp_k(h_steps_array)

h_steps_array
K_array
#function for final DO concentration given
def DO_final(DO_sat,DO_initial,K,n):
  DO = DO_sat-(DO_sat-DO_initial)*(1-K)**n
  return DO

DO_final_list = []

#get the final dissolved oxygen content for each step height
for K, n in zip(K_array, n_steps_array):
  DO_final_list.append(DO_final(DO_sat,DO_initial,K,n))


i = DO_final_list.index(max(DO_final_list))
n_steps = n_steps_array[i]
h_weir = h_steps_array[i]
n_steps

#Finding dimensions of the troughs
t_jet = ((Q_plant**2/(L_weir_total**2*pc.gravity))**(1/3)).to(u.m)
v_jet = Q_plant/(L_weir_total*t_jet)

ti_jet = np.sqrt((2*h_weir)/pc.gravity)
width_jet = v_jet*ti_jet
width_trough = 2*width_jet.to(u.m)
depth_trough = (2/3)*h_weir

print('The final dissolved oxygen concentration is {}'.format(DO_final_list[i]))
print('The horizontal length of the aerator is {}'.format(L_weir_total))
print('The height the water falls per step (h) is {}'.format(h_weir))
print('The number of steps is {}'.format(n_steps))
print('The minimum width (B) of each step is {}'.format(width_trough))
print('The minimum depth (H) of each step is {}'.format(depth_trough))
```
## Multiple-tray Tower Aerator

# Using Python and Running it With Hydrogen in Markdown

## Running Code With Hydrogen
1. Below this, I've copied the code I wrote for the [Writing Python](https://github.com/AguaClara/aguaclara_tutorial/wiki/Writing Python) tutorial. You should familiarize yourself with the different Hydrogen commands using this code. For the first line, use `Hydrogen: Run` (`Cmnd + Enter`).
2. For the second line, use `Hydrogen: Run and Move Down` (`Shift + Enter`).
3. For the remaining code, highlight it with your cursor and use `Hydrogen: Run`. What is the difference between the three?

```python
from aide_design.play import*

xArray = u.Quantity(np.arange(0.1, 0.5, 0.01), u.m)

@u.wraps(None, [u.m / u.s, u.m, u.m ** 2 / u.s], False)
def re_flat_plate(velocity, dist, nu):
  """This function calculates the Reynolds Number for flow past a plate using fluid velocity, plate length, and kinematic viscosity."""
  return (velocity * dist / nu)

plt.plot(xArray, 5 * xArray / np.sqrt(re_flat_plate(1, xArray, pc.viscosity_kinematic(293 * u.kelvin))), '-', label = 'Blasius Solution')
plt.xlabel('Distance From Leading Edge (Meters)')
plt.ylabel('Boundary Layer Thickness (Meters)')
plt.title('Blasius Solution for Water at 293 K')
plt.minorticks_on()
plt.grid(which = 'major')
plt.grid(which = 'minor')
plt.legend(loc = 'lower right', ncol = 1)
plt.show()
```

## Python Basics
These questions are meant to test what you've learned from the Python Basics tutorial. If you need help answering a question, refer there first and use other online resources before seeking a Subteam Lead or RA. Be sure to run all your code with Hydrogen. When you code, make sure your using proper [variable naming](https://github.com/AguaClara/aide_design/wiki/Variable-Naming) and [coding standards](https://github.com/AguaClara/aide_design/wiki/Standards)

1. Write a conditional statement with 3 conditions: when x is 10, when x is 1, and when x is anything other than 1 or 10. For each condition, have your code print what the value is or isn't.

<!--- Fill you answer here. --->
```Python
def new_function(x):
  if x == 10:
    print('x is 10')
  elif x == 1:
    print('x is 1')
  print ('x is '+ str(x))
  ```



2. Write a `for` loop that takes a variable with an initial value of 0, and adds the current index to the previous value of that variable (i.e. you variable should grow in size every iteration). Perform the iteration 20 times, and have the final value be printed at the end.

<!--- Fill you answer here. --->

```python
def add_i(j):
  j = 0
  for i in range(0,20):
    j += i
    print(j)


```




3. Using the NumPy package and `unit_registry`, calculate the value of sin(4) meters, and use the sigfig function from the unit unit_registry module in aide_design to get your answer to 2 sig-figs. *(Hint: You will need to import these packages. Remember how to do that?)*
```python
from aide_design.play import*

x = np.sin(4)
rounded_x = np.around(x, decimals=2)
print(rounded_x)
```


4. Create a `list` of length 5, and verify the length of your list. Once you've done that, turn your `list` into an `array` and apply units of meters to it. After that, create a 5x5 `array`, extract the middle row and middle column. Verify the size of your 2D `array` and apply units of liters to it.

```python
myList = [0, 1, 2, 3, 6]
len(myList)

myArray = np.array(myList)
myArrayUnits = myArray * u.m

my2DArray = np.array([[1 ,2, 3, 4, 5], [4, 5, 6, 7, 8], [7, 8, 9, 10, 11], [1, 2, 3, 4, 5], [0, 1, 2, 3, 4]])
my2DArray[:,2]

my2DArray[2,:]

np.size(my2DArray)
my2DArrayu = my2DArray * u.L
```




5.  One of the most famous equations for a particle diffusing through a liquid at low Reynolds Number is the Stokes-Einstein Equation where k<sub>B</sub> is the Boltzmann constant, T is the temperature in Kelvin, eta is the dynamic viscosity in kg/(m*s), and r is the particle radius. Write a function that takes a temperature in Kelvin, a particle radius in meters, and a viscosity of water to calculate the diffusion coefficient D.

    Since this requires the Boltzmann Constant from SciPy, I've started the code for you with an import. Add a function call at the end of your code block and put some numbers into the inputs. Run your code with Hydrogen.

    *(Hint: You'll want to make sure Temperature input is turned into Kelvin and radius input is turned into meters. Your answer should also be in base units How might you do this? Check back to the Python Basics tutorial where I wrote an Ideal Gas function)*

$$ D = \frac{k_BT}{6\pi\eta r} $$

```python
from scipy.constants import Boltzmann as kB_sc # I've imported the unitless value for kB from SciPy

kB = kB_sc * u.joule / u.kelvin # I've given kB units for you in J/K; you can use the kB variable to give you Boltzmann's constant with units
import math

def stokes_einstein(T, eta, r):
  T_new = T.to(u.kelvin)
  eta_unit = eta.to(u.kilogram/ (u.meter * u.second))
  r_new = r.to(u.meter)
  kB = kB_sc * u.joule / u.kelvin
  kB_base = kB.to_base_units()
  D = (kB_base*T_new)/(6* np.pi * eta_unit * r_new)
  return D.to_base_units()

stokes_einstein(0 * u.celsius,  5 * u.kilogram/ (u.meter * u.second), 2 * u.meter)

```

6. You have a pipe with a radius of 0.2 m with water flowing in it at 2 m<sup>3</sup>/s. You want to see how the Reynolds Number changes as viscosity changes due to a change in temperature from 0 to 200<sup>o</sup>C. Create a plot of Reynolds Number against Temperature in Kelvin to show a relationship. Make sure your plot has a title, labeled axes, and axes grid. You can use functions from `physchem` like `pc.re_pipe` and `pc.viscosity_kinematic`. *(Hint: Make an array of temperatures to input into the `pc.viscosity_kinematic` function)*. Make sure to save you plot to your images folder in your personal repository, and display it below using `plt.show()` and a relative file path to the image.

```python
temp = []
for k in range (0,201):
  temp.append(k)

#Convert temperature in Celsius to Kelvin
tempArray = (np.array(temp) + 273) * u.kelvin
tempArray

viscosity = []
for i in range(len(tempArray)):
  calc_visc = pc.viscosity_kinematic(tempArray[i])
  viscosity.append(calc_visc)
viscosity

re_number = []
q = 2 * (u.meter ** 3 / u.s) #Flow rate in m^3/s
d = 0.2 * 2 * u.meter #Pipe diameter in m

for j in range(len(viscosity)):
  re = pc.re_pipe(q,d,viscosity[j])
  re_number.append(re)

re_number
plt.plot(tempArray, re_number, '.', label = 'Reynolds Number')
plt.xlabel('Temperature (Kelvin)')
plt.ylabel('Reynolds Number')
plt.title('Relationship of Reynolds Number with Temperature')
plt.minorticks_on()
plt.grid(which = 'major')
plt.grid(which = 'minor')
plt.legend(loc = 'lower right', ncol = 1)
plt.tight_layout()
plt.savefig('./Images/ReynoldsNumber.png')
plt.show()
```

# GitHub Basics
Congratulations! You've completed this interactive tutorial. Now all you need to do is save your work and put it on your personal repository. Toggle the Git Tab using `Cntrl + Shift + 9`.

1. Stage your changes.
2. In your commit message write that you've completed the tutorial.
3. Commit your changes and push them.

# ShockResponseSpectrum
Algorithm to generate a shock response spectrum given an input acceleration-time data.

# SRS Calculation

The SRS is specifically a plot of the peak absolute accelerations of a structure against a range of hypothetical natural frequencies of interest. This gives us a the approximate worst case accelerations experienced by the structure.

We will first model the system as a Single-Degree-Of-Freedon Spring-Mass-Damnper system. We can derive a 2nd order ODE as follows.

<p align="center">
  <img src="https://render.githubusercontent.com/render/math?math=%5Cddot%7Bz%7D%2B2%5Czeta%5Comega_0%5Cdot%7Bz%7D%2B%5Comega_0%5E2z%3D-%5Cddot%7Bu%7D">
</p>

The damping ratio <img src="https://render.githubusercontent.com/render/math?math=%5Czeta"> is given by the equation <img src="https://render.githubusercontent.com/render/math?math=%5Cfrac%7B1%7D%7B2Q%7D">, where <img src="https://render.githubusercontent.com/render/math?math=Q"> is set to <img src="https://render.githubusercontent.com/render/math?math=10"> for large metallic bodies in SRS calculations by convention. <img src="https://render.githubusercontent.com/render/math?math=z"> and <img src="https://render.githubusercontent.com/render/math?math=u"> are the relative displacement of the structure to the base and the base displacement respectively. 

The absolute acceleration <img src="https://render.githubusercontent.com/render/math?math=%5Cddot%7Bx%7D"> of the structure is given by the following relation.

<p align="center">
  <img src="https://render.githubusercontent.com/render/math?math=%5Cddot%7Bx%7D%3D%5Cddot%7Bz%7D%2B%5Cddot%7Bu%7D">
</p>

First we find <img src="https://render.githubusercontent.com/render/math?math=z(t)"> by solving the 2nd order ODE with numerical methods, as we only have pairwise values of <img src="https://render.githubusercontent.com/render/math?math=%5Cddot%7Bu%7D"> and <img src="https://render.githubusercontent.com/render/math?math=t">.

The method chosen to for the numerical integration is Runga Kutta's 4th Order Method (RK4). Given the initial value problem as such;

<p align="center">
<img src="https://render.githubusercontent.com/render/math?math=%5Cfrac%7Bdy%7D%7Bdx%7D%3Df(t%2C%20y)%2Cy(t_0)%3Dy_0">
</p>

We can solve for successive <img src="https://render.githubusercontent.com/render/math?math=y"> values by the following;

<p align="center">
  <img src="https://render.githubusercontent.com/render/math?math=y_%7Bn%2B1%7D%20%3D%20y_n%20%2B%20%5Cfrac%7B1%7D%7B6%7Dh(k_1%20%2B%202k_2%20%2B%202k_3%20%2B%20k4)">
</p>
<p align="center">
  <img src="https://render.githubusercontent.com/render/math?math=t_%7Bn%2B1%7D%20%3D%20t_n%20%2B%20h">
</p>

Where <img src="https://render.githubusercontent.com/render/math?math=h"> is the timestep and,

<p align="center">
  <img src="https://render.githubusercontent.com/render/math?math=k_1%20%3D%20f(t_n%2C%20y_n)">
</p>

<p align="center">
  <img src="https://render.githubusercontent.com/render/math?math=k_2%20%3D%20f(t_n%20%2B%20%5Cfrac%7Bh%7D%7B2%7D%2C%20y_nh%5Cfrac%7Bk1%7D%7B2%7D)">
</p>

<p align="center">
  <img src="https://render.githubusercontent.com/render/math?math=k_3%20%3D%20f(t_n%20%2B%20%5Cfrac%7Bh%7D%7B2%7D%2C%20y_nh%5Cfrac%7Bk2%7D%7B2%7D)">
</p>

<p align="center">
  <img src="https://render.githubusercontent.com/render/math?math=k_4%20%3D%20f(t_n%20%2B%20h%2C%20y_n%20%2B%20hk3)">
</p>

RK4 is a much more accurate numerical integration method as compared to the famouse Euler's Method. However, RK4 can only be used to solve 1st order ODEs. We need to first transform our 2nd order ODE into a form we can work with.

State-Space Representation are mathematical models of physical systems as a set of input, output and state variables related by 1st order differentials.

We let <img src="https://render.githubusercontent.com/render/math?math=z_1%20%3D%20z"> and <img src="https://render.githubusercontent.com/render/math?math=z_2%20%3D%20%5Cdot%7Bz%7D"> be the the relative displacement and relative velocities, they are the minimum number of states required to quantify the 2nd order ODE. We then represent the dynamics (derivatives) of these states in terms of the states themselves, as well as the input <img src="https://render.githubusercontent.com/render/math?math=%5Cddot%7Bu%7D">.
  
<p align="center">
  <img src="https://render.githubusercontent.com/render/math?math=%5Cdot%7Bz_1%7D%20%3D%20z_2">
</p>

<p align="center">
  <img src="https://render.githubusercontent.com/render/math?math=%5Cdot%7Bz_2%7D%20%3D%20-2%5Czeta%5Comega_0%5Cdot%7Bz%7D%20-%20%5Comega_0%5E2z%20-%5Cddot%7Bu%7D">
</p>

The State-Space Model can be represented in matrix form.
  
<p align="center">
  <img src="https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bbmatrix%7D%20%5Cdot%7Bz_1%7D%20%5C%5C%20%5Cdot%7Bz_2%7D%20%5Cend%7Bbmatrix%7D%0A%3D%0A%5Cbegin%7Bbmatrix%7D%200%20%26%201%20%5C%5C%20-%5Comega_0%5E2%20%26%20-2%5Czeta%5Comega_0%20%5Cend%7Bbmatrix%7D%0A%5Cbegin%7Bbmatrix%7D%20z_1%20%5C%5C%20z_2%20%5Cend%7Bbmatrix%7D%0A%2B%0A%5Cbegin%7Bbmatrix%7D%200%20%5C%5C%20-1%20%5Cend%7Bbmatrix%7D%5Cddot%7Bu%7D">
</p>

Define <img src="https://render.githubusercontent.com/render/math?math=y%20%3D%20%5Cbegin%7Bbmatrix%7D%20z_1%20%5C%5C%20z_2%20%5Cend%7Bbmatrix%7D">, we can get that our state-space model follows <img src="https://render.githubusercontent.com/render/math?math=%5Cdot%7By%7D%20%3D%20Ay%20%2B%20B%5Cddot%7Bu%7D">, the exact form we are able to apply RK4 on. With initual conditions of <img src="https://render.githubusercontent.com/render/math?math=y_0"> being <img src="https://render.githubusercontent.com/render/math?math=%5Cbegin%7Bbmatrix%7D%200%20%5C%5C%200%20%5Cend%7Bbmatrix%7D">, seeing as how the displacements and velocities of both the base and structure is <img src="https://render.githubusercontent.com/render/math?math=0">. We can find <img src="https://render.githubusercontent.com/render/math?math=z"> by applying <img src="https://render.githubusercontent.com/render/math?math=z%20%3D%20%5Cbegin%7Bbmatrix%7D%201%20%26%200%20%5Cend%7Bbmatrix%7Dy">.

Employing RK4 allows us to evaluate for pairwise values of <img src="https://render.githubusercontent.com/render/math?math=z"> and <img src="https://render.githubusercontent.com/render/math?math=t">. We're interested in finding <img src="https://render.githubusercontent.com/render/math?math=%5Cddot%7Bx%7D(t)">. Going back to the equation <img src="https://render.githubusercontent.com/render/math?math=%5Cddot%7Bx%7D%20%3D%20%5Cddot%7Bz%7D%20%2B%20%5Cddot%7Bu%7D">, we just need to find <img src="https://render.githubusercontent.com/render/math?math=%5Cddot%7Bz%7D"> and add it to<img src="https://render.githubusercontent.com/render/math?math=%5Cddot%7Bu%7D">. Again, we need to use a numerical method to find calculate the derivative. This would be done by taking successive differences between <img src="https://render.githubusercontent.com/render/math?math=z(i)"> and <img src="https://render.githubusercontent.com/render/math?math=z(i%2B1)">, keeping in mind that the the size of our <img src="https://render.githubusercontent.com/render/math?math=%5Cdot%7Bz%7D"> array would be <img src="https://render.githubusercontent.com/render/math?math=1"> less than the <img src="https://render.githubusercontent.com/render/math?math=z"> array. Repeating for the 2nd derivative, lastly, summing <img src="https://render.githubusercontent.com/render/math?math=%5Cddot%7Bz%7D"> and <img src="https://render.githubusercontent.com/render/math?math=%5Cddot%7Bu%7D"> gives us <img src="https://render.githubusercontent.com/render/math?math=%5Cddot%7Bx%7D">. We can now store the maximum value of <img src="https://render.githubusercontent.com/render/math?math=%5Cddot%7Bx%7D"> against the <img src="https://render.githubusercontent.com/render/math?math=%5Comega_0"> value used to calculate this absolute acceleration. We repeat this over the range of natural frequencies of interest. The resulting plot of the peak <img src="https://render.githubusercontent.com/render/math?math=%5Cddot%7Bx%7D"> values against <img src="https://render.githubusercontent.com/render/math?math=%5Comega_0"> gives us the SRS.

A sample implementation of the above described algorithm is given below.

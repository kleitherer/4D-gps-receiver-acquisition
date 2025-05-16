# 4D-gps-receiver-acquisition

# EE259 (Principles of Sensing for Autonomy): PSET 1, Problem 3 #

Satellite Positioning and GPS Receiver Acquisition. This problem (a-f) implements a non-perturbed GPS range and velocity acquisition given ephemeris data and Doppler frequency estimates. Parts C through F are included in the jupypter notebook.

## Part A ##
Find an expression for r, the distance of the satellite from the center of the Earth, in terms of eccentric anamoly E and orbital parameters a and e. 

$$
r = a(1 - e \cos E)
$$

## Part B ##
Find cos ν and sin ν, which, when multiplied by the distance r, give us an R2 representation of the satellite position in the orbital plane with the new origin being the center of Earth F. Please express
everything in terms of eccentricity e and the eccentric anomaly E.

$$
\cos v = \frac{\cos E - e}{1 - e \cos E}, \quad
\sin v = \frac{\sqrt{1 - e^2} \sin E}{1 - e \cos E}
$$

$$
\mathbf{p}_{orbital} =
\begin{bmatrix}
r \cos v \
r \sin v \
0
\end{bmatrix}
$$

## Part C ##
Given nine parameters of ephemeris data, find the five satellite's positions in the ECEF frame and plot their positions with the provided function to confirm the satellites are close to nominal trajectories. Below is the process to my solution, but it essentially follows this structure:
In order to find a satellite, we need to:
1) know the shape and orientation of its orbit 
2) know where it is within that orbit 
3) convert that position from the orbital plane to 3D Earth-Centered coordinate system

First, solve for the eccentric anomaly $E$ using Kepler's Equation:

$$
M = E - e \sin E
$$

This is solved using the Newton-Raphson method:

$$
E_{n+1} = E_n - \frac{E_n - e \sin E_n - M}{1 - e \cos E_n}
$$

Once you have $E$, calculate the satellite’s position in the orbital plane:

Radius:
 
$$
r = a(1 - e \cos E)
$$

True anomaly (from $E$):
 
$$
\cos v = \frac{\cos E - e}{1 - e \cos E}, \quad
\sin v = \frac{\sqrt{1 - e^2} \sin E}{1 - e \cos E}
$$

Plug everything into the vector to get the satellite position in orbital plane:

$$
\mathbf{p}_{orbital} =
\begin{bmatrix}
r \cos v \
r \sin v \
0
\end{bmatrix}
$$


Rotate orbital coordinates into the Earth-Centered Earth-Fixed (ECEF) frame using orbital parameters $\omega$ (argument of perigee), $i$ (inclination), and $\Omega$ (right ascension):

$$
\mathbf{p}{ECEF} = R_z(-\Omega) R_x(-i) R_z(-\omega) \cdot \mathbf{p}{orbital}
$$


## Part D ##
Using the satellites' positions from part c and the given pseudoranges within the ephemeris data, find the receiver's position and its clock bias. 

The measured pseudorange $\rho_i$ between the receiver and satellite $i$ is modeled as:

$$
\rho_i = | \mathbf{p}_r - \mathbf{p}_i | + c \cdot t_d
$$

Where:
	•	$\mathbf{p}_r = [x, y, z]$ is the receiver’s position
	•	$\mathbf{p}_i$ is the satellite position
	•	$t_d$ is the receiver’s clock bias
	•	$c$ is the speed of light

Solve the nonlinear system using the Gauss-Newton method to estimate $\mathbf{p}_r$ and $t_d$ by minimizing:

$$
\min_{x} | f(x) |^2
$$

where $x = [x_r, y_r, z_r, t_d]$ and $f(x)$ is the vector of residuals between the measured and predicted pseudoranges.

## Part E ##
This subsection asked me to derive the velocity expression for the satellites by taking the time derivative of the expression given above for the satellite position in
the orbital plane po.

$$
\mathbf{v} =
\begin{bmatrix}
-\sqrt{\frac{\mu}{a^3}} \cdot \frac{a \sin E}{1 - e \cos E} \
\sqrt{\frac{\mu}{a^3}} \cdot \frac{a \sqrt{1 - e^2} \cos E}{1 - e \cos E} \
0
\end{bmatrix}
$$


## Part F ##
In this subsection I needed to estimate the receiver's velocity by implementing the expression I found in E and solving an overdetermined system using the doppler shifts given per satellite in the ephemeris data.

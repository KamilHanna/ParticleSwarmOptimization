# Particle Swarm Optimization (PSO) Project
Implementation of a Particle Swarm Optimization simulation by Hanna Kamil, Hugot Simon and Zhou Fude; for the Advanced Methods for Scientific Computing course by Prof.Formaggia at Politecnico di Milano 

## 1. Introduction
Particle Swarm Optimization (PSO) is a computational method based on a meta-heuristic method that optimizes a problem by iteratively trying to improve a candidate solution with regard to a given measure of quality. It's interesting because it's inspired by the social behavior of bird flocking or fish schooling. Meta-heuristics are widely used to optimize non-convex problems. These are problems that have many local minimas and a unique global minima. 

## 2. Particle Swarm Optimization (PSO)
PSO is initialized with a group of random particles (solutions) and then searches for optima by updating generations. In every iteration, each particle is updated by following two "best" values. The first one is the best solution it has achieved so far. This value is the `personal best` of the particle. Another "best" value that is tracked by the particle swarm optimizer is the best value obtained so far by any particle in the population. This best value is a `global best`.

When a particle takes part of the swarm into consideration, it is a local best value. Each particle keeps track of its coordinates in the problem space which are associated with the best solution (fitness) it has achieved so far. When a particle takes all the population as its topological neighbors, the best value is a global one which is obtained so far by any particle in the population.

We have the following update rules for the position and the velocity at a given time step $t$ and for a given particle i:

![Velocity Equation](https://latex.codecogs.com/svg.image?\bg_black&space;\color{White}&space;v_i^{t+1}&space;=&space;\underbrace{w&space;\cdot&space;v_i^t}_{\text{inertia}}&space;&plus;&space;\underbrace{c_1&space;r_1&space;(pBest_i^t&space;-&space;x_i^t)}_{\text{cognitive}}&space;&plus;&space;\underbrace{c_2&space;r_2&space;(gBest_i^t&space;-&space;x_i^t)}_{\text{social}})


and the position update rule:

![Position Update](https://latex.codecogs.com/svg.image?\bg_black&space;\color{White}&space;x_i^{t&plus;1}&space;=&space;x_i^t&space;&plus;&space;v_i^{t&plus;1})

Where, $v$ is the velocity, $x$ the position, $w$ is the inertia weight that dictates the momentum of the particle, $c1$ and $c2$ are the acceleration parameters which are the weights of the cognitive and social componenets in the global update. $r1$ and $r2$ are random uniformly distributed variables between 0 and 1. 

## 3. Parallel PSO (PPSO)
The main issue encountered in the serial PSO is the convergence rate and precision of the approximate solution are greatly affected by the random initialization of the particles onto the search space of the problem. This can lead to a large variability of results for the same parameters.

In order to minimize the effect of the initialization on the results a variation of the PSO can be used, the star PSO or parallel PSO (PPSO). The main idea behind PPSO is to populate the search space with multiple sub-swarms with different hyper-parameters orchestrated by a master. These sub-swarms act independently from one another. 

After a given communication delay (for example 100 iterations) all sub-swarms communicate their global best values to the master. The best overall global value is then set as the new directions for all sub-swarms.  

## 3. Our Implementation
Our project is structured as follows: 
- The `PSO` class acts as the "swarm". It holds all the particles, hyper-parameters and the methods to update position, velocity, local and global best values.
  The `PSO` class takes care of the initialization of the swarm in other words the collection of particles on the search space associated to the objective function used. 
- The `Particle` class implements the object which holds the position, velocity and fitness value at a given position for a given objective function.
- `Functions` is a namespace containing all the test functions we used. It also implements the methods to get the functions.   

The project is organized in the following folders:
- `include/`: Contains the header files for the PSO and Particles classes as well as  the Functions namespace.
- `src/`: Contains the main file, the implementations of the PSO and Particle classes.
- `visualization/`: Contains a Python script for visualizing the results of the PSO algorithm.

### 3.1 Our PPSO
The PPSO implemented is only partial. The communication between the sub-swarms is not currently available. Nonetheless, this allows us to compare diffent initialization and hyperparameter configurations simultaneously and accept/reject the results given by the sub-swarms. 

## 4. Test Functions
We tested our PSO implementation on several benchmark functions. As said previously, the PSO heuristic is a very common algorithm to optimize non-convex functions. In other words, we want to optimize functions with many local minimums and a single global miima. The following functions are commonly used in the field of optimization to evaluate the performance of algorithms. In all cases we have $\mathcal f : \mathbb R^+ \rightarrow \mathbb R$

- Rastrigin: has a single minimum of 0 at $[0,0,...,0,0]^{D}$
$$F(x) = \sum^{D}_{d=1}{x^{[d]2} - 10 * cos(2 \pi x^{[d]}) + 10}$$

The Rastrigin function has many local minimums which can be a good benchmark of the capacity of the algorithm to recognize these local minimas. 

- Rosenbrock or banana function is a function with a single minimum of 0 at $[1,1,...,1,1]^{D}$

$$F(x)= \sum^{D-1}_{d=1} (1-x^{[d]})^2 + 100 (x^{[d+1]}-(x^{[d]2})^2$$

The Rosenbrock function is a very difficult function to optimize due to its large flat region around the global minimum. This function is a good way to determine whether the algorithm is able to avoid getting stuck in near flat regions.

- Ackley function has a minimum of 0 at $[0,0,...,0,0]^{D}$

![Ackley Function](https://latex.codecogs.com/svg.image?\bg_black&space;\color{White}&space;F(x)&space;=&space;-20&space;\cdot&space;\exp\left(-0.2&space;\cdot&space;\sqrt{&space;\frac{1}{D}&space;\sum_{d=1}^{D}&space;x_d^2&space;}&space;\right)&space;-&space;\exp\left(&space;\frac{1}{D}&space;\sum_{d=1}^{D}&space;\cos(2\pi&space;x_d)&space;\right)&space;&plus;&space;20&space;&plus;&space;\exp(1))

- Griewank is similar to the rastrigin function 

![Griewank Function](https://latex.codecogs.com/svg.image?\bg_black&space;\color{White}&space;F(x)&space;=&space;\sum_{d=1}^{D}&space;\frac{x_d^2}{4000}&space;-&space;\prod_{d=1}^{D}&space;\cos\left(\frac{x_d}{\sqrt{d}}\right)&space;&plus;&space;1)

## 5. Dependencies and build
This project requires `CMake` for building the project and `openMP` in order to enable the CPU parallelism.
In the case `openMP` is not installed or not found the project will still compile and run, only in fully serial manner.   

You can compile the project by running the following command in the root directory of the project:

```sh
mkdir build && cd build/ 
```
```sh
cmake ..
```
```sh
make
```
```sh
./PSO_EXEC 
```

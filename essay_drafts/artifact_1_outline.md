
# Learning as a Stochastic Variational Dynamical System

## Central Question

Can modern learning algorithms be understood as trajectory selection principles governed by variational and thermodynamic structure?

## Core Sections 
- Variational Mechanics and Trajectory Optimization
- Gradient Flow vs Hamiltonian Flow
- Dissipative Dynamics and Learning
- Adding Noise: Langevin and Thermodynamic Structure
- Variational Inference as Action Minimization
- Diffusion Models as Probabilistic Trajectories
- Toward a Unified Geometric View


## Variational Mechanics and Trajectory Optimization 

### Dynamical Systems

A dynamical system is a mathematical framework used to describe how something changes over time. It provides a rule that determines how the state of a system evolves, given its current condition. The state represents the complete information needed to describe the system at a particular moment, such as the position of a particle, the configuration of a mechanical system, or the activity of a network. Once the system’s initial state is specified, the dynamical system determines how that state develops into the future (and sometimes the past).

You can think of a dynamical system as consisting of two main ingredients: a space of possible states and a rule that describes how states change with time. As time progresses, the system follows a trajectory through this state space, representing its evolution.

In classical mechanics, it is helpful to distinguish **configuration** from **state**. A configuration is a point $q \in Q$ recording “where the system is,” while the **mechanical state** is typically $(q,\dot q) \in TQ$. Velocities complete the state because mechanical laws are usually **second-order** in time: specifying $q(t_0)$ alone does not determine the future, but specifying $(q(t_0),\dot q(t_0))$ does (given the forces/constraints).

Formally, let $M$ be a state space (often modeled as a smooth manifold). A continuous-time dynamical system is defined by a rule that assigns a trajectory to each initial state. This is typically specified by a differential equation of the form

$$
\frac{dx}{dt} = F(x, t)
$$

where $x(t) \in M$ represents the state of the system at time $t$, and $F$ is a function that determines how the state changes over time.

Equivalently, a dynamical system can be described by a flow

$$
\phi_t : M \rightarrow M
$$

such that $\phi_t(x_0)$ gives the state of the system at time $t$ starting from the initial state $x_0$, and satisfies

$$
\phi_0(x) = x, \quad \phi_{t+s}(x) = \phi_t(\phi_s(x)).
$$

Under standard regularity assumptions (e.g. the vector field $F$ is locally Lipschitz in $x$), these viewpoints are equivalent: $F$ generates the flow $\phi_t$ via solutions of the ODE, and $\phi_t$ recovers $F$ by differentiation at $t=0$.


### Configuration Space $Q$

In mechanics, a **Configuration Space** $Q$ is a mathematical space where each point represents one complete way a system can be arranged or positioned. Instead of tracking objects directly in physical space, we describe the system using the smallest set of coordinates needed to uniquely specify its state.

Each set of coordinate values corresponds to a single point in configuration space, and as the system changes over time it traces a path through this space. The number of dimensions in configuration space equals the number of independent coordinates needed to describe the system. Thinking this way allows complex motion to be understood as movement through a geometric space of possibilities.

For example, a single particle moving in ordinary space can be fully described by three coordinates 
$$ (q_1,q_2,q_3) $$ 
so its configuration space is simply three-dimensional space $\mathbb{R}^3$, where each point represents one possible position of the particle.


### Mechanical State Space $TQ$

For mechanical systems, the natural state space is not $Q$ itself but instead a space called the **tangent bundle** $TQ$. Intuitively, $TQ$ is the space of all pairs $(q,v)$ where $q\in Q$ and $v \in T_qQ$ is a **tangent vector** at $q$, interpreted as a velocity.

This is why **Lagrangians live on $TQ$**: at each time $t$, the velocity $\dot q(t)$ is literally a tangent vector $\dot q(t)\in T_{q(t)}Q$, so $L$ must accept both $q$ and $\dot q$ as inputs.


### Dynamical Trajectories $q(t)$

A dynamical system evolves over time by moving through its configuration space. The function $q(t)$ describes this evolution by specifying the system’s configuration at each moment in time. In other words, for every time $t$, the value of $q(t)$ tells you exactly where the system is within the space of all possible configurations.

As time varies, $q(t)$ traces out a continuous curve through configuration space. This curve is called the system’s **trajectory**, and it represents the full history of how the system moves from one configuration to another.

The quantity $\dot{q}(t)$ describes how the system moves along this trajectory. It represents the **velocity through configuration space**, indicating both how quickly the configuration is changing and in what direction it is changing at time $t$. While $q(t)$ specifies the system’s position in configuration space, $\dot{q}(t)$ describes how that position is evolving.

Together, $q(t)$ and $\dot{q}(t)$ describe both where the system is and how it is moving. We typically assume trajectories are at least continuously differentiable ($C^1$) so that $\dot q(t)$ exists.

Formally, let $Q$ denote the configuration space of a system (typically modeled as a smooth manifold). A trajectory of the system is defined as a smooth time-parameterized curve

$$
q : I \rightarrow Q
$$

where $I \subseteq \mathbb{R}$ is a time interval and $q(t)$ specifies the system’s configuration at time $t$.

The velocity of the system is defined as the time derivative of this curve. At each time $t$, the velocity is an element of the tangent space of $Q$ at the point $q(t)$:

$$
\dot{q}(t) = \frac{dq}{dt}(t) \in T_{q(t)}Q
$$

where $T_{q(t)}Q$ denotes the tangent space to the configuration space at $q(t)$.

Equivalently, the pair

$$
(q(t), \dot{q}(t))
$$

defines a curve in the tangent bundle $TQ$, which is the space containing all configurations together with their associated velocities.


### Functionals

A functional is a mathematical rule that takes an entire function as its input and produces a single number as its output. Instead of operating on individual numbers or vectors, a functional evaluates properties of whole curves, shapes, or fields. You can think of it as a way of assigning a numerical value to an entire path or configuration, often measuring quantities like total energy, length, or accumulated cost. Functionals are especially useful when studying systems where the object of interest is not a single point, but a continuous trajectory or distribution. In many areas of physics and mathematics, the behavior of a system can be determined by finding the function that makes a particular functional as small or large as possible.

For example, if $y(x)$  is a curve between two points, the expression
$$
J[y] = \int_a^b y(x)^2  dx
$$
is a functional because it takes the entire function $y(x)$ as input and outputs a single number representing the total accumulated squared value of the curve.

Formally, a functional is a mapping
$$
[
J : \mathcal{F} \to \mathbb{R}
]
$$
where $\mathcal{F} $ is a space of functions (such as continuous or differentiable functions) and $ J $ assigns a real number to each function in that space.


### Lagrangian Formalism 

A Lagrangian $L(q, \dot{q}, t)$ is a mathematical function that summarizes how a physical system moves by describing the balance between motion and stored energy. It is typically written as a function of a system’s coordinates $q$, their rates of change $\dot{q}$, and time $t$, and it encodes the rules governing the system’s dynamics. Instead of directly computing forces like in Newtonian mechanics, the Lagrangian allows us to determine motion by identifying the path a system takes between two configurations. This is done by constructing a quantity called the action and finding the path that makes this quantity **stationary** (often minimal in physical systems).

 Lagrangians can be thought of as a **cost density** or a **scoring rule** that tells us how “favorable” or “natural” a system’s motion is at each moment in time. At its core often in classical mechanics, it measures a balance between two competing tendencies: kinetic energy $T$, which reflects how strongly a system is moving or spreading its motion, and potential energy $V$, which reflects how strongly the system is being pulled toward or held by its environment. The difference $T - V$ can be thought of as measuring how freely the system is able to move relative to how constrained or trapped it is. If kinetic energy is large compared to potential energy, the system is moving freely; if potential energy dominates, the system is strongly restricted by forces or stored energy. You can imagine it like a traveler balancing momentum with terrain: $T$ rewards motion and exploration, while $V$ represents hills, valleys, or constraints that shape which routes are natural or costly. The system’s actual motion is determined by combining this balance over time into a total quantity called the action and selecting the path that makes this total score **stationary** (often minimal in physical systems). This viewpoint matters because it reframes “solving the equations of motion” as an optimization-style problem over trajectories. That is to say, variational mechanics reformulates dynamics as optimization over function spaces. 

We will use the following **running example** throughout this section.

For a single particle of mass $m$ moving in one dimension with position $q(t)$ in a potential energy field $V(q)$, the Lagrangian is
$$L(q, \dot{q}, t) = T - V = \frac{1}{2} m \dot{q}^2 - V(q)$$
where $\dot{q}$ is the particle’s velocity, $T$ is kinetic energy, and $V$ is potential energy. Additionally, while classical mechanical systems often use T−V, modern variational formulations allow far more general Lagrangians.


Formally, a Lagrangian is a function
$$L : TQ \times \mathbb{R} \rightarrow \mathbb{R}$$
where $Q$ is the configuration space of the system, $TQ$ is its tangent bundle (the space of coordinates $q$ and velocities $\dot{q}$), and $L(q, \dot{q}, t)$ assigns a real number to each configuration, velocity, and time.



### Action Functional

We now introduce the central object linking **dynamics** and **optimization**. Here, we define the **action functional** on trajectories (typically with fixed endpoints):

$$
S[q(\cdot)] = \int_{t_0}^{t_1} L(q(t), \dot{q}(t), t)\, dt
$$

Where:

* $q(t)$ is a trajectory in configuration space $Q$
* $\dot{q}(t)$ is velocity (a tangent vector in $T_{q(t)}Q$)
* $L$ is the Lagrangian

Typically, one varies over trajectories satisfying boundary conditions $q(t_0)=q_0$ and $q(t_1)=q_1$, and seeks paths that make $S$ **stationary** (often minimal in physical systems). In our running example, substituting $L(q,\dot q,t)=\tfrac12 m\dot q^2 - V(q)$ makes $S[q(\cdot)]$ an explicit “score” assigned to entire paths.


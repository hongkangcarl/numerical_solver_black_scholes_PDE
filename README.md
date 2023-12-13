# Numerical solver for Black-Scholes PDEs (option pricing)
This Jupyter notebook presents a finite-difference numerical solver for Black-Scholes PDEs for European option pricing.

## Derivation of Black-Scholes PDEs

Assume the stock price follows a geometric Brownian motion

$$
dS = \mu Sdt+\sigma SdW
$$

Now consider the European call option price $C(S, t)$. Apply Ito's lemma,

$$
dC = (\mu SC_S + \frac{\sigma^2}{2} S^2 C_{SS}+C_t)dt+\sigma SC_SdW
$$

Now consider a delta hedge portolio $X$, long a call and short $C_S$ shares of the underlying stock

$$
X = C - C_S S
$$

Then

$$
dX = dC - C_S dS = (\frac{\sigma^2}{2}S^2C_{SS} + C_t)dt
$$

which is deterministic. So under no-arbitrage assumption, the return rate must be the risk-free rate,

$$
dX = rXdt
$$

Therefore, 

$$
\frac{\sigma^2}{2}S^2C_{SS} + C_t = rX = r(C-C_SS)
$$

This gives the Black-Scholes PDE

$$
C_t + \frac{\sigma^2}{2}S^2C_{SS} + rSC_S - rC = 0
$$

## Numerical solution of BS PDEs

First redefine the time as the time to maturity, so the PDE becomes

$$
C_t = \frac{\sigma^2}{2}S^2C_{SS} + rSC_S - rC
$$

The initial condition is the payoff at maturity

$$
C(S,t=0)= \max \\{S-K,0\\}
$$

The boundary conditions are Neumann boundary conditions:

$$
C_S = 0, S=0
$$

$$
C_S = 1, S\to\infty
$$

Discretizing the time derivative

$$
C_t \approx \frac{C^{t+∆t} - C^t}{∆t}
$$

Using the implicit Euler method (to prevent numerical exploding errors), which approximates the RHS using the values at $t+∆t$,

$$
\frac{C^{t+∆t} - C^t}{∆t} \approx \frac{\sigma^2}{2}S^2C^{t+∆t}_{SS} + rSC_S^{t+∆t} - rC^{t+∆t}
$$

Now discretize the stock price $\\{S_i\\}_ {i=0}^N$ and the option price $\\{C_i\\}_ {i=0}^N$. For all the intermediate nodes $1≤i≤N-1$, approximate the derivatives as

$$
(C_S)_ i \approx \frac{C_{i+1} - C_{i-1}}{2\Delta S}
$$

$$
(C_{SS})_ i \approx \frac{C_{i+1} + C_{i-1} - 2C_i}{\Delta S^2}
$$

And for the boundary nodes: 

$$
C_1 - C_0 = 0
$$

$$
C_N - C_{N-1} = \Delta S
$$

Now we can establish a systems of linear equations

$$
\mathbf A \mathbf x = \mathbf b
$$

where the vector $\mathbf x = \mathbf A^{-1}\mathbf b$ is the $\\{C^{t+∆t}_ i\\}_ {i=0}^N$ to be solved.

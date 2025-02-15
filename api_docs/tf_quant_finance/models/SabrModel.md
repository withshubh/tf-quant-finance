<!--
This file is generated by a tool. Do not edit directly.
For open-source contributions the docs will be updated automatically.
-->

*Last updated: 2021-03-24.*

<div itemscope itemtype="http://developers.google.com/ReferenceObject">
<meta itemprop="name" content="tf_quant_finance.models.SabrModel" />
<meta itemprop="path" content="Stable" />
<meta itemprop="property" content="__init__"/>
<meta itemprop="property" content="dim"/>
<meta itemprop="property" content="drift_fn"/>
<meta itemprop="property" content="dtype"/>
<meta itemprop="property" content="fd_solver_backward"/>
<meta itemprop="property" content="fd_solver_forward"/>
<meta itemprop="property" content="name"/>
<meta itemprop="property" content="sample_paths"/>
<meta itemprop="property" content="volatility_fn"/>
</div>

# tf_quant_finance.models.SabrModel

<!-- Insert buttons and diff -->

<table class="tfo-notebook-buttons tfo-api" align="left">
</table>

<a target="_blank" href="https://github.com/google/tf-quant-finance/blob/master/tf_quant_finance/models/sabr/sabr_model.py">View source</a>



Implements the SABR model defined by the following equations.

Inherits From: [`GenericItoProcess`](../../tf_quant_finance/models/GenericItoProcess.md)

```python
tf_quant_finance.models.SabrModel(
    beta, volvol, rho=0, enable_unbiased_sampling=False, psi_threshold=2,
    ncx2_cdf_truncation=10, dtype=None, name=None
)
```



<!-- Placeholder for "Used in" -->

```
  dF_t = v_t * F_t ^ beta * dW_{F,t}
  dv_t = volvol * v_t * dW_{v,t}
  dW_{F,t} * dW_{v,t} = rho * dt
```

`F_t` is the forward. `v_t` is volatility. `beta` is the CEV parameter.
`volvol` is volatility of volatility. `W_{F,t}` and `W_{v,t}` are two
correlated Wiener processes with instantaneous correlation `rho`.

This model supports time dependent parameters. In other words, `beta`,
`volvol`, and `rho` can be functions of time. When all of these parameters are
constants, `beta` != 1, and `enable_unbiased_sampling` is True, the almost
exact sampling procedure described in Ref [1] will be used. Otherwise it will
default to Euler sampling.

### Example. Simple example with time independent params.

```none
  process = sabr.SabrModel(beta=0.5, volvol=1, rho=0.5, dtype=tf.float32)
  paths = process.sample_paths(
      initial_forward=100,
      initial_volatility=0.05,
      times=[1,2,5],
      time_step=0.01,
      num_samples=10000)
```

### Example 2. Callable volvol and correlation coefficient.

```none
  volvol_fn = lambda t: tf.where(t < 2, 0.5, 1.0)
  rho_fn = lambda t: tf.where(t < 1, 0., 0.5)

  process = sabr.SabrModel(
      beta=0.5, volvol=volvol_fn, rho=rho_fn, dtype=tf.float32)

  # Any time where parameters vary drastically (e.g. t=2 above) should be
  # added to `times` to reduce numerical error.
  paths = process.sample_paths(
      initial_forward=100,
      initial_volatility=0.05,
      times=[1,2,5],
      time_step=0.01,
      num_samples=10000)
```

### References:
[1]: Chen B, Oosterlee CW, Van Der Weide H. Efficient unbiased simulation
scheme for the SABR stochastic volatility model. 2011
Link: http://ta.twi.tudelft.nl/mf/users/oosterle/oosterlee/SABRMC.pdf
[2]: Andersen, Leif B.G., Efficient Simulation of the Heston Stochastic
Volatility Model (January 23, 2007). Available at SSRN:
https://ssrn.com/abstract=946405 or http://dx.doi.org/10.2139/ssrn.946405

#### Args:


* <b>`beta`</b>: CEV parameter of SABR model. The type and the shape must be
  one of the following: (a) A scalar real tensor in [0, 1]. When beta = 1,
    the algorithm falls back to the Euler sampling scheme. (b) A python
    callable accepting one real `Tensor` argument time t. It should return
    a scalar real value or `Tensor`.
* <b>`volvol`</b>: Volatility of volatility. Either a scalar non-negative real
  tensor, or a python callable accepting same parameters as beta callable.
* <b>`rho`</b>: The correlation coefficient between the two correlated Wiener
  processes for the forward and the volatility. Either a scalar real
  tensor in (-1, 1), or a python callable accepting same parameters as
  beta callable. Default value: 0.
* <b>`enable_unbiased_sampling`</b>: bool. If True, use the sampling procedure
  described in ref [1]. Default value: False
* <b>`psi_threshold`</b>: The threshold of applicability of Andersen L. (2008)
  Quadratic Exponential (QE) algorithm. See ref [1] page 13 and ref [2]. A
  scalar float value in [1, 2]. Default value: 2.
* <b>`ncx2_cdf_truncation`</b>: A positive integer. When computing the CDF of a
  noncentral X2 distribution, it needs to calculate the sum of an
  expression from 0 to infinity. In practice, it needs to be truncated to
  compute an approximate value. This argument is the index of the last
  term that will be included in the sum. Default value: 10.
* <b>`dtype`</b>: The float type to use. Default value: `tf.float32`
* <b>`name`</b>: str. The name to give to the ops created by this class.
  Default value: None which maps to the default name `sabr_model`.

## Methods

<h3 id="dim"><code>dim</code></h3>

<a target="_blank" href="https://github.com/google/tf-quant-finance/blob/master/tf_quant_finance/models/generic_ito_process.py">View source</a>

```python
dim()
```

The dimension of the process.


<h3 id="drift_fn"><code>drift_fn</code></h3>

<a target="_blank" href="https://github.com/google/tf-quant-finance/blob/master/tf_quant_finance/models/generic_ito_process.py">View source</a>

```python
drift_fn()
```

Python callable calculating instantaneous drift.

The callable should accept two real `Tensor` arguments of the same dtype.
The first argument is the scalar time t, the second argument is the value of
Ito process X - `Tensor` of shape `batch_shape + [dim]`. The result is the
value of drift a(t, X). The return value of the callable is a real `Tensor`
of the same dtype as the input arguments and of shape `batch_shape + [dim]`.

#### Returns:

The instantaneous drift rate callable.


<h3 id="dtype"><code>dtype</code></h3>

<a target="_blank" href="https://github.com/google/tf-quant-finance/blob/master/tf_quant_finance/models/generic_ito_process.py">View source</a>

```python
dtype()
```

The data type of process realizations.


<h3 id="fd_solver_backward"><code>fd_solver_backward</code></h3>

<a target="_blank" href="https://github.com/google/tf-quant-finance/blob/master/tf_quant_finance/models/generic_ito_process.py">View source</a>

```python
fd_solver_backward(
    start_time, end_time, coord_grid, values_grid, discounting=None,
    one_step_fn=None, boundary_conditions=None, start_step_count=0, num_steps=None,
    time_step=None, values_transform_fn=None, dtype=None, name=None, **kwargs
)
```

Returns a solver for Feynman-Kac PDE associated to the process.

This method applies a finite difference method to solve the final value
problem as it appears in the Feynman-Kac formula associated to this Ito
process. The Feynman-Kac PDE is closely related to the backward Kolomogorov
equation associated to the stochastic process and allows for the inclusion
of a discounting function.

For more details of the Feynman-Kac theorem see [1]. The PDE solved by this
method is:

```None
  V_t + Sum[mu_i(t, x) V_i, 1<=i<=n] +
    (1/2) Sum[ D_{ij} V_{ij}, 1 <= i,j <= n] - r(t, x) V = 0
```

In the above, `V_t` is the derivative of `V` with respect to `t`,
`V_i` is the partial derivative with respect to `x_i` and `V_{ij}` the
(mixed) partial derivative with respect to `x_i` and `x_j`. `mu_i` is the
drift of this process and `D_{ij}` are the components of the diffusion
tensor:

```None
  D_{ij}(t,x) = (Sigma(t,x) . Transpose[Sigma(t,x)])_{ij}
```

This method evolves a spatially discretized solution of the above PDE from
time `t0` to time `t1 < t0` (i.e. backwards in time).
The solution `V(t,x)` is assumed to be discretized on an `n`-dimensional
rectangular grid. A rectangular grid, G, in n-dimensions may be described
by specifying the coordinates of the points along each axis. For example,
a 2 x 4 grid in two dimensions can be specified by taking the cartesian
product of [1, 3] and [5, 6, 7, 8] to yield the grid points with
coordinates: `[(1, 5), (1, 6), (1, 7), (1, 8), (3, 5) ... (3, 8)]`.

This method allows batching of solutions. In this context, batching means
the ability to represent and evolve multiple independent functions `V`
(e.g. V1, V2 ...) simultaneously. A single discretized solution is specified
by stating its values at each grid point. This can be represented as a
`Tensor` of shape [d1, d2, ... dn] where di is the grid size along the `i`th
axis. A batch of such solutions is represented by a `Tensor` of shape:
[K, d1, d2, ... dn] where `K` is the batch size. This method only requires
that the input parameter `values_grid` be broadcastable with shape
[K, d1, ... dn].

The evolution of the solution from `t0` to `t1` is often done by
discretizing the differential equation to a difference equation along
the spatial and temporal axes. The temporal discretization is given by a
(sequence of) time steps [dt_1, dt_2, ... dt_k] such that the sum of the
time steps is equal to the total time step `t0 - t1`. If a uniform time
step is used, it may equivalently be specified by stating the number of
steps (n_steps) to take. This method provides both options via the
`time_step` and `num_steps` parameters. However, not all methods need
discretization along time direction (e.g. method of lines) so this argument
may not be applicable to some implementations.

The workhorse of this method is the `one_step_fn`. For the commonly used
methods, see functions in <a href="../../tf_quant_finance/math/pde/steppers.md"><code>math.pde.steppers</code></a> module.

The mapping between the arguments of this method and the above
equation are described in the Args section below.

For a simple instructive example of implementation of this method, see
<a href="../../tf_quant_finance/models/GenericItoProcess.md#fd_solver_backward"><code>models.GenericItoProcess.fd_solver_backward</code></a>.

TODO(b/142309558): Complete documentation.

#### Args:


* <b>`start_time`</b>: Real positive scalar `Tensor`. The start time of the grid.
  Corresponds to time `t0` above.
* <b>`end_time`</b>: Real scalar `Tensor` smaller than the `start_time` and greater
  than zero. The time to step back to. Corresponds to time `t1` above.
* <b>`coord_grid`</b>: List of `n` rank 1 real `Tensor`s. `n` is the dimension of the
  domain. The i-th `Tensor` has shape, `[d_i]` where `d_i` is the size of
  the grid along axis `i`. The coordinates of the grid points. Corresponds
  to the spatial grid `G` above.
* <b>`values_grid`</b>: Real `Tensor` containing the function values at time
  `start_time` which have to be stepped back to time `end_time`. The shape
  of the `Tensor` must broadcast with `[K, d_1, d_2, ..., d_n]`. The first
  axis of size `K` is the values batch dimension and allows multiple
  functions (with potentially different boundary/final conditions) to be
  stepped back simultaneously.
* <b>`discounting`</b>: Callable corresponding to `r(t,x)` above. If not supplied,
  zero discounting is assumed.
* <b>`one_step_fn`</b>: The transition kernel. A callable that consumes the following
  arguments by keyword:
    1. 'time': Current time
    2. 'next_time': The next time to step to. For the backwards in time
      evolution, this time will be smaller than the current time.
    3. 'coord_grid': The coordinate grid.
    4. 'values_grid': The values grid.
    5. 'boundary_conditions': The boundary conditions.
    6. 'quadratic_coeff': A callable returning the quadratic coefficients
      of the PDE (i.e. `(1/2)D_{ij}(t, x)` above). The callable accepts
      the time and  coordinate grid as keyword arguments and returns a
      `Tensor` with shape that broadcasts with `[dim, dim]`.
    7. 'linear_coeff': A callable returning the linear coefficients of the
      PDE (i.e. `mu_i(t, x)` above). Accepts time and coordinate grid as
      keyword arguments and returns a `Tensor` with shape that broadcasts
      with `[dim]`.
    8. 'constant_coeff': A callable returning the coefficient of the
      linear homogenous term (i.e. `r(t,x)` above). Same spec as above.
      The `one_step_fn` callable returns a 2-tuple containing the next
      coordinate grid, next values grid.
* <b>`boundary_conditions`</b>: A list of size `dim` containing boundary conditions.
  The i'th element of the list is a 2-tuple containing the lower and upper
  boundary condition for the boundary along the i`th axis.
* <b>`start_step_count`</b>: Scalar integer `Tensor`. Initial value for the number of
  time steps performed.
  Default value: 0 (i.e. no previous steps performed).
* <b>`num_steps`</b>: Positive int scalar `Tensor`. The number of time steps to take
  when moving from `start_time` to `end_time`. Either this argument or the
  `time_step` argument must be supplied (but not both). If num steps is
  `k>=1`, uniform time steps of size `(t0 - t1)/k` are taken to evolve the
  solution from `t0` to `t1`. Corresponds to the `n_steps` parameter
  above.
* <b>`time_step`</b>: The time step to take. Either this argument or the `num_steps`
  argument must be supplied (but not both). The type of this argument may
  be one of the following (in order of generality): (a) None in which case
    `num_steps` must be supplied. (b) A positive real scalar `Tensor`. The
    maximum time step to take. If the value of this argument is `dt`, then
    the total number of steps taken is N = (t0 - t1) / dt rounded up to
    the nearest integer. The first N-1 steps are of size dt and the last
    step is of size `t0 - t1 - (N-1) * dt`. (c) A callable accepting the
    current time and returning the size of the step to take. The input and
    the output are real scalar `Tensor`s.
* <b>`values_transform_fn`</b>: An optional callable applied to transform the
  solution values at each time step. The callable is invoked after the
  time step has been performed. The callable should accept the time of the
  grid, the coordinate grid and the values grid and should return the
  values grid. All input arguments to be passed by keyword.
* <b>`dtype`</b>: The dtype to use.
* <b>`name`</b>: The name to give to the ops.
  Default value: None which means `solve_backward` is used.
* <b>`**kwargs`</b>: Additional keyword args:
  (1) pde_solver_fn: Function to solve the PDE that accepts all the above
    arguments by name and returns the same tuple object as required below.
    Defaults to `tff.math.pde.fd_solvers.solve_backward`.


#### Returns:

A tuple object containing at least the following attributes:
  final_values_grid: A `Tensor` of same shape and dtype as `values_grid`.
    Contains the final state of the values grid at time `end_time`.
  final_coord_grid: A list of `Tensor`s of the same specification as
    the input `coord_grid`. Final state of the coordinate grid at time
    `end_time`.
  step_count: The total step count (i.e. the sum of the `start_step_count`
    and the number of steps performed in this call.).
  final_time: The final time at which the evolution stopped. This value
    is given by `max(min(end_time, start_time), 0)`.


<h3 id="fd_solver_forward"><code>fd_solver_forward</code></h3>

<a target="_blank" href="https://github.com/google/tf-quant-finance/blob/master/tf_quant_finance/models/generic_ito_process.py">View source</a>

```python
fd_solver_forward(
    start_time, end_time, coord_grid, values_grid, one_step_fn=None,
    boundary_conditions=None, start_step_count=0, num_steps=None, time_step=None,
    values_transform_fn=None, dtype=None, name=None, **kwargs
)
```

Returns a solver for the Fokker Plank equation of this process.

The Fokker Plank equation (also known as the Kolmogorov Forward equation)
associated to this Ito process is given by:

```None
  V_t + Sum[(mu_i(t, x) V)_i, 1<=i<=n]
    - (1/2) Sum[ (D_{ij} V)_{ij}, 1 <= i,j <= n] = 0
```

with the initial value condition $$V(0, x) = u(x)$$.

This method evolves a spatially discretized solution of the above PDE from
time `t0` to time `t1 > t0` (i.e. forwards in time).
The solution `V(t,x)` is assumed to be discretized on an `n`-dimensional
rectangular grid. A rectangular grid, G, in n-dimensions may be described
by specifying the coordinates of the points along each axis. For example,
a 2 x 4 grid in two dimensions can be specified by taking the cartesian
product of [1, 3] and [5, 6, 7, 8] to yield the grid points with
coordinates: `[(1, 5), (1, 6), (1, 7), (1, 8), (3, 5) ... (3, 8)]`.

Batching of solutions is supported. In this context, batching means
the ability to represent and evolve multiple independent functions `V`
(e.g. V1, V2 ...) simultaneously. A single discretized solution is specified
by stating its values at each grid point. This can be represented as a
`Tensor` of shape [d1, d2, ... dn] where di is the grid size along the `i`th
axis. A batch of such solutions is represented by a `Tensor` of shape:
[K, d1, d2, ... dn] where `K` is the batch size. This method only requires
that the input parameter `values_grid` be broadcastable with shape
[K, d1, ... dn].

The evolution of the solution from `t0` to `t1` is often done by
discretizing the differential equation to a difference equation along
the spatial and temporal axes. The temporal discretization is given by a
(sequence of) time steps [dt_1, dt_2, ... dt_k] such that the sum of the
time steps is equal to the total time step `t1 - t0`. If a uniform time
step is used, it may equivalently be specified by stating the number of
steps (n_steps) to take. This method provides both options via the
`time_step` and `num_steps` parameters. However, not all methods need
discretization along time direction (e.g. method of lines) so this argument
may not be applicable to some implementations.

The workhorse of this method is the `one_step_fn`. For the commonly used
methods, see functions in <a href="../../tf_quant_finance/math/pde/steppers.md"><code>math.pde.steppers</code></a> module.

The mapping between the arguments of this method and the above
equation are described in the Args section below.

For a simple instructive example of implementation of this method, see
<a href="../../tf_quant_finance/models/GenericItoProcess.md#fd_solver_forward"><code>models.GenericItoProcess.fd_solver_forward</code></a>.

TODO(b/142309558): Complete documentation.

#### Args:


* <b>`start_time`</b>: Real positive scalar `Tensor`. The start time of the grid.
  Corresponds to time `t0` above.
* <b>`end_time`</b>: Real scalar `Tensor` smaller than the `start_time` and greater
  than zero. The time to step back to. Corresponds to time `t1` above.
* <b>`coord_grid`</b>: List of `n` rank 1 real `Tensor`s. `n` is the dimension of the
  domain. The i-th `Tensor` has shape, `[d_i]` where `d_i` is the size of
  the grid along axis `i`. The coordinates of the grid points. Corresponds
  to the spatial grid `G` above.
* <b>`values_grid`</b>: Real `Tensor` containing the function values at time
  `start_time` which have to be stepped back to time `end_time`. The shape
  of the `Tensor` must broadcast with `[K, d_1, d_2, ..., d_n]`. The first
  axis of size `K` is the values batch dimension and allows multiple
  functions (with potentially different boundary/final conditions) to be
  stepped back simultaneously.
* <b>`one_step_fn`</b>: The transition kernel. A callable that consumes the following
  arguments by keyword:
    1. 'time': Current time
    2. 'next_time': The next time to step to. For the backwards in time
      evolution, this time will be smaller than the current time.
    3. 'coord_grid': The coordinate grid.
    4. 'values_grid': The values grid.
    5. 'quadratic_coeff': A callable returning the quadratic coefficients
      of the PDE (i.e. `(1/2)D_{ij}(t, x)` above). The callable accepts
      the time and  coordinate grid as keyword arguments and returns a
      `Tensor` with shape that broadcasts with `[dim, dim]`.
    6. 'linear_coeff': A callable returning the linear coefficients of the
      PDE (i.e. `mu_i(t, x)` above). Accepts time and coordinate grid as
      keyword arguments and returns a `Tensor` with shape that broadcasts
      with `[dim]`.
    7. 'constant_coeff': A callable returning the coefficient of the
      linear homogenous term (i.e. `r(t,x)` above). Same spec as above.
      The `one_step_fn` callable returns a 2-tuple containing the next
      coordinate grid, next values grid.
* <b>`boundary_conditions`</b>: A list of size `dim` containing boundary conditions.
  The i'th element of the list is a 2-tuple containing the lower and upper
  boundary condition for the boundary along the i`th axis.
* <b>`start_step_count`</b>: Scalar integer `Tensor`. Initial value for the number of
  time steps performed.
  Default value: 0 (i.e. no previous steps performed).
* <b>`num_steps`</b>: Positive int scalar `Tensor`. The number of time steps to take
  when moving from `start_time` to `end_time`. Either this argument or the
  `time_step` argument must be supplied (but not both). If num steps is
  `k>=1`, uniform time steps of size `(t0 - t1)/k` are taken to evolve the
  solution from `t0` to `t1`. Corresponds to the `n_steps` parameter
  above.
* <b>`time_step`</b>: The time step to take. Either this argument or the `num_steps`
  argument must be supplied (but not both). The type of this argument may
  be one of the following (in order of generality): (a) None in which case
    `num_steps` must be supplied. (b) A positive real scalar `Tensor`. The
    maximum time step to take. If the value of this argument is `dt`, then
    the total number of steps taken is N = (t1 - t0) / dt rounded up to
    the nearest integer. The first N-1 steps are of size dt and the last
    step is of size `t1 - t0 - (N-1) * dt`. (c) A callable accepting the
    current time and returning the size of the step to take. The input and
    the output are real scalar `Tensor`s.
* <b>`values_transform_fn`</b>: An optional callable applied to transform the
  solution values at each time step. The callable is invoked after the
  time step has been performed. The callable should accept the time of the
  grid, the coordinate grid and the values grid and should return the
  values grid. All input arguments to be passed by keyword.
* <b>`dtype`</b>: The dtype to use.
* <b>`name`</b>: The name to give to the ops.
  Default value: None which means `solve_forward` is used.
* <b>`**kwargs`</b>: Additional keyword args:
  (1) pde_solver_fn: Function to solve the PDE that accepts all the above
    arguments by name and returns the same tuple object as required below.
    Defaults to `tff.math.pde.fd_solvers.solve_forward`.


#### Returns:

A tuple object containing at least the following attributes:
  final_values_grid: A `Tensor` of same shape and dtype as `values_grid`.
    Contains the final state of the values grid at time `end_time`.
  final_coord_grid: A list of `Tensor`s of the same specification as
    the input `coord_grid`. Final state of the coordinate grid at time
    `end_time`.
  step_count: The total step count (i.e. the sum of the `start_step_count`
    and the number of steps performed in this call.).
  final_time: The final time at which the evolution stopped. This value
    is given by `max(min(end_time, start_time), 0)`.


<h3 id="name"><code>name</code></h3>

<a target="_blank" href="https://github.com/google/tf-quant-finance/blob/master/tf_quant_finance/models/generic_ito_process.py">View source</a>

```python
name()
```

The name to give to ops created by this class.


<h3 id="sample_paths"><code>sample_paths</code></h3>

<a target="_blank" href="https://github.com/google/tf-quant-finance/blob/master/tf_quant_finance/models/sabr/sabr_model.py">View source</a>

```python
sample_paths(
    initial_forward, initial_volatility, times, time_step, num_samples=1,
    random_type=None, seed=None, name=None, validate_args=False,
    precompute_normal_draws=True
)
```

Returns a sample of paths from the process.

Generates samples of paths from the process at the specified time points.

Currently only supports absorbing boundary conditions.

#### Args:


* <b>`initial_forward`</b>: Initial value of the forward. A scalar real tensor.
* <b>`initial_volatility`</b>: Initial value of the volatilities. A scalar real
  tensor.
* <b>`times`</b>: The times at which the path points are to be evaluated. Rank 1
  `Tensor` of positive real values. This `Tensor` should be sorted in
  ascending order.
* <b>`time_step`</b>: Positive Python float or a scalar `Tensor `to denote time
  discretization parameter.
* <b>`num_samples`</b>: Positive scalar `int`. The number of paths to draw.
* <b>`random_type`</b>: Enum value of `RandomType`. The type of (quasi)-random number
  generator to use to generate the paths.
  Default value: None which maps to the standard pseudo-random numbers.
* <b>`seed`</b>: Python `int`. The random seed to use. If not supplied, no seed is
  set.
* <b>`name`</b>: str. The name to give this op. If not supplied, default name of
  `sample_paths` is used.
* <b>`validate_args`</b>: Python `bool`. When `True`, input `Tensor's` are checked
  for validity despite possibly degrading runtime performance. The checks
  verify that `times` is strictly increasing. When `False` invalid inputs
  may silently render incorrect outputs. Default value: False.
* <b>`precompute_normal_draws`</b>: Python bool. Indicates whether the noise
  increments are precomputed upfront (see <a href="../../tf_quant_finance/models/euler_sampling/sample.md"><code>models.euler_sampling.sample</code></a>).
  For `HALTON` and `SOBOL` random types the increments are always
  precomputed. While the resulting graph consumes more memory, the
  performance gains might be significant. Default value: `True`.


#### Returns:

A `Tensor`s of shape [num_samples, k, 2] where `k` is the size of the
`times`.  The first values in `Tensor` are the simulated forward `F(t)`,
whereas the second values in `Tensor` are the simulated volatility
trajectories `V(t)`.


<h3 id="volatility_fn"><code>volatility_fn</code></h3>

<a target="_blank" href="https://github.com/google/tf-quant-finance/blob/master/tf_quant_finance/models/generic_ito_process.py">View source</a>

```python
volatility_fn()
```

Python callable calculating the instantaneous volatility.

The callable should accept two real `Tensor` arguments of the same dtype and
shape `times_shape`. The first argument is the scalar time t, the second
argument is the value of Ito process X - `Tensor` of shape `batch_shape +
[dim]`. The result is value of volatility `S_ij`(t, X). The return value of
the callable is a real `Tensor` of the same dtype as the input arguments and
of shape `batch_shape + [dim, dim]`.

#### Returns:

The instantaneous volatility callable.





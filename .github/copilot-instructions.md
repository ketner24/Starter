# Optimization notebook conventions

Write Python intended to run in a Jupyter notebook.

## Libraries and aliases
- Use `import numpy as np`.
- Use `import pandas as pd` when working with tabular data.
- Use `import cvxpy as cvx`.
- Use `import scipy.sparse as sparse` when constraints are large or structured.
- Use `import matplotlib.pyplot as plt` and `import seaborn as sns` for plots.
- Use `import time` for simple solve-time comparisons.

## CVXPY modeling style
- First formulate the mathematical objective and constraints clearly in Markdown.
- Declare decisions with `cvx.Variable(...)`; use attributes such as `nonneg=True`,
  `boolean=True`, `integer=True`, or `bounds=(lower, upper)` when appropriate.
- Define `objective = cvx.Minimize(...)` or `objective = cvx.Maximize(...)`.
- Store constraints in a Python list named `constraints` or `cons`.
- Build models as `prob = cvx.Problem(objective, constraints)`.
- Solve LPs and assignment models with `solver=cvx.HIGHS` when available.
- Use `cvx.OSQP` for quadratic programs and `cvx.SCS` for exponential-cone problems.
- Report `prob.status`, `prob.value`, and key decision-variable values after solving.
- Use vectorized expressions such as `cvx.sum(cvx.multiply(C, X))`,
  `cvx.sum(X, axis=0)`, and `cvx.sum(X, axis=1)` instead of nested scalar loops.
- For large matrix-vector LPs, flatten matrices with `.ravel()` and construct
  sparse constraint matrices using `sparse.coo_array`, `sparse.bmat`, `csr_array`,
  or `csc_array`.

## Common patterns
- Assignment models: minimize `cvx.sum(cvx.multiply(C, X))`; require every row
  and column of `X` to sum to one; set bounds from zero to one when appropriate.
- Portfolio models: compute `Sigma = df.cov().to_numpy()` and
  `r = df.mean().to_numpy()`; use nonnegative portfolio weights, weights summing
  to one, and a return target; minimize risk with
  `0.5 * cvx.quad_form(x, Sigma)`.
- Efficient frontier: define the target as `returnrate = cvx.Parameter()` and
  reuse the same `cvx.Problem` while varying `returnrate.value`.
- Regression: use `cvx.sum_squares(X @ b - y)` for least squares,
  `cvx.norm(X @ b - y, 1)` for LAD, and appropriate L1/L2 penalties for
  lasso, ridge, and elastic net.
- Seed synthetic data with `np.random.seed(0)` unless asked otherwise.

## Response style
- Generate short, runnable cells.
- Explain the decision variables, objective, and constraints before code.
- Check matrix dimensions where ambiguity could cause errors.
- Preserve feasible convex formulations and call out nonconvex constraints.
- Avoid unnecessary classes or abstractions.

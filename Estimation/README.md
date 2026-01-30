# Estimation

### 1. Guess values for $a_{1}$ and $a_{2}$

```matlab
params0 = [-0.8, -1.7];  % Initial guess [a1, a2]
lb = [-1.6, -3.4];       % Lower bounds
ub = [0, 0];             % Upper bounds
```
>**Note**: Based on the estimates from the paper, I set the bounds roughly. Since this exercise is intended to getting the hang of the methodology used in the paper, one cannot learn important things from the specific numbers.

### 2. Solve for $\\{P_{i}\\}$ using the market clearing conditions for all $j$.

Denote $T_{i}=P_{i}^{1-\sigma}$. Then the market clearing condition becomes simpler.

```matlab
function T = solve_T_system(a1, a2, y, d, delta, n_regions)
    
    theta = y / sum(y);
    T = ones(n_regions, 1);
    
    max_iter = 1000;
    tol = 1e-10;
    
    for iter = 1:max_iter
        T_old = T;
        
        for j = 1:n_regions
            sum_val = 0;
            for i = 1:n_regions
                exp_term = exp(a1 * log(d(i,j)) + a2 * (1 - delta(i,j)));
                sum_val = sum_val + (1/T_old(i)) * theta(i) * exp_term;
            end
            T(j) = sum_val;
        end
        
        % Normalize
        T = T / T(1);
        
        % Check convergence
        if max(abs(T - T_old)) < tol
            return;
        end
    end
    
    warning('T did not converge');
end
```

### 3.

3. Compute $k=E[\ln{z_{ij}}] - E[\text{predicted}]$.
4. Calculate the nonlinear least squares objective.
5. If the difference between $\ln{z_{ij}}$ from the data and the prediction is below a tolerance level, stop.
6. Otherwise, return to step 1.
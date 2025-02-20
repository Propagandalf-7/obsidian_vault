# time-series

## Variance
$s^2 = \dfrac{1}{n}\sum^n_{t=1}(y_t - \bar{y})^2$


--------------------------------------------------------------------------
## Autocovariance
$\gamma_k = \dfrac{1}{n}\sum^{n-k}_{t=1} (y_t-\bar{y})(y_{t+k}-\bar{x})$

$n$ - number of elements in the timeseries.
$y_t$ - the value of the time series at time $t$.
$\bar{y}$ - the mean/expected value of the values $y_t$.
$k$ - is the lag integer with $k \in [0, n-1]$.
$\gamma_k$ - is the autocovariance at lag $k$.


Note: At lag $k = 0$, the autocovariance is just the variance.

#### Autocovariance Matrix $R_k$

$\mathbf{R_k} = \begin{pmatrix} \gamma_0 & \gamma_1 & \gamma_2 & \cdots & \gamma_{k-1} \\ \gamma_1 & \gamma_0 & \gamma_1 & \cdots & \gamma_{k-2} \\ \gamma_2 & \gamma_1 & \gamma_0 & \cdots & \gamma_{k-3} \\ \vdots & \vdots & \vdots & \ddots & \vdots \\ \gamma_{k-1} & \gamma_{k-2} & \gamma_{k-3} & \cdots & \gamma_0 \end{pmatrix}$

--------------------------------------------------------------------------
## Autocorrelation
### Autocorrelation Function (ACF):

$r_k = \dfrac{\sum^{n-k}_{t=1}(y_t - \bar{y})(y_{t-k} - \bar{y})}{\sum^n_{t=1}(y_t - \bar{y})^2}$

with
$n$ - number of elements in the timeseries.
$y_t$ - the value of the time series at time $t$.
$\bar{y}$ - the mean/expected value of the values $y_t$.
$k$ - is the lag integer with $k \in [0, n-1]$.
$r_k$ - the k'th correlation value, measuring the correlation between $y_t$ and $y_{t-1}$.

#### Acceptance of ACF values:
White noise is classified as: $r_k<\pm \frac{2}{\sqrt{n}}$. White noise are values that can be interpreted as random values that do not contribute to the behavior of the series. If 95% of the values are white noise, then it is a white noise series (i.e. not enough data to influence the results).

--------------------------------------------------------------------------
### Partial Autocorrelation function (PACF):

$\mathbf{R_k} \cdot \mathbf{\phi_k} = \mathbf{r_k}$

$\mathbf{R_k}$ - the $k \times k$ autocovariance matrix.
$\mathbf{\phi_k} = \left[\phi_1, \phi_2, ..., \phi_k\right]^T$ - the vector of autoregressive coefficients.
$\mathbf{r_k} = \left[\gamma_1, \gamma_2, ..., \gamma_k\right]^T$ - the vector of autocovariances of lags $1, 2,..., k$.


--------------------------------------------------------------------------
## Autoregressive Model (AR):

$y_t = \beta + \epsilon_t + \sum_{i=1}^p \theta_i y_{t-i}$
with
$y_t$ - the time series value at time $t$
$p$ - the number of lags
$\epsilon_t$ - the noise/error at time t
$\beta$ - some constant
$\theta$ - 

--------------------------------------------------------------------------
# SARIMAX Model

Parameters:
$p$ - Order of the non-seasonal AR (autoregressive) component.
$d$ - Degree of non-seasonal differencing.
$q$ - Order of the non-seasonal MA (moving average) component.
$P$ - Order of the seasonal AR component.
$D$ - Degree of seasonal differencing.
$Q$ - Order of the seasonal MR component.
m - length of the seasonal period.

#### 2. **SARIMAX Model Formulation**: 

The SARIMAX model incorporates an exogenous regressor, \( X_t \), representing external factors that affect the series. The general form of the SARIMAX model can be written as: 

$y_t = \mu + (\phi_1 * y_{t-1} + ... + \phi_p * y_{t-p}) + (\theta_1 * e_{t-1} + ... + \theta_q * e_{t-q}) + (\Phi_1 * y_{t-m} + ... + \Phi_P * y_{t-mP}) + (\Theta_1 * e_{t-m} + ... + \Theta_Q * e_{t-mQ}) + (\beta_1 * X_{1,t} + ... + \beta_k * X_{k,t}) + e_t$


SARIMAX(2,1,1)(1,1,1,12)SARIMAX(2, 1, 1)(1, 1, 1, 12)SARIMAX(2,1,1)(1,1,1,12), it implies:

- p=2p=2p=2: Sales depend on the previous two months' sales.
- d=1d=1d=1: One differencing is applied to remove the trend.
- q=1q=1q=1: One lagged error term is used for moving average correction.
- P=1,D=1,Q=1P=1, D=1, Q=1P=1,D=1,Q=1: Sales depend on values from the same month last year, with seasonal differencing.
- m=12m=12m=12: The seasonal period is 12 months.
- XtX_tXt​: Advertising budget influences sales.


# Objective Functions

These are the functions that we want to minimize/
## Akaike Information Criterion (AIC)

$AIC = 2k - 2 \ln{(L)}$
with:
$k$ - the number of estimated parameters in the model
$L$ - the maximum likelihood of the model
## Bayesian Information Criterion (BIC)

$BIC = \ln{(n)}k - 2 \ln{(L)}$
with:
$n$ - the number of observations
$k$ - the number of estimated parameters in the model
$L$ - the maximum likelihood of the model

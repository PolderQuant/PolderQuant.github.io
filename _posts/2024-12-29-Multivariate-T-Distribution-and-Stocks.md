---
layout: post
title:  "Multivariate T & Stocks 6"
---

# Introduction
In the dynamic landscape of video and music streaming services, platforms such as Disney, Amazon, Netflix, and AT&T have experienced notable success, capturing the interest of investors worldwide. Our investment firm has access to a daily returns dataset (`returns_daily.csv`) from January 3rd, 2020 to December 29th, 2023, covering these four major stocks.

We aim to analyze the daily returns of Disney, Amazon, Netflix, and AT&T by fitting a **multivariate t-distribution**. This approach accounts for tail clustering and potential co-crashes, where a traditional multivariate normal distribution might fail. We do two main things:

1. **Simulation Study**: Validate our Maximum Likelihood Estimation (MLE) procedure for the multivariate t-distribution on synthetic data.  
2. **Real-World Application**: Use the fitted multivariate t-distribution on the real stock returns to evaluate potential investment decisions and conduct hypothesis tests about daily returns.

---

<!-- TOC -->
- [Simulating a multivariate t-distribution](#simulating-a-multivariate-t-distribution)
- [Dissecting the stock market](#dissecting-the-stock-market)
- [Are Amazon returns still king?](#are-amazon-returns-still-king)
- [The streaming war revisited: Disney vs. Netflix daily returns](#the-streaming-war-revisited-disney-vs-netflix-daily-returns)
<!-- /TOC -->

---

## Simulating a multivariate t-distribution

To verify our estimator, we first simulate data where

[![\\ X \sim t_3(\mu, \Sigma, \nu)](https://latex.codecogs.com/svg.latex?%5C%5C%20X%20%5Csim%20t_3(%5Cmu%2C%20%5CSigma%2C%20%5Cnu))](#_)

with:

[![\\  \\ \mu = (1, 1, 1)^T \\ ](https://latex.codecogs.com/svg.latex?%5C%5C%20%20%5C%5C%20%5Cmu%20%3D%20(1%2C%201%2C%201)%5ET%20%5C%5C%20)](#_)
[![\\ \nu = 4](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cnu%20%3D%204)](#_)
[![\\ \Sigma = \begin{pmatrix} \\   1 & 0.3 & 0.7 \\ \\   0.3 & 1   & 1.0 \\ \\   0.7 & 1   & 3.0 \\   \end{pmatrix}](https://latex.codecogs.com/svg.latex?%5C%5C%20%5CSigma%20%3D%20%5Cbegin%7Bpmatrix%7D%20%5C%5C%20%20%201%20%26%200.3%20%26%200.7%20%5C%5C%20%5C%5C%20%20%200.3%20%26%201%20%20%20%26%201.0%20%5C%5C%20%5C%5C%20%20%200.7%20%26%201%20%20%20%26%203.0%20%5C%5C%20%20%20%5Cend%7Bpmatrix%7D)](#_)

### Simulation setup

1. **Data Generation**: For 
   [![\\ n \in \{50, 100, 500, 750\}](https://latex.codecogs.com/svg.latex?%5C%5C%20n%20%5Cin%20%5C%7B50%2C%20100%2C%20500%2C%20750%5C%7D)](#_),
   we generate 1000 datasets per sample size using the true parameters.  

2. **Estimation**: For each dataset, we optimize the **negative log-likelihood** of the t-distribution (via an MLE approach) to estimate [![\\ \mu, \Sigma, \text{and} \ \nu](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cmu%2C%20%5CSigma%2C%20%5Ctext%7Band%7D%20%5C%20%5Cnu)](#_)

3. **Hypothesis Testing**: We conduct a Wald test on [![\\ \nu](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cnu)](#_) at 5% significance:

   [![\\ \begin{aligned} \\    H_0: &\ \nu = 4 \\ \\    H_1: &\ \nu \neq 4. \\    \end{aligned}](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cbegin%7Baligned%7D%20%5C%5C%20%20%20%20H_0%3A%20%26%5C%20%5Cnu%20%3D%204%20%5C%5C%20%5C%5C%20%20%20%20H_1%3A%20%26%5C%20%5Cnu%20%5Cneq%204.%20%5C%5C%20%20%20%20%5Cend%7Baligned%7D)](#_)

4. **Standard Errors**: Compute standard errors for the estimated parameters.  

5. **Bias**: Assess bias by comparing the mean estimate to the true parameter values.

Below is a **summary table** of our main parameters of interest (bias and average estimates for [![\\ \mu_1, \Sigma_{3,1}, \text{ and } \nu](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cmu_1%2C%20%5CSigma_%7B3%2C1%7D%2C%20%5Ctext%7B%20and%20%7D%20%5Cnu)](#_) ), as well as the rejection rates for the hypothesis test. Note how, as n increases, the estimates converge to the true values and the standard errors decrease.

| n   | Bias [![\\ \widehat{\mu_1}](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cwidehat%7B%5Cmu_1%7D)](#_) | Avg [![\\ \widehat{\mu_1}](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cwidehat%7B%5Cmu_1%7D)](#_) | Bias $$\widehat{\Sigma_{3,1}}$$ | Avg [![\\ \widehat{\Sigma_{3,1}}](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cwidehat%7B%5CSigma_%7B3%2C1%7D%7D)](#_)| Bias [![\\ \hat{\nu}](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Chat%7B%5Cnu%7D)](#_)| Avg [![\\ \hat{\nu}](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Chat%7B%5Cnu%7D)](#_) |
|-----|--------------------------|--------------------------|---------------------------------|--------------------------------|--------------------|-------------------|
| 50  | 0.0040                   | 1.0040                  | 0.0386                          | 0.7386                         | 1065.2928          | 1069.2928         |
| 100 | -0.0059                  | 0.9941                  | 0.0057                          | 0.7057                         | 140.4283           | 144.4283          |
| 500 | 0.0000                   | 1.0000                  | 0.0000                          | 0.7000                         | 0.0777             | 4.0777            |
| 750 | -0.0004                  | 0.9996                  | 0.0027                          | 0.7027                         | 0.0533             | 4.0533            |

| n   | Std. error [![\\ \widehat{\mu_1}](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cwidehat%7B%5Cmu_1%7D)](#_) | Std. error [![\\ \widehat{\Sigma_{3,1}}](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cwidehat%7B%5CSigma_%7B3%2C1%7D%7D)](#_)| Std error [![\\ \hat{\nu}](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Chat%7B%5Cnu%7D)](#_)  |
|-----|--------------------------------|---------------------------------------|--------------------------|
| 50  | 0.1657                         | 0.4211                                | 5217.3927               |
| 100 | 0.1209                         | 0.2381                                | 1240.5258               |
| 500 | 0.0506                         | 0.1028                                | 0.4714                  |
| 750 | 0.0419                         | 0.0823                                | 0.3626                  |

| n   | # of rejections of [![\\ H_0](https://latex.codecogs.com/svg.latex?%5C%5C%20H_0)](#_) | Percentage |
|-----|----------------------------|------------|
| 50  | 47                         | 4.7%       |
| 100 | 50                         | 5.0%       |
| 500 | 41                         | 4.1%       |
| 750 | 32                         | 3.2%       |

**Key Takeaways**  
- As b grows, estimates become more accurate (lower bias) and more precise (lower standard errors).  
- The rejection rates of [![\\ nu](https://latex.codecogs.com/svg.latex?%5C%5C%20nu)](#_) hover around the nominal 5% level, confirming appropriate test performance.  
- Estimating [![\\ nu](https://latex.codecogs.com/svg.latex?%5C%5C%20nu)](#_) accurately can be challenging for small sample sizes.

![](/assets/img/multivariate_t/afb1.png)  
![](/assets/img/multivariate_t/afb2.png)

---

## Dissecting the stock market

With our estimator verified, we now turn to real data: daily returns of **Disney, Amazon, Netflix, and AT&T** from 2020-01-03 to 2023-12-29.

### Estimated parameters:

- **Mean vector** [![\\ \hat{\mu}](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Chat%7B%5Cmu%7D)](#_):

  [![\\ \hat{\mu} =  \\     \begin{pmatrix} \\       -0.0621 \\ \\        0.0527 \\ \\        0.0420 \\ \\       -0.0115 \\     \end{pmatrix} \\ = \begin{pmatrix} \\       Disney \\ \\       Amazon \\ \\       Netflix\\ \\       AT\&T \\     \end{pmatrix}](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Chat%7B%5Cmu%7D%20%3D%20%20%5C%5C%20%20%20%20%20%5Cbegin%7Bpmatrix%7D%20%5C%5C%20%20%20%20%20%20%20-0.0621%20%5C%5C%20%5C%5C%20%20%20%20%20%20%20%200.0527%20%5C%5C%20%5C%5C%20%20%20%20%20%20%20%200.0420%20%5C%5C%20%5C%5C%20%20%20%20%20%20%20-0.0115%20%5C%5C%20%20%20%20%20%5Cend%7Bpmatrix%7D%20%5C%5C%20%3D%20%5Cbegin%7Bpmatrix%7D%20%5C%5C%20%20%20%20%20%20%20Disney%20%5C%5C%20%5C%5C%20%20%20%20%20%20%20Amazon%20%5C%5C%20%5C%5C%20%20%20%20%20%20%20Netflix%5C%5C%20%5C%5C%20%20%20%20%20%20%20AT%5C%26T%20%5C%5C%20%20%20%20%20%5Cend%7Bpmatrix%7D)](#_)

  *Std. errors of [![\\ \hat{\mu} = (0.0528, 0.0611, 0.0701, 0.0399)](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Chat%7B%5Cmu%7D%20%3D%20(0.0528%2C%200.0611%2C%200.0701%2C%200.0399))](#_)*

- **Scale matrix** [![\\ \hat{\Sigma}](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Chat%7B%5CSigma%7D)](#_):

  [![\\ \hat{\Sigma} = \\     \begin{pmatrix} \\       2.1926 & 1.1766 & 2.9032 & 1.2703\\ \\       1.1766 & 2.0557 & 3.8695 & 0.7594\\ \\       2.9032 & 3.8695 & 0.3633 & 0.3421\\ \\       1.2703 & 0.7594 & 0.3421 & 1.2556 \\     \end{pmatrix}](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Chat%7B%5CSigma%7D%20%3D%20%5C%5C%20%20%20%20%20%5Cbegin%7Bpmatrix%7D%20%5C%5C%20%20%20%20%20%20%202.1926%20%26%201.1766%20%26%202.9032%20%26%201.2703%5C%5C%20%5C%5C%20%20%20%20%20%20%201.1766%20%26%202.0557%20%26%203.8695%20%26%200.7594%5C%5C%20%5C%5C%20%20%20%20%20%20%202.9032%20%26%203.8695%20%26%200.3633%20%26%200.3421%5C%5C%20%5C%5C%20%20%20%20%20%20%201.2703%20%26%200.7594%20%26%200.3421%20%26%201.2556%20%5C%5C%20%20%20%20%20%5Cend%7Bpmatrix%7D)](#_)

- **Degrees of freedom** [![\\ \hat{\nu} = 3.2713](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Chat%7B%5Cnu%7D%20%3D%203.2713)](#_)  
  *Std. error of [![\\ \hat{\nu} = 0.2126](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Chat%7B%5Cnu%7D%20%3D%200.2126)](#_)*

**Interpretation**  
- **Amazon** exhibits the highest expected daily return 0.0527%.  
- **Disney** shows a negative average daily return -0.0621%.  
- Larger variances in Disney and Amazon imply higher volatility.  
- Covariances reveal potentially high co-movement between certain stocks (e.g., Amazon & Disney).

![](/assets/img/multivariate_t/afb3.png)

---

## Are Amazon returns still king?

An executive suggests Amazon’s daily return is **1%**, so we test:

[![\\ \begin{aligned} \\ H_0: &\ \mu_2 = 1 \\ \\ H_1: &\ \mu_2 \neq 1. \\ \end{aligned}](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cbegin%7Baligned%7D%20%5C%5C%20H_0%3A%20%26%5C%20%5Cmu_2%20%3D%201%20%5C%5C%20%5C%5C%20H_1%3A%20%26%5C%20%5Cmu_2%20%5Cneq%201.%20%5C%5C%20%5Cend%7Baligned%7D)](#_)

Using a Wald test at 5% significance:

- Test statistic [![\\ \approx 240.5862](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Capprox%20240.5862)](#_)  
- Critical  [![\\ \chi^2_1](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cchi%5E2_1)] value at 5% = 3.841  

Since [![\\ 240.5862 \gg 3.841](https://latex.codecogs.com/svg.latex?%5C%5C%20240.5862%20%5Cgg%203.841)](#_), we **reject** [![\\ H_0](https://latex.codecogs.com/svg.latex?%5C%5C%20H_0)](#_), implying Amazon’s daily return is **not** 1%. The estimated return [![\\ \approx 0.0527\%](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Capprox%200.0527%5C%25)](#_) is notably lower.

![](/assets/img/multivariate_t/amzn.png)

---

## The streaming war revisited: Disney vs. Netflix daily returns

Another claim is that Disney and Netflix have **equal** expected returns:

[![\\ \begin{aligned} \\ H_0: &\ \mu_1 = \mu_3 \\ \\ H_1: &\ \mu_1 \neq \mu_3. \\ \end{aligned}](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cbegin%7Baligned%7D%20%5C%5C%20H_0%3A%20%26%5C%20%5Cmu_1%20%3D%20%5Cmu_3%20%5C%5C%20%5C%5C%20H_1%3A%20%26%5C%20%5Cmu_1%20%5Cneq%20%5Cmu_3.%20%5C%5C%20%5Cend%7Baligned%7D)](#_)

Again, a Wald test at 5% significance yields:

- Test statistic [![\\ \approx 2.4335](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Capprox%202.4335)](#_)
- Critical [![\\ \chi^2_1](https://latex.codecogs.com/svg.latex?%5C%5C%20%5Cchi%5E2_1)] value at 5% = 3.841  

Since [![\\ 2.4335 < 3.841](https://latex.codecogs.com/svg.latex?%5C%5C%202.4335%20%3C%203.841)](#_), we **fail to reject** [![\\ H_0](https://latex.codecogs.com/svg.latex?%5C%5C%20H_0)](#_). Statistically, we don’t find enough evidence to say Disney’s and Netflix’s expected daily returns differ.

---

<!-- You could add references or further notes here if needed -->

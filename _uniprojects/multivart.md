---
title:  "Multivariate T & Stocks"
excerpt: "An overview of simulating and testing a multivariate t-distribution and applying it to real stock returns."
header:
teaser: /assets/img/multivariate_t/teaser.jpg
layout: collection # Ensure it matches your theme logic
collection: uniprojects
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
To verify our estimator, we first simulate data where \( X \sim t_3(\mu, \Sigma, \nu) \) with:
- \(\mu = (1, 1, 1)^T\)
- \(\nu = 4\)
- \(\Sigma = \begin{pmatrix}
1 & 0.3 & 0.7 \\
0.3 & 1   & 1.0 \\
0.7 & 1   & 3.0
\end{pmatrix}\)

### Simulation setup
1. **Data Generation**: For \(n \in \{50, 100, 500, 750\}\), we generate 1000 datasets per sample size using the true parameters.  
2. **Estimation**: For each dataset, we optimize the **negative log-likelihood** of the t-distribution (via an MLE approach) to estimate \(\mu\), \(\Sigma\), and \(\nu\).  
3. **Hypothesis Testing**: We conduct a Wald test on \(\nu\) at 5% significance:  
   \[
   H_0: \nu = 4 \quad \text{vs} \quad H_1: \nu \neq 4.
   \]  
4. **Standard Errors**: Compute standard errors for the estimated parameters.  
5. **Bias**: Assess bias by comparing the mean estimate to the true parameter values.

Below is a **summary table** of our main parameters of interest (bias and average estimates for \(\mu_1\), \(\Sigma_{3,1}\), and \(\nu\)), as well as the rejection rates for the hypothesis test. Note how, as \(n\) increases, the estimates converge to the true values and the standard errors decrease.

| n   | Bias \(\widehat{\mu_1}\) | Avg \(\widehat{\mu_1}\) | Bias \(\widehat{\Sigma_{3,1}}\) | Avg \(\widehat{\Sigma_{3,1}}\) | Bias \(\hat{\nu}\) | Avg \(\hat{\nu}\) |
|-----|---------------------------|--------------------------|---------------------------------|--------------------------------|--------------------|-------------------|
| 50  | 0.0040                   | 1.0040                  | 0.0386                          | 0.7386                         | 1065.2928          | 1069.2928         |
| 100 | -0.0059                  | 0.9941                  | 0.0057                          | 0.7057                         | 140.4283           | 144.4283          |
| 500 | 0.0000                   | 1.0000                  | 0.0000                          | 0.7000                         | 0.0777             | 4.0777            |
| 750 | -0.0004                  | 0.9996                  | 0.0027                          | 0.7027                         | 0.0533             | 4.0533            |

| n   | Std. error \(\widehat{\mu_1}\) | Std. error \(\widehat{\Sigma_{3,1}}\) | Std error \(\hat{\nu}\) |
|-----|--------------------------------|---------------------------------------|--------------------------|
| 50  | 0.1657                         | 0.4211                                | 5217.3927               |
| 100 | 0.1209                         | 0.2381                                | 1240.5258               |
| 500 | 0.0506                         | 0.1028                                | 0.4714                  |
| 750 | 0.0419                         | 0.0823                                | 0.3626                  |

| n   | # of rejections of \(H_0\) | Percentage |
|-----|----------------------------|------------|
| 50  | 47                         | 4.7%       |
| 100 | 50                         | 5.0%       |
| 500 | 41                         | 4.1%       |
| 750 | 32                         | 3.2%       |

**Key Takeaways**  
- As \(n\) grows, estimates become more accurate (lower bias) and more precise (lower standard errors).  
- The rejection rates of \(\nu\) hover around the nominal 5% level, confirming appropriate test performance.  
- Estimating \(\nu\) accurately can be challenging for small sample sizes.

### [Image block for simulated parameter convergence]
*(Place simulation plots here, e.g. “afb1.png” and “afb2.png”.)*
![](/assets/img/multivariate_t/afb1.png)
![](/assets/img/multivariate_t/afb2.png)

---

## Dissecting the stock market
With our estimator verified, we now turn to real data: daily returns of **Disney, Amazon, Netflix, and AT&T** from 2020-01-03 to 2023-12-29.

**Estimated parameters:**

- **Mean vector** \(\hat{\mu}\):
  \[
    \hat{\mu} = 
    \begin{pmatrix}
      -0.0621 \\
      0.0527 \\
      0.0420 \\
      -0.0115
    \end{pmatrix}
    \quad
    \text{(Disney, Amazon, Netflix, AT&T)}
  \]  
  *Std. errors of \(\hat{\mu}\) = (0.0528, 0.0611, 0.0701, 0.0399)*

- **Scale matrix** \(\hat{\Sigma}\):
  \[
    \hat{\Sigma} =
    \begin{pmatrix}
      2.1926 & 1.1766 & 2.9032 & 1.2703\\
      1.1766 & 2.0557 & 3.8695 & 0.7594\\
      2.9032 & 3.8695 & 0.3633 & 0.3421\\
      1.2703 & 0.7594 & 0.3421 & 1.2556
    \end{pmatrix}
  \]  
  *Std. errors of \(\hat{\Sigma}\) follow similarly (element-wise).*

- **Degrees of freedom** \(\hat{\nu} = 3.2713\)  
  *Std. error of \(\hat{\nu}\) = 0.2126*

**Interpretation**  
- **Amazon** exhibits the highest expected daily return (\(0.0527\%\)).  
- **Disney** shows a negative average daily return (\(-0.0621\%\)).  
- Larger variances in Disney and Amazon imply higher volatility.  
- Covariances reveal potentially high co-movement between certain stocks (e.g., Amazon & Disney).

### [Image block for scatterplots of empirical stock returns]
*(Place correlation or scatter matrix plot here, e.g. “afb3.png”.)*
![](/assets/img/multivariate_t/afb3.png)

---

## Are Amazon returns still king?
An executive suggests Amazon’s daily return is **1%**, so we test:
\[
H_0: \mu_2 = 1 \quad \text{vs} \quad H_1: \mu_2 \neq 1.
\]

Using a Wald test at 5% significance:
- Test statistic \(\approx 240.5862\)
- Critical \(\chi^2_1\) value at 5% = 3.841

Since \(240.5862 \gg 3.841\), we **reject** \(H_0\), implying Amazon’s daily return is **not** 1%. The estimated return (\(\approx 0.0527\%\)) is notably lower.

### [Image block for Amazon stock price]
*(Place Amazon price chart here, e.g. “amzn.png”.)*
![](/assets/img/multivariate_t/amzn.png)

---

## The streaming war revisited: Disney vs. Netflix daily returns
Another claim is that Disney and Netflix have **equal** expected returns:

\[
H_0: \mu_1 = \mu_3 \quad \text{vs} \quad H_1: \mu_1 \neq \mu_3.
\]

Again, a Wald test at 5% significance yields:
- Test statistic \(\approx 2.4335\)
- Critical \(\chi^2_1\) value at 5% = 3.841

Since \(2.4335 < 3.841\), we **fail to reject** \(H_0\). Statistically, we don’t find enough evidence to say Disney’s and Netflix’s expected daily returns differ.

---

<!-- You could add references or further notes here if needed -->

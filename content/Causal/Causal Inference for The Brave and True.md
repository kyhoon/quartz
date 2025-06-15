---
url: https://matheusfacure.github.io/python-causality-handbook/landing-page.html
tags:
  - causal
  - todo
---
## 01 - Introduction To Causality

**ATE** = Average Treatment Effect
**ATT** = Average Treatment Effect on the Treated
$$
\begin{align}
ATE &= E[Y_1 - Y_0] \\
ATT &= E[Y_1 - Y_0 |T=1] 
\end{align}
$$
$$
\begin{equation}
E[Y \mid T=1] - E[Y \mid T=0] = 
\underbrace{E[Y_1 - Y_0 \mid T=1]}_{\text{ATT}} + 
\{ \underbrace{E[Y_0 \mid T=1] - E[Y_0 \mid T=0]}_{\text{BIAS}} \}
\end{equation}
$$
The **bias** is given by how the treated and control group differ before the treatment, in case neither of them has received the treatment.

 If $E[Y_0|T=0]=E[Y_0|T=1]$, then, association IS CAUSATION!

When there is bias:

![[Pasted image 20250608121604.png]]

no bias:

![[Pasted image 20250608121619.png]]


## 02 - Randomised Experiments

Randomisation annihilates bias by making the potential outcomes independent of the treatment.
$$
(Y_0, Y_1) \perp T
$$
Saying that the potential outcomes are independent of the treatment is saying that they would be, in expectation, the same in the treatment or the control group. ... the only thing generating a difference between the outcome in the treated and in the control group.

In causal questions, we usually can argue in both ways: that X causes Y, or that it is a third variable Z that causes both X and Y, and hence the X and Y correlation is just spurious. For this reason, knowing the assignment mechanism leads to a much more convincing causal answer.


## 03 - Stats Review: The Most Dangerous Equation

Moivre’s equation talks about a fundamental fact about the reality of information and records in the form of data: it is always imprecise.

standard deviation, confidence intervals

Hypothesis testing, p-values


## 04 - Graphical Causal Models

conditional independence of the potential outcomes:  $(Y_0, Y_1) \perp T \mid X$

![[Pasted image 20250608123311.png]]

$A \not\perp C$ and $A \perp C \mid B$

![[Pasted image 20250608123419.png]]

**backdoor path**
$A \not\perp B$ and $A \perp B \mid C$

![[Pasted image 20250608123455.png]]

**collider**
$A \perp B$ and $A \not\perp B \mid C$

 A path is **blocked** if and only if:
1. It contains a non collider that has been conditioned on
2. It contains a collider that has not been conditioned on and has no descendants that have been conditioned on.

![[Pasted image 20250608123811.png]]

1. $D \perp C$. It contains a collider that it has **not** been conditioned on.
2. $D \not\perp C \mid A$. It contains a collider that it has been conditioned on.
3. $D \not\perp C \mid G$. It contains the descendent of a collider that has been conditioned on. You can see G as some kind of proxy for A here.
4. $A \perp F$. It contains a collider, B->E<-F, that it has **not** been conditioned on.
5. $A \not\perp F \mid E$. It contains a collider, B->E<-F, that it has been conditioned on.
6. $A \perp F \mid E, C$. It contains a collider, B->E<-F, that it has been conditioned on, but it contains a non collider that has been conditioned on. Conditioning on E opens the path, but conditioning on C closes it again.

**Confounding bias**

The first significant cause of bias is confounding. It happens when the treatment and the outcome share a common cause.

![[Pasted image 20250608124058.png]]

**Selection Bias**

If confounding bias happens when we don’t control for a common cause, selection bias is more related to effects. One word of caution here, economists tend to refer to all sorts of biases as selection bias.

Often, selection bias arises when we control for more variables than we should. It might be the case that the treatment and the potential outcome are marginally independent but become dependent once we condition on a collider.

Among them, you control for investments. But investment is not a common cause of education and wage. Instead, it is a consequence of both. More educated people both earn more and invest more. Also, those who make more invest more. Since investment is a collider, by conditioning on it, you are opening a second path between the treatment and the outcome, which will make it harder to measure the direct effect. ... But by doing so, you are also indirectly and inadvertently not allowing wages to change much.

![[Pasted image 20250608124409.png]]

As a side note, all of this we’ve discussed is true if we condition on any descendent of a common effect.

A similar thing happens when we condition on a mediator of the treatment.

![[Pasted image 20250608124550.png]]


## 05 - The Unreasonable Effectiveness of Linear Regression

$$
y_i=\beta_0+\kappa T_i + \beta_1 X_{1i} + \ldots + \beta_k X_{ki} + u_i
$$
It means that the coefficient of a multivariate regression is the bivariate coefficient of the same regressor **after accounting for the effect of other variables in the model**.

... Even if we can’t use randomised controlled trials to keep other factors equal between treated and untreated, regression can do this by including those same factors in the model, even if the data is not random!

OVB = Omitted Variable Bias

To put it more succinctly, we say that **there is no OVB if all the confounding variables are accounted for in the model**. We can also leverage our knowledge about causal graphs here.

... This is to show that **causal inference with non-random or observational data should always be taken with a grain of salt**. We can never be sure that all confounders were accounted for.


## 06 - Grouped and Dummy Regression

This phenomenon of having a region of low variance and another of high variance is called **heteroskedasticity**.

weighting for the size of each group, if the variables are grouped

interaction terms for dummy variables


## 07 - Beyond Confounders

Fortunately, regression can help us lower this variability. The trick is to use additional controls. **If a variable is a good predictor of the outcome, it will explain away a lot of its variance**.

But maybe controlling for it lowers the variance, right? Well, not true again. In order for a control to lower the variance, it has to be a good predictor of the outcome, not of the treatment, which is the case here.

Still, we might want to control it right? It can’t hurt… Or can it?

Surprisingly, it can hurt!

So the bottom line is that we should add controls that are both correlated with the treatment and the outcome (confounder), like the severity in the model above. We should also add controls that are good predictors of the outcome, even if they are not confounders, because they lower the variance of our estimates. However, we should **NOT** add controls that are just good predictors of the treatment, because they will increase the variance of our estimates.

... However, both are part of the causal effect of the email, so we don’t want to hold them fixed. Instead, we could argue that email increases payments precisely because it boosts the agreement rate. If we fix those variables, we are removing some of the true effect from the email variable.

This sort of bias is so pervasive it has its own name. While confounding is the bias from failing to control for a common cause, **selection bias is when we control for a common effect or a variable in between the path from cause to effect.**

**Conditional-on-Positives**
$$
E[Y | T] = E[Y|Y > 0, T] \cdot P(Y > 0|T)
$$
![[Pasted image 20250608134305.png]]

The issue is in estimating the COP part. **It will be biased even under random assignment**. On an intuitive level, it’s not crazy to think that some units are zeros only because they didn’t get the treatment. The treatment would cause them to **not** be zeros.

Knowing this, if we remove the zeros, we will keep the never zeros on both the treated and the control group. But we would remove from the control those that flip from zero to not zero with the treatment.

![[Pasted image 20250608134509.png]]

![[Pasted image 20250608134540.png]]

![[Pasted image 20250608134833.png]]


## 08 - Instrumental Variables

One way to control for OVB is, well, adding the omitted variable into our model. However, that is not always possible, mostly because we simply don’t have data on the omitted variables.

Another way of saying this is that this instrument Zi is uncorrelated with Y0, but it is correlated with T. This is sometimes referred to as the exclusion restriction.

$$
\begin{align}
Y_i &= \beta_0 + \kappa T_i + v_i \\
v_i &= \beta W_i + u_i
\end{align}
$$
where $W$ is a confounder.
By definition, the instrumental variable $Z$ should satisfy: $Cov(Z, v) = 0$

$$
Cov(Z, Y) = Cov(Z, \beta_0 + \kappa T_i + v_i) = \kappa Cov(Z, T) + Cov(Z, v) = \kappa Cov(Z, T)
$$
two stage regression
$$
\kappa = \frac{Cov(Y_i, Z_i) / V(Z_i)}{Cov(T_i, Z_i) / V(Z_i)} = \frac{\text{Reduced Form}}{\text{1st Stage}}
$$

1. $Cov(Z, T) \neq 0$. This is saying that we should have a strong 1st stage, or that the instrument indeed impacts the treatment variable.
2. $Y \perp Z | T$. This is the exclusion restriction, stating that the instrument Z only affects the outcome Y through the treatment T.

Unfortunately, we can’t verify the second IV condition. We can only argue in favor of it.

![[Pasted image 20250608140153.png]]

However, it doesn’t matter how strong the second stage is if we have a weak first stage. A weak first stage means that the instrument has only a very small correlation with the treatment. Therefore, we can’t learn much about the treatment from the instrument.

![[Pasted image 20250608140409.png]]

As we can see in the plots above, estimates vary wildly when the correlation between T and Z is weak. This is because the SE also increases a lot when the correlation is low.

Another thing to notice is that **2SLS is biased**! Even with high correlation, the parameter estimate still does not reach the true ATE of 2.0. Actually, 2.0 is not even in the 95% CI! 2SLS is only consistent, which means that it approaches the true parameter value if the sample size is big enough.

1. 2SLS is biased towards OLS. This means that if OLS has a negative/positive bias, 2SLS will also have it. The advantage of 2SLS is that it is at least consistent, where OLS is not, in the case of omitted variables. In the example above, our unobserved U impacts negatively the outcome but its positively correlated with the treatment, which will result in a negative bias. That is why we are seeing the ATE estimate below the true value (negative bias).

2. The bias will increase with the number of instruments we add. If we add too many instruments, 2SLS becomes more and more like OLS.

Besides knowing how this bias behaves, a final piece of advice is to avoid some **common mistakes when doing IV**:

1. Doing IV by hand. As we’ve seen, IV by hand will result in wrong standard errors, even if the parameter estimates are right. The SE won’t be completely off. Still, why do it if you can use software and get the right SE?

2. Using anything other than OLS on the 1st stage. Lots of Data Scientist encounter IV and think they can do better. For example, they see a dummy treatment and think about replacing the 1st stage by a logistic regression, after all, they are predicting a dummy variable, right?. The problem is that this is plain wrong. The consistency of IV relies on a property that only OLS can give, which is the orthogonality of the residuals, so anything different than OLS on the 1st stage will yield something biased. (OBS: there are some modern techniques that use Machine Learning for IV, but their results have been, at best, questionable).

(when you do it by hand)

![[Pasted image 20250608141125.png]]


## 09 - Non Compliance and LATE

It partitions the world into 4 kinds of subjects, depending on how they respond to the instrumental variable.

1. Compliers
2. Never Takers
3. Always Takers
4. Defiers

You see, if we do this, we are actually answering a different question than the one we intended to. We would be finding the **causal effect of treatment assignment**, not of the treatment itself. ... is the causal effect of treatment assignment an unbiased estimate of the ATE?

As it turns out, it is not. Because of non compliance, the outcome of those assigned to the treatment will be pushed in the direction of the outcome of those assigned to the control.

![[Pasted image 20250608141528.png]]

**Local average treatment effect** (LATE) makes it explicit the population for which we can estimate the causal effect.

This is the independence Assumption. This says that the instrument is as good as randomly assigned. In other words, Z, the instrument, is not correlated with the potential treatments, which is the same as saying that people in different instrument groups are comparable. -> does not target any particular "type" of person, e.g. always-takers or compliers

$Z$ = assignment of treatment
potential treatment $T_{0i}, T_{1i}$ $\neq$ actual treatment $T$

1. Compliers means that Ti1>Ti0
2. Never Takers Ti1=Ti0=0
3. Always Takers Ti1=Ti0=1

The conclusion of this is that IV says nothing about the effect on never takers, always takers or defiers, because the treatment is unchanged for them! **IV only finds the treatment effect for the compliers**.

... This shows that the result with 2SLS is much lower than the one we got with OLS: 3.29 against 27.60. This makes sense, since the causal effect estimated with OLS is positively biased. We also need to remember about LATE. 3.29 is the average causal effect on compliers. Unfortunately, we can’t say anything about those never takers.


## 10 - Matching

This estimate is done by 1) partitioning the data into confounder cells, in this case, men and women, 2) estimating the effect on each cell and 3) combining the estimate with a weighted average, where the weight is the sample size of the cell or covariate group. ... This is called a **non-parametric estimate**, since it places no assumption on how the data was generated.

If we control for sex using **regression**, we will add the assumption of linearity. ... Instead, regression uses weights that are proportional to the variance of the treatment in that group.

**The Subclassification Estimator**
$$
ATE = \int\left(E[Y|X,T=1] - E[Y|X,T=0]\right)dP(x)
$$

**Matching Estimator**

Since some sort of confounder X makes it so that treated and untreated are not initially comparable, I can make them so by **matching each treated unit with a similar untreated unit**.

we have to define some measurement of proximity to compare how units are close to each other. One common metric for this is the euclidean norm ||Xi−Xj||. This difference, however, is not invariant to the scale of the features. This means that features like age, that take values on the tenths, will be much less important when computing this norm compared to features like income, which take the order of hundreds. For this reason, before applying the norm, we need to scale the features so that they are on roughly the same scale.

$$
\begin{align}
\hat{ATE} &= \frac{1}{N}\sum_{i=1}^N (2T_i - 1)(Y_i - Y_{jm}(i)) \\
\hat{ATET} &= \frac{1}{N_1} \sum \left(Y_i - Y_{j(i)} \right)
\end{align}
$$
where $Y_{jm}(i)$ is the sample from the other treatment group which is most similar to $Y_i$.

Bias arises when the matching discrepancies are huge. Fortunately, we know how to correct it. Each observation contributes (μ0(Xi)−μ0(Xj(i))) to the bias so all we need to do is subtract this quantity from each matching comparison in our estimator. To do so, we can replace μ0(Xj(i)) with some sort of estimate of this quantity μ0^(Xj(i)), which can be obtained with models like linear regression.

$$
\hat{ATET} = \frac{1}{N_1} \sum \left((Y_i - Y_{j(i)}) - (\hat{\mu_0}(X_i) - \hat{\mu_0(X_{j(i)})}) \right)
$$


## 11 - Propensity Score

Propensity score comes from the realisation that you don’t need to directly control for confounders X to achieve conditional independence $(Y_1,Y_0) \perp T \mid X$. Instead, it is sufficient to control for a balancing score $E[T|X]$. This balancing score is often the conditional probability of the treatment, $P(T|X)$, also called the propensity score e(x).
$$
(Y_1, Y_0) \perp T \mid e(x)
$$

![[Pasted image 20250608152739.png]]

If I know what e(x) is, X alone tells me nothing more that can help me learn what T would be. Which means that controlling for e(x) acts the same way as controlling for X directly.

![[Pasted image 20250608153029.png]]

Notice that this estimator requires that e(x) and 1−e(x) are larger than zero. In words, this means that everyone needs to have at least some chance of receiving the treatment and of not receiving it. Another way of stating this is that the treated and untreated distributions need to overlap. This is the **positivity assumption** of causal inference.

We can also use the propensity score to find evidence of confounding. If a segmentation of the population has a higher propensity score than another, it means that something which is not random is causing the treatment. If that same thing is also causing the outcome, we have confounding.

![[Pasted image 20250608153240.png]]

![[Pasted image 20250608153246.png]]

To compute the standard error for the IPTW estimator, we can use the formula of the variance of a weighted average.

σw2=∑i=1nwi(yi−μ^)2∑i=1nwi

However, we can only use this if we have the true propensity score. If we are using the estimated version of it, P^(x), we need to account for the errors in this estimation process. The easiest way of doing this is by **bootstrapping** the whole procedure. This is achieved by sampling with replacement from the original data and computing the ATE like we did above. We then repeat this many times to get the distribution of the ATE estimate.

... **Propensity score doesn’t need to predict the treatment very well. It just needs to include all the confounding variables**. If we include variables that are very good in predicting the treatment but have no bearing on the outcome this will actually increase the variance of the propensity score estimator.

To see this, consider the following example (adapted from Hernán’s Book). You have 2 schools, one of them apply the growth mindset seminar to 99% of its students and the other to 1%. Suppose that the schools have no impact on the treatment effect (except through the treatment), so it’s not necessary to control for it. If you add the school variable to the propensity score model, it’s going to have a very high predictive power. However, by chance, we could end up with a sample where everyone in school A got the treatment, leading to a propensity score of 1 for that school, which would lead to an infinite variance.

... We actually need to construct the prediction in a way that controls for confounding, not in a way to predict the treatment.

**Propensity Score Matching**

If we control for the propensity score, we now estimate a ATE of 0.39, which is lower than the 0.47 we got previously with a regression model without controlling for the propensity score. We can also use matching on the propensity score. This time, instead of trying to find matches that are similar in all the X features, we can find matches that just have the same propensity score.

(matching on propensity scores) ... One final word of caution here is that the above standard errors are wrong, as they don’t account for the uncertainty in the estimation of the propensity score. Unfortunately, [bootstrap doesn’t work with matching](https://economics.mit.edu/sites/default/files/publications/ON%20THE%20FAILURE%20OF%20THE%20BOOTSTRAP%20FOR.pdf).


## 12 - Doubly Robust Estimation

![[Pasted image 20250608153952.png]]

First, it is called doubly robust because it only requires one of the models, P^(x) or μ^(x), to be correctly specified.

if $\hat{μ_1}(x)$ is correct, then $E[T_i(Y_i−\hat{μ_1}(X_i))]=0$.

![[Pasted image 20250608154235.png]]

Now, assume that the propensity score $\hat{P}(X_i)$ is correctly specified. In this case, $E[T_i−\hat{P}(X_i)]=0$, which wipes out the part dependent on $\hat{μ_1}(X_i)$.

... Its magic happens because in causal inference, there are two ways to remove bias from our causal estimates: you either model the **treatment mechanism** or the **outcome mechanism**. If either of these models are correct, you are good to go.

One caveat is that, in practice, it’s very hard to model precisely either of those. More often, what ends up happening is that neither the propensity score nor the outcome model are 100% correct. They are both wrong, but in different ways. When this happens, it is not exactly settled [1](https://www.stat.cmu.edu/~ryantibs/journalclub/kang_2007.pdf) [2](https://arxiv.org/pdf/0804.2969.pdf) [3](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2798744/) if it’s better to use a single model or doubly robust estimation. As for me, I still like using them because at least it gives me two possibilities of being correct.


## 13 - Difference-in-Differences

... In all these cases, you have a period before and after the intervention and you wish to untangle the impact of the intervention from a general trend.

![[Pasted image 20250608155207.png]]

One obvious problem with Diff-in-Diff is failure to satisfy the parallel trend assumption.

![[Pasted image 20250608155202.png]]


## 14 - Panel Data and Fixed Effects

... But what would happen if we had more periods? Or more groups? Turns out this setup is so common and powerful for causal inference that it gets its own name: panel data.

One way to see the parallel (or common) trends assumptions is as an independence assumption. If we recall from very early chapters, the independence assumption requires that the treatment assignment is independent from the potential outcomes: 

$$Y_d \perp D \mid X$$
If the traditional independence assumption states that the treatment assignment can’t be related to **the levels** of potential outcomes, the parallel trends states that the treatment assignment can’t be related to **the growth** in potential outcomes over time.

$$
\left(Y_d(t) - Y_d(t - 1)\right) \perp D
$$

... All we need to do is create dummy variables indicating that person and add that to a linear model. This is what we mean when we say we can control for the person itself: we are adding a variable (dummy in this case) that denotes that particular person. When estimating the effect of marriage on income with this person dummy in our model, regression finds the effect of marriage **while keeping the person variable fixed**. Adding this unit dummy is what we call a fixed effect model.

Now, remember how I’ve said that using panel data with a fixed effect model is as simple as adding a dummy for the entities. It’s true, but in practice, we don’t actually do it. Imagine a dataset where we have 1 million customers. If we add one dummy for each of them, we would end up with 1 million columns, which is probably not a good idea. Instead, we use the trick of partitioning the linear regression into 2 separate models. We’ve seen this before, but now is a good time to recap it. Suppose you have a linear regression model with a set of features X1 and another set of features X2.

![[Pasted image 20250608155700.png]]

... First, we use the dummies to predict the outcome and the feature. These are steps 1 and 2 above.

![[Pasted image 20250608160208.png]]

Notice that fixed effect is fitting **one regression line per city**. Also notice that the lines are parallel. The slope of the line is the effect of marketing costs on in-app purchase. So the **fixed effect is assuming that the causal effect is constants across all entities**, which are cities in this case.

![[Pasted image 20250608160256.png]]

Just like we did a fixed effect for the individual level, we could design a fixed effect for the time level. If adding a dummy for each individual controls for fixed individual characteristics, adding a time dummy would control for variables that are fixed for each time period, but that might change across time.

There are situations where even panel data won’t help you. ... The most obvious one is when you have confounders that are changing in time. Fixed effects can only eliminate bias from attributes that are constant for each individual.

Another less obvious case when fixed effect fails is when you have **reversed causality**. For instance, let’s say that it isn’t marriage that causes you to earn more. Is earning more that increases your chances of getting married.


## 15 - Synthetic Control

... However, note that the sample size here is 4, which is also the number of parameters in our Diff-in-Diff models. In this case, the standard error is not well defined, so what should we do? Another problem is that Florianopolis might not be as similar to Porto Alegre as we would want to.

To work around this, we will use what is known as [**“the most important innovation in the policy evaluation literature in the last few years”**](https://www.aeaweb.org/articles?id=10.1257/jep.31.2.3), Synthetic Controls. It is based on a simple, yet powerful idea. We don’t need to find any single unit in the untreated that is very similar to the treated. Instead, we can forge our own as a combination of multiple untreated units ...

For each unit j and each time t, we observe the outcome Yjt. For each unit j and period t, define YjtN as the potential outcome without intervention and YjtI, the potential outcome with intervention. Then, the effect for the treated unit j=1 at time t, for t>T0 is defined as
$$
\tau_{1t} = Y_{1t}^I - Y_{1t}^N
$$

Since unit j=1 is the treated one, Y1tI is factual but Y1tN is not. The challenge then becomes how do we estimate Y1tN. Notice how the treatment effect is defined for each period, which means it can change in time. It doesn’t need to be instantaneous. It can accumulate or dissipate. To put it in a picture, the problem of estimating the treatment effect boils down to the problem of **estimating what would have happened to the outcome of unit j=1 if it had not been treated**.

![[Pasted image 20250608160820.png]]

To estimate the treatment effect with synthetic control, we will try to build a “fake unit” that resembles the treated unit before the intervention period. Then, we will see how this “fake unit” behaves after the intervention. The difference between the synthetic control and the unit that it mimics is the treatment effect.

... This is the case where, even if T is large, N is also large, which gives too much flexibility to our linear regression model. If you are familiar with regularized models, know that you could use Ridge or Lasso regression to fix this. Here, we will look at another more traditional way to avoid overfitting.

One way to play safer is to constrain our synthetic control to only do interpolation. To do so, we will restrict the weights to be positive and sum up to one.

![[Pasted image 20250608161044.png]]

Notice two things here. First, interpolation won’t be able to create a perfect match of the treated unit in this case. This is because the treated is the unit with the smallest number of sales and the highest price. Convex combinations can only replicate exactly features that are in between the control units.

Another thing to notice is that interpolation is sparse. We will project the treated unit on a wall of the convex hull and this wall is defined only by a few units.

![[Pasted image 20250608161115.png]]

Here, we will use the idea of Fisher’s Exact Test. Its intuition is very simple. We permute the treated and control exhaustively. Since we only have one treated unit, this would mean that, for each unit, we pretend it is the treated while the others are the control.

... So what this does is it pretends that the treatment actually happened for another state, not California, and see what would have been the estimated effect for this treatment that didn’t happen. Then, we see if the treatment in California is sufficiently larger when compared to the other fake treatment. The idea is that for states that weren’t actually treated, once we pretend they were, we won’t be able to find any significant treatment effect.

![[Pasted image 20250608161348.png]]

if we want to test the one sided hypothesis that the effect in California is below zero, we can estimate the P-value as the proportion of times the effect in California is bigger than all the estimated effects.


## 16 - Regression Discontinuity Design

![[Pasted image 20250608161556.png]]

This is, in its own way, a sort of Local Average Treatment Effect (LATE), since we can only know it at the threshold. In this setting, we can think of RDD as a local randomized trial. For those at the threshold, the treatment could have gone either way and, by chance, some people fell below the threshold, and some people fell above.

$$
y_i = \beta_0 + \beta_1r_i + \beta_2 1\{r_i > c\} + \beta_3 1\{r+i > c\}r_i
$$

![[Pasted image 20250608161748.png]]

... What can happen is that regression might focus too much on fitting the other data points at the cost of a poor fit at the threshold. If this happens, we might get the wrong measure of the treatment effect.

One way to solve this is to give higher weights for the points that are closer to the threshold. There are many ways to do this, but a popular one is to reweight the samples with the **triangular kernel**

$$
K(R, c, h) = 1\{\vert R - c \vert \leq h \} * \left(1 - \frac{\vert R - c \vert}{h}\right)
$$

... Here, the regression discontinuity is **fuzzy**, rather than sharp. Notice how the probability of getting the diploma doesn’t jump from zero to one at the threshold. But it does jump from something like 50% to 90%.

![[Pasted image 20250608162138.png]]

Just like when we have the potential outcome, we have the potential treatment status in this situation. T1 is the treatment everyone would have received had they been above the threshold. T0 is the treatment everyone would have received had they been below the threshold. As you’ve might have noticed, we can think of the **threshold as an Instrumental Variable**. Just as in IV, if we naively estimate the treatment effect, it will be biased towards zero.

![[Pasted image 20250608162213.png]]

![[Pasted image 20250608162233.png]]

... Students are not manipulating where they fall on the threshold. Just for illustrative purposes, the second plot shows what bunching would look like if students could manipulate where they fall on the threshold.


## 17 - Predictive Models 101

The first few chapters of part two will focus on estimating heterogeneous treatment effects. We will move from a world where all we cared about was the average treatment effect, $E[Y_1−Y_0]$, to one where we want to know how different units respond differently to the treatment $E[Y_1−Y_0 \mid X]$. This is the world where personalisation is paramount.

The key takeaway here is that **if you can frame your business problem as a prediction problem, then machine learning is probably the right tool for the job**. I really can’t emphasize this enough. With all the hype around machine learning, I feel that people forget about this very important point and often end up making models that are very good at predicting something totally useless.


## 18 - Heterogeneous Treatment Effects and Personalization

Causal inference is then the process of estimating the causal relationship between T and Y under context X. Once we’ve done that, optimizing Y is just a matter of setting the treatment T in an optimal way

$$
\underset{T}{\text{argmax}} \, E[Y \mid X, T]
$$
In that sense, beyond the positive aspect of causal inference, we also have a normative motivation.

Now, things will become less black and white. We want more than just the average treatment effect. We will allow the treatment to impact positively some people but not others.

We want to personalize the treatment. In more technical terms, we want to estimate the Conditional Average Treatment Effect (CATE)

$$
E[Y_1 - Y_0 \mid X] \quad \text{or} \quad E[y'(t) \mid X]
$$

We’ve also contrasted this goal with that of a predictive model. Namely, we are rethinking the estimation task, from predicting Y in it’s raw format to predicting how $Y$ changes with $T$, $\delta y / \delta t$.


## 19 - Evaluating Causal Models

The trick is to use aggregate measurements of sensitivity. Even if you can’t estimate sensitivity individually, you can do it for a group and that is what we will leverage here.

![[Pasted image 20250615134125.png]]

In the image, we can see that the first model is somewhat good at predicting sales (high correlation with sales), but the groups it produces have roughly the same treatment effect, as shown in the plot on the bottom. Two of the three segments have the same sensitivity and only the last one has a different, lower sensitivity.

Once we have the ordered groups, we can construct what we will call the **Cumulative Sensitivity Curve**. We first compute the sensitivity of the first group; then, of the first and the second and so on, until we’ve included all the groups. In the end, we will just compute the sensitivity for the entire dataset.

![[Pasted image 20250615134747.png]]

Second, a model is better to the degree that
$$
y^′(t)_k−y^′(t)_{k+a}
$$
is the largest, for any k and a>0. The intuition being that not only do we want the sensitivity of the top k units to be higher than the sensitivity of the units below them, but we want that difference to be as large as possible.

![[Pasted image 20250615134913.png]]

Needless to say, none of our models gets even close to an ideal sensitivity curve. The random model `rand_m` oscillates around the average sensitivity and never goes too far away from it. This means that the model can’t find groups where the sensitivity is different from the average one. As for the predictive model `pred_m`, it appears to be reversely ordering sensitivity, because the curve starts below the average sensitivity. Not only that, it also converges to the average sensitivity pretty quickly, at around 50% of the samples. Finally, the causal model `sensitivity_m` seems more interesting. It has this weird behavior at first, where the cumulative sensitivity increases away from the average, but then it reaches a point where we can treat about 75% of the units while keeping a pretty decent sensitivity of almost 0. This is probably happening because this model can identify the very low sensitivity (high price sensitivity) days. Hence, provided we don’t increase prices on those days, we are allowed to do it for most of the sample (about 75%), while still having a low price sensitivity.

The next idea is a very simple, yet powerful improvement on top of the cumulative sensitivity. We will multiply the cumulative sensitivity by the proportional sample size. For example, if the cumulative sensitivity is, say -0.5 at 40%, we will end up with -0.2 (-0.5 * 0.4) at that point. Then, we will compare this with the theoretical curve produced by a random model.

![[Pasted image 20250615135143.png]]

... We managed to evaluate how good are models in ordering sensitivity even though we didn’t have a ground truth to compare against.


## 20 - Plug-and-Play Estimators

We’ve seen how to estimate the CATE using a linear regression with interactions between the treatment and the features

$$
y_i=\beta_0+\beta_1t_i+ \beta_2X_i+\beta_3t_iX_i+e_i.
$$

If we estimate this model, we can get estimates for $\tau(x)$

$$
\hat\tau(x)=\hat\beta_1+\hat\beta_3X_i
$$

Still, the linear models have some drawbacks. The main one being the linearity assumption on $X$. Notice that you don’t even care about $\beta_2$ on this model. But if the features $X$ don’t have a linear relationship with the outcome, your estimates of the causal parameters $\beta_1$ and $\beta_3$ will be off.

Here is a crazy idea: let’s transform the outcome variable by multiplying it with the treatment.

$$
Y_i^∗=2Y_i∗T_i−2Y_i∗(1−T_i)
$$

So, if the unit was treated, you would take the outcome and multiply it by 2. If it wasn’t treated, you would take the outcome and multiply it by -2.

To understand that, we need a bit of math. Because of random assignment, we have that $T \perp Y(0),Y(1)$, which is our old unconfoundedness friend. That implies that $E[T,Y(t)]=E[T]∗E[Y(t)]$, which is the definition of independence.

![[Pasted image 20250615140159.png]]

Now that we’ve solved the simple case, what about the more complicated case, where treatment is not 50% 50%, or not even randomly assigned? As it turns out, the answer is a bit more complicated, but not much. First, if we don’t have random assignment, we need at least conditional independence $T \perp Y(1),Y(0) \mid X$. That is, controlling for $X$, $T$ is as good as random. With that, we can generalize the transformed target to

$$
Y_i^∗=Y_i∗\frac{T_i−e(X_i)}{e(X_i)(1−e(X_i))}
$$

where $e(X_i)$ is the propensity score.

![[Pasted image 20250615140612.png]]

That is actually one of the biggest downsides of this target transformation technique. With this target transformation, you do get a lot of simplicity, since you can just transform the target and use any ML estimator to predict **heterogeneous** treatment effects. The cost of it is that you get a lot of **variance**. That’s because the transformed target is a very noisy estimate of the individual treatment effect and that variance gets transferred to your estimation.

Another obvious downside of the target transformation method is that it only works for discrete or binary treatments. ... So, even though I couldn’t find anything regarding target transformations for continuous treatment, I came up with something that works in practice. Just keep in mind that I don’t have a super solid econometric research around it.

$$
\tau(x)=E[\partial Y_i(t)\mid X]=E[\tau_i \mid X]
$$
$$
Y_i^∗=(Y_i−\bar{Y})\frac{(T_i−\bar{T})}{\sigma_T^2}
$$
In plain English, we would transform the original target by subtracting the mean from it, then we would multiply it by the treatment, from which we’ve also subtracted the mean from. Finally, we would divide it by the treatment variance. Alas, we have a target transformation for the continuous case.

To show that this target transformation works, we need to remember that we are estimating the parameter for a local linear model

$$
Y_i=\alpha+\beta T_i+e_i \mid X_i=x
$$

In our example, those would be the linear models for the hot and cold days. Here, we are interested in the $\beta$ parameter, which is our conditional elasticity or CATE.

![[Pasted image 20250615141246.png]]

Having talked about the continuous case, there is still an elephant in the room we need to adress. We’ve assumed a linearity on the treatment effect. However, that is very rarely a reasonable assumption. Usually, treatment effects saturate in one form or another.

The problem here is that **elasticity or treatment effect changes with the treatment itself**. In our example, the treatment effect is more intense at the beginning of the curve and smaller as prices get higher.

There is no easy way out of this problem and I confess I’m still investigating what works best. For now, the thing that I do is try to think about the functional form of the treatment effect and somehow linearize it.

... As a side note, besides target transformation, this method also goes by the name of **F-Learner**.


## 21 - Meta Learners

Meta learners are a simple way to leverage off-the-shelf predictive machine learning methods in order to solve the same problem we’ve been looking at so far: estimating the CATE. Again, none of them is the single best one and each one has its weakness.

The first learner we will use is the **S-Learner**. This is the simplest learner we can think of. We will use a single (hence the S) machine learning model $M_s$ to estimate

$$
μ(x)=E[Y|T,X]
$$

To do so, we will simply include the treatment as a feature in the model that tries to predict the outcome $Y$.

Then, we can make predictions under different treatment regimes. The difference in predictions between the test and control will be our CATE estimate

$$
\hat\tau(x)_i=M_s(X_i,T=1)−M_s(X_i,T=0)
$$

![[Pasted image 20250615142201.png]]

To evaluate this model, we will look at the cumulative gain curve in the test set. I’m also plotting the gain curve in the train. Since the train is biased, this curve cannot give any indication if the model is good, but it can point us out if we are overfitting to the training set. When this happens, the curve in the train set will be super high. If you want to see what that looks like, try replacing the `max_depth` parameter from 3 to 20.

Depending on the type of data that you have, the S-learner might do better or worse. In practice, I find that the S-learner is a good first bet for any causal problem, mostly due to its simplicity. Not only that, the S-learner can handle both continuous and discrete treatments, while the rest of the learners in this chapter can only deal with discrete treatments.

The major disadvantage of the S-learner is that it tends to bias the treatment effect towards zero. Since the S-learner employs what is usually a regularized machine learning model, that regularization can restrict the estimated treatment effect. ‪Chernozhukov‬ et al (2016) outline this problem using simulated data:

![[Pasted image 20250615142254.png]]

Here, they plot the difference between the true causal effect (red outline) and the estimated causal effect, $\tau−\hat\tau$, using an S-learner. The estimated causal effect is heavily biased.

The **T-learner** tries to solve the problem of discarding the treatment entirely by forcing the learner to first split on it. Instead of using a single model, we will use one model per treatment variable. In the binary case, there are only two models that we need to estimate (hence the name T):

$$
\begin{align}
\mu_0(x) &= E[Y|T=0,X] \\
\mu_1(x) &= E[Y|T=1,X]
\end{align}
$$

Then, at prediction time, we can make counterfactual predictions for each treatment and get the CATE as follows.

$$
\hat\tau(x)_i=M_1(X_i)−M_0(X_i)
$$

![[Pasted image 20250615142438.png]]

The T-Learner avoids the problem of not picking up on a weak treatment variable, but it can still suffer from regularization bias.

![[Pasted image 20250615142533.png]]

What happens here is that the model for the untreated can pick up the non linearity, but the model for the treated cannot, because we’ve used regularization to deal with a small sample size.

The **X-learner** is significantly more complex to explain than the previous learner, but its implementation is quite simple, so don’t worry. The X-Learner has two stages and a propensity score model. The first one is identical to the T-learner. First, we split the samples into treated and untreated and fit a ML model for the treated and for control.

$$
\begin{align}
\hat M_0(X) &≈ E[Y|T=0,X] \\
\hat M_1(X) &≈ E[Y|T=1,X]
\end{align}
$$

Now, things start to take a turn. For the second stage, we impute the treatment effect for the control and for the treated using the models above

$$
\begin{align}
\hat\tau(X,T=0) &= \hat M_1(X,T=0)−Y_{T=0} \\
\hat\tau(X,T=1) &= Y_{T=1}− \hat M_0(X,T=1)
\end{align}
$$

Then, we fit two more models to predict those effects

$$
\begin{align}
\hat M_{τ0}(X) &≈ E[\hat\tau(X)|T=0] \\
\hat M_{τ1}(X) &≈ E[\hat\tau(X)|T=1]
\end{align}
$$
Now, we need a way to combine the two in a way that gives more weight to the correct model. Here is where the propensity score model comes to play. Let $\hat e(x)$ be the propensity score model, we can combine the two second stage models as follows:

$$
\hat\tau(x)=\hat M_{\tau0}(X)\hat e(x) + \hat M_{τ1}(X)(1−\hat e(x))
$$

Since there are very few treated units, e^(x) is very small. This will give a very small weight to the wrong model M^τ0(X).

![[Pasted image 20250615143133.png]]

As we can see, compared to the T-learner, the X-learner does a much better job in correcting the wrong CATE estimated at the non linearity. In general the X-learner performs better when a treatment group is much larger than the other.

![[Pasted image 20250615143144.png]]

Again, the simplest thing we can do is using a single or **S-learner** with the treatment as a feature. This tends to work well when the treatment is not a **weak predictor** of the outcome. But if that’s not the case, the S-learner tends to be biased towards zero or even drop the treatment entirely. Adding a bit more complexity, we can force the learner to pick up the treatment by using a **T-learner**. Here, we fit one Machine Learning model per treatment level. This works fine when there are enough samples for all treatment levels, but it can fail when one treatment level has a **small sample size**, forcing a model to be **heavily regularized**. To fix that, we can add another level of complexity using an **X-learner**, where we have two fitting stages and we use a propensity score model to correct potential mistakes from models estimated with very few data points.


## 22 - Debiased/Orthogonal Machine Learning

Another bonus is that Debiased/Orthogonal ML works for both continuous and discrete treatments, something that neither the T, not X learner could do.

**Frisch-Waugh-Lovell**

Suppose you have a linear regression model with a set of features $X_1$ and another set of features $X_2$. You then estimate that model’s parameters.

$$
\hat Y = \hat\beta_1 X_1+ \hat\beta_2 X_2
$$

where $X_1$ and $X_2$ are feature matrices (one column per feature and one row per observation) and $\hat\beta_1$ and $\hat\beta_2$ are row vectors. You can get the exact same β1^ parameter by doing the following steps

1. regress the outcome y on the second set of features $\hat y^∗= \hat\gamma_1 X_2$
2. regress the first set of features on the second $\hat X_1= \hat\gamma_2 X_2$
3. obtain the residuals $\tilde X_1=X_1−\hat X_1$ and $\tilde y_1=y− \hat y^*$
4. regress the residuals of the outcome on the residuals of the features $\tilde y=\hat\beta_1 \tilde X_1$

... First, regress the outcome on the features to get outcome residuals. Then, regress the treatment on the features to get treatment residuals. Finally, regress the outcome residuals on the feature residuals. This will yield the exact same estimate as if we regress the outcome on the features and treatment at the same time.

Another way of saying this is that the treatment effect can be derived from **regression on residuals**, where we obtain the residuals from regressing Y on X and regress it on the residuals of regressing T on X. Let’s say that ∼ is the regression operator, so we can summarise FWL theorem as follows.

$$
(Y−(Y∼X))∼(T−(T∼X))
$$

**Double/Debiased ML** can be seen as Frisch, Waugh and Lovell theorem on steroids. The idea is very simple: use ML models when constructing the outcome and treatment residuals:

$$
Y_i−\hat M_y(X_i)= \tau \cdot (T_i− \hat M_t(X_i)) + \epsilon
$$

Where $\hat M_y(X_i)$ is estimating $E[Y|X]$ and $\hat M_t(X_i)$ is estimating $E[T|X]$.

1. Estimate the outcome $Y$ with features $X$ using a flexible ML regression model $M_y$.
2. Estimate the treatment $T$ with features $X$ using a flexible ML regression model $M_t$.
3. Obtain the residuals $\tilde Y = Y−M_y(X)$ and $\tilde T=T−M_t(X)$
4. regress the residuals of the outcome on the residuals of the treatment $\tilde Y= \alpha + \tau \tilde T$
where $τ$ is the causal parameter ATE, which we can estimate, for example, with OLS.

So far, we’ve seen how Double/Debiased ML allow us to focus on estimating the Average Treatment Effect (ATE), but it can also be used to estimate treatment effect heterogeneity or Conditional Average Treatment Effect (CATE). Essentially, we are now saying that the causal parameter τ changes depending on the unit’s covariates.

$$
Y_i−M_y(X_i)=\tau(X_i)\cdot(T_i−M_t(X_i))+\epsilon_i
$$

The Double/Debiased ML procedure with a final linear model is already very good, as we can see by the curve above. But, maybe we can do even better. As a matter of fact, this is a very general procedure that we can understand just like a meta-learner. Nie and Wager called it the **R-Learner**, as a way to recognise the work of Robinson (1988) and to emphasize the role of **residualization**.

![[Pasted image 20250615150449.png]]

... To answer that properly, let’s remember what is the underlying assumption that the Double/Debiased ML makes about the data generating process. These assumptions can be seen in the equation we’ve laid down before.

$$
\tilde Y_i= \tau(X_i)\tilde T_i + e_i
$$

In words, it says that the residualized outcome is equal to the residualized treatment multiplied by the conditional treatment effect. This mean that the **treatment impacts the outcome linearly**.

So, does this mean that the non-parametric model can’t capture the non-linearity of the treatment effect? Again, not really… Rather, what is happening is that Double/ML **finds the locally linear approximation to the non-linear CATE**.

![[Pasted image 20250615150722.png]]

... “Great! I’ll give a lot of discounts to this unit! After all, for every 1 unit in discount, I’ll get 2 in sales”. However, that’s the wrong conclusion. The treatment effect is 2 only at that discount level.

This means you have to be extra careful when extrapolating a nonlinear treatment effect to a new treatment level. If you are not, you might end up making very unprofitable decisions. Another way to put is is that, when treatment effect is not linear, even non-parametric Double/Debiased-ML will **struggle to make counterfactuals outcome predictions**. It will try to linearly extrapolate the treatment effect (TE) from a low treatment level to a high treatment level or the other way around. And, due to the non linearity, that extrapolation will likely be off.

The final idea we will try is a fundamental shift in mentality. We will no longer try to estimate the linear approximation to the CATE. Instead, we will make counterfactual predictions.

Here is how this will go down. First, let’s start with the traditional Double/Debiased-ML formulation, where we have the residualized version of the treatment and outcome.

$$
\tilde Y_i= \tau(X_i) \tilde T_i + e_i
$$

Now, I’ll move the treatment inside the treatment effect function. This allows the treatment effect to be non linear, that is to change with the treatment itself.

$$
\tilde Y_i= \tau(X_i, \tilde T_i) + e_i
$$

... Simply speaking, we will fit a ML model to predict the residualised outcome $\tilde Y$ from the residualized treatment $\tilde T$ together with the features $X$.

Then, once we have this model, we will make 2 step counterfactual predictions. First we will have to make a prediction for the treatment in order to get $\tilde T$, then, we will feed that prediction, along with the features, in our final model $\hat\tau (X_i, \tilde T_i)$.

First and foremost, it has the same problems all ML techniques have when applied naively to causal inference: **bias**. Since the final model is a regularized ML model, this regularization can bias the causal estimate to zero.

The second problem has to do with the ML algorithm you choose. Here, we choose boosted trees. Trees are not very good at making smooth predictions. As a consequence, we can have discontinuities in the prediction curve. You can see that in the plots above: a stepwise behavior here and there. Also, trees are not very good at extrapolating, so this model might output weird predictions for prices never seen before.

![[Pasted image 20250615151215.png]]

![[Pasted image 20250615151227.png]]


## 23 - Challenges with Effect Heterogeneity and Nonlinearity

... But that is not the only challenge. Because treatment effects are so slippery, its estimators are often quite noisy. This has huge practical consequences for applications where we want to segment units by their treatment effect, like when we want to do personalized treatment allocation.

We will now see that, sometimes, we can get a better treatment effect segmentation if we don’t directly try to estimate CATE, but instead focus on another **proxy** target, which usually has less variance. A common case when this happens is when the outcome variable of interest Y is binary.

... How come the feature we know to drive effect heterogeneity, `age`, is worse for personalization when compared with a feature (`estimated_income`) we know not to modify the treatment effect? The answer lies in the **non-linearity of the outcome function**. Although `estimated_income` does not modify the effect of the nudge on the latent outcome, it does once we transform that latent outcome to conversion (at least indirectly). Conversion is not linear. This means that **its derivative changes depending on where you are**.

... the derivative of high conversion is very low. But because conversion is also bounded at zero, it will also have a low derivative if it is already very low.

![[Pasted image 20250615152445.png]]

For instance, in our conversion problem, if the **average conversion is low**, we are at to the left of the logistic curve and the **treatment effect will be higher at high baseline conversion**. This would translate to a nudge policy that advocates for treating (nudging) those customers with an already high probability of conversion. On the other hand, if the **average conversion is high**, we will be to the right side of the logistic curve, where the derivative (and hence the treatment effect) will be **higher for those customers with lower baseline conversion**.

This is certainly a lot to remember, but we can definitely simplify: **just treat whomever is closer to a baseline conversion of 50%**.

... Just like in the case where the outcome was binary, in this example, **the treatment effect is correlated with the outcome**. The higher the sales (lower the price), the lower the absolute treatment effect; the lower the sales (higher the price) the lower the absolute treatment effect. But in this case, the situation is even more complicated because the **effect is not only correlated with the outcome, but with the treatment level**.

So what can you do when treatment effects change depending on where you are in terms of both treatment and outcome? To be honest, this is still an active area of research. In practical terms, the best thing you can do is to be **very careful** when trying to answer which type of customer is more sensitive to the treatment. Make sure that the compared customer types all had the same treatment distribution. And, if not, be very skeptical of extrapolating the treatment effect.


## 24 - The Difference-in-Differences Saga

![[Pasted image 20250615155125.png]]

 In fact, **TWFE (Two Way Fixed Effect), in its usual formulation, turns out to be biased in many real life applications**. This event caused a wave of revisions in multiple studies in economics that relied on this technique.

For simplicity sake let’s consider the FE model without the time effects:

$$
y_{it}= \tau D_{it} + \gamma_i + e_{it}
$$

We can group the assumption this model makes into two groups

1. Functional Form Assumptions:
    - No heterogeneous effects in time (constant effects);
    - Linearity in the covariates;
    - Additive fixed effects.

2. Strict Exogeneity
    - Parallel trends
    - No anticipation
    - No unobserved time varying confounders
    - Past treatment don’t affect current outcome (no carryover)
    - Past outcome don’t affect current treatment (no feedback)

![[Pasted image 20250615155534.png]]

![[Pasted image 20250615155603.png]]

The first three comparisons are no reason for concern, mostly because what they use as control is very well behaved. However, the fourth comparison, late vs early, is problematic. Notice that this comparison uses the early treated as a control. Also notice that this early treated control has a weird behavior. It climbs up sharply at the beginning. That is a reflection of our ATT not being instantaneous, but instead taking 10 days to mature.

![[Pasted image 20250615155702.png]]

More than that, we know it doesn’t work because it is too restrictive. It forces the effect to be the same \(\tau_{it}=\tau \ \forall i, t\), that is, it forces time homogeneity. If that is the problem, an easy fix would be to simply allow for a different effect for each time and unit.

$$
\text{Installs}_{it}=\sum_{i=0}^N\sum_{t=0}^T \tau_{it}D_{it}+\gamma_i+\theta_t+ e_{it}
$$

Unfortunately, we can’t fit that. It would have more parameters than we have data points.

So, a natural improvement on that impractical model above is to allow the effect to change by cohort instead of units:

$$
\text{Installs}_{it}=\sum_{g=0}^G\sum_{t=0}^T \tau_{gt}D_{it}+\gamma_i+\theta_t+ e_{it}
$$

where `G` is the total number of cohorts and `g` marks each individual cohort.

... When using DiD and TWFE, we often invoke the parallel trends assumption, without really thinking about what exactly that assumption means. Sadly, the parallel trends assumption is much more restrictive and less plausible than most people realize.

We can never forget that TWFE (and DiD more generally) is a mix of **both functional form and independence assumptions**. In this chapter, we’ve only tackled the functional form problems, but there is still a big elephant in the room: the parallel trend assumption.


## 25 - Synthetic Difference-in-Differences

In the Diff-in-Diff chapter, we got the treatment effect by estimating the following linear model.

$$
Y_{it}= \beta_0 + \beta_1 Post_t+ \beta_2 Treated_i + \beta_3 Treated_i Post_t + e_{it}
$$

Where `post` is a time dummy indicating that the period is after the treatment and `treated` is a unit dummy marking units as being part of the treated group.

![[Pasted image 20250615160250.png]]

![[Pasted image 20250615160335.png]]

![[Pasted image 20250615160432.png]]

![[Pasted image 20250615160455.png]]

There is also this $ζ^2$ term, which is theoretically motivated, but very complicated to explain, so I will unfortunately leave it as a bit of a mystery. In the reference, you can check the original article, which explains them. We define it like this:

$$
ζ=(N_{tr}∗T_{post})^{1/4} \sigma(\Delta it)
$$

where Δit is the first difference in the outcomes $Y_{it}−Y_{i(t−1)}$ and $\sigma(\Delta it)$ is the standard deviation of this difference. Here is the code to compute it.

![[Pasted image 20250615161258.png]]

staggered adoption

![[Pasted image 20250615161358.png]]

![[Pasted image 20250615161426.png]]

The idea is to run a series of placebo tests, where we pretend a unit from the control pool is treated, when it actually isn’t. Then, we use SDID to estimate the ATT of this placebo test and store its result. We re-run this step multiple times, sampling a control unit each time. In the end, we will have an array of placebo ATTs. The variance of this array is the placebo variance of the SDID effect estimate, which we can use to construct a confidence interval.

![[Pasted image 20250615161621.png]]

Notice how the error for synthetic control is higher than for SDID. Again, that is because SDID captures a lot of the variance in the outcome via its time and unit fixed effects.

First, there is an additional **L2 penalty** in the optimization of the unit weights which makes them more spread out across control units. Second, SDID allows for an **intercept** (and hence, extrapolation) when building such weights. Third, SDID introduces the use of **time weights**, which are not present in either DID nor SC. For this reason, I wouldn’t say SDID is just merging SC and SDID. It is rather building something new, inspired by these two approaches.

... I also wouldn’t say that SDID is better or worse than traditional Synthetic Control. Each of them have different properties that might be appropriate or not, depending on the situation. For example, you might find yourself in a situation where allowing the **extrapolations** from SDID is dangerous. In which case, SC might be a good alternative.



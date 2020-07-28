

# Linear Regression Models

In our visualization of data, we've seen trends, patterns, and variation. Let's now endeavor to describe those trends, patterns, and variation more *precisely* and *quantitatively* by building statistical *models*. In this chapter, we will focus on a class of models for describing quantitative outcome variables: linear regression models.

## Modeling Goals

Broadly, a model is a simplified representation of the world. When we build models, we may have different goals.

One goal when building models is **prediction**. Given data on a **response** or **outcome variable**, $Y$, and one or more **predictor or explanatory variables**, $X$, the goal is to find a mathematical function, $f$, of $X$ that gives good predictions of $Y$.  For example, we might want to be able to predict a customer's chest size knowing their neck size. This $X$ may be a single variable, but it is most often a set of variables. We'll be building up to multivariate modeling over the course of this chapter. 

<div class="reflect">
<p>Can you think of some other concrete examples in which we’d want a model to do prediction? Consider what predictions might be made about you every day…</p>
</div>

What are the qualities of a good model and function, $f$? We want to find an $f(X)$ such that $\hat{y} = f(x)$ is a good predictor of $y$. In other words, we want the model prediction $\hat{y}$ (read, "y hat") to be close to the observed response. We want $y-\hat{y}$ to be small. This difference between the observed value and the prediction, $y-\hat{y}$, is called a **residual**. We'll discuss residuals more later.

Another goal when building models is **description**. We want a model, $f(x)$, to "explain" the relationship between the $X$ and $Y$ variables. Note that an overly complicated model may not be that useful here because it can't help us *understand* the relationship. A more complex model may, however, produce better predictions. [George Box](https://en.wikipedia.org/wiki/George_E._P._Box) is often quoted "All models are wrong but some are useful." Depending on our goal, one model may be more useful than another.

<div class="reflect">
<p>Can you think of some concrete examples in which we’d want a model to do explain a phenomenon? Consider how policy decisions get made…</p>
</div>

To begin, we will consider a simple, but powerful model in which we limit this function, $f(X)$, to be a straight line with a y-intercept, $\beta_0$, and slope, $\beta_1$. ($\beta$ is the Greek letter beta.) The $E[Y | X]$ below stands for the **expected value** of the response variable $Y$ for a *given* value of $X$. 

$$E[Y | X] = \beta_0 + \beta_1\,X$$

This is a **simple linear regression model**. We model the expected value (the average value) of the response variable given an explanatory variable $X$ as a line. It is the foundation of many statistical models used in modern statistics and is more flexible than you may think.

We'll need to find the values of the slope and intercept that give us a line that gets close to the averages in order to explain the general relationship between X and Y. We'll get to this soon.

Once we have values the intercept and slope, we are going to call those estimates and put a hat on them, so $\hat{\beta}_0$ is our **estimated intercept** and our **estimated slope** is $\hat{\beta}_1$. We can use those to make predictions by plugging in a value of x: 

$$\hat{y} = \hat{\beta}_0 +\hat{\beta}_1 x$$

The little hat on top of y means that we're talking about a predicted or estimated value of y, so our model says that the predicted or estimated value of y is equal to an estimated intercept ($hat{\beta}_0$), plus an estimated slope ($hat{\beta}_1$), times x. 

<div class="mathbox">
<p>In the past, you may have seen the equation of a line as</p>
<p><span class="math display">\[y = mx + b\]</span></p>
<p>where <span class="math inline">\(m\)</span> is the slope and <span class="math inline">\(b\)</span> is the y-intercept. We will be using different notation so that it can generalize to multiple linear regression.</p>
<p>The y-intercept is the value when <span class="math inline">\(x=0\)</span> and the slope is change in <span class="math inline">\(y\)</span> for each 1 unit increase of <span class="math inline">\(x\)</span> (“rise over run”).</p>
</div>


## Lines

Let's return to the thought experiment in which you were a manufacturer of button-down dress shirts.


```r
body <- read.delim("Data/bodyfat.txt")

body %>%
    ggplot(aes(x = Neck, y = Chest)) +
    geom_point(color = 'steelblue') + 
    xlab('Neck size (cm)') + 
    ylab('Chest size (cm)') +
    theme_minimal()
```

<img src="03-linear-regression_files/figure-html/unnamed-chunk-4-1.png" width="672" />

<div class="reflect">
<p>If you were to add one or multiple lines to the plot above to help you make business decisions, where would you want it (or them)?</p>
</div>

Let's say you were only going to make one size of shirt. You might want to add a horizontal line at the mean Chest size and a vertical line at the mean Neck size. 


```r
body %>%
    ggplot(aes(x = Neck, y = Chest)) +
    geom_point(color = 'steelblue') + 
    geom_hline(yintercept = mean(body$Chest)) +
    geom_vline(xintercept = mean(body$Neck)) +
    xlab('Neck size (cm)') + 
    ylab('Chest size (cm)') +
    theme_minimal()
```

<img src="03-linear-regression_files/figure-html/unnamed-chunk-6-1.png" width="672" />

We can see that a shirt made to these specifications would fit the "average person." However, this might not serve your market very well. For many people, the shirt would be too tight because their chest and/or neck sizes would be larger than average. For many people, the shirt would be too large because they chest and/or neck sizes would be smaller than average. 

Let's try something else. Let's allow ourselves 5 different sizes (XS, S, M, L, XL). Then, we can cut the neck size variable into 5 groups of equal length and estimate the mean chest sizes within each of these groups. 

<img src="03-linear-regression_files/figure-html/unnamed-chunk-7-1.png" width="672" />

<div class="reflect">
<p>What do these lines tell us for our business venture?</p>
</div>

What if we wanted to be able to make more sizes? Could we get a pretty good sense of what the chest sizes should be for a given neck size? Let's try allowing for 8 different sizes. 

<img src="03-linear-regression_files/figure-html/unnamed-chunk-9-1.png" width="672" />

<div class="reflect">
<p>What are the pros and cons of having a larger number of sizes?</p>
</div>

<div class="reflect">
<p>Stop and think about the data collection process. If you were measuring your own neck size, how precise do you think you could get? What factors might impact that precision?</p>
</div>

We can see from this scatterplot that there is a generally linear relationship between neck and chest size. Perhaps we can find one line to describe the relationship between neck size and chest size and use that line to decide on sizes later.

<img src="03-linear-regression_files/figure-html/unnamed-chunk-12-1.png" width="672" />

<div class="reflect">
<p>What does line tell us for our business venture?</p>
</div>

If the scatterplot between two quantitative variables **resembles a straight line**,

- a straight line could roughly **describe** the mean value of $y$ for each value of $x$.
- a straight line could **describe** how much we'd *expect* $y$ to change based on a 1 unit change in $x$.
- a straight line could help us **predict** the $y$ based on a new value of $x$.


## "Best" fitting line

To choose the "best" fitting line, we need to find estimates of the intercept ($\beta_0$) and slope ($\beta_1$),

$$ E[Y | X] =  \beta_0 + \beta_1\,X $$

that gives us the "best" fit to the $n$ points on a scatterplot, $(x_i,y_i)$ where $i=1,...,n$. 

<div class="reflect">
<p>What do we mean by “best”? In general, we’d like good predictions and a model that describes the average relationship. But we need to be more precise about what we mean by “best”.</p>
</div>

### First idea

One idea of "best" is that we want the line that minimizes the sum of the **residuals**, $e_i = y_i - \hat{y}_i = y_i - ( \hat{\beta}_0 + \hat{\beta}_1x_i)$. The residual is the error in our prediction, the difference between what you observe and what you predict based on the line.

- **Problem**: We will have positive and negative residuals; they will cancel each other out if we add them together. While a good idea, this definition of "best" won't give us what we want. We'll want an idea that deals with the negative signs. 


### Second idea

Another idea of "best" is that we want the line that minimizes the sum of the absolute value of the residuals, $\sum_{i=1}^n |y_i - \hat{y}_i| = \sum_{i=1}^n |e_i|$.

- **Problem**: This definition of "best" results in a procedure referred to as **Least Absolute Deviations**, but there isn't always one unique line that satisfies this. So, while this is a valid definition of "best," this isn't stable as there isn't always one "best" line. 

### Third idea

Lastly, another idea of "best" is that we want the line that minimizes the sum of squared residuals, $\sum_{i=1}^n (y_i - \hat{y}_i)^2= \sum_{i=1}^n e_i^2$.

- This is referred to as **Least Squares** and has a unique solution. We'll will focus on this definition of "best" in this class. It also has some really nice mathematical properties and [connections to linear algebra](https://medium.com/@andrew.chamberlain/the-linear-algebra-view-of-least-squares-regression-f67044b7f39b). 

## Least Squares

Let's try to find the line that **minimizes the Sum of Squared Residuals** by searching over a grid of values for (intercept, slope).

Below is a visual of the sum of squared residuals for a variety of values of the intercept and slope. The surface height is sum of squared residuals for each combination of slope and intercept.

<img src="03-linear-regression_files/figure-html/unnamed-chunk-15-1.png" width="672" />

We can see there is valley where the minimum must be. Let's visualize this in a slightly different way. We'll encode the surface height as color (white is lowest).

<img src="03-linear-regression_files/figure-html/unnamed-chunk-16-1.png" width="672" />

The large values of the sum of squared residuals are dominating this image, so let's change the color scheme to see more variation in smaller values (white is lowest).

<img src="03-linear-regression_files/figure-html/unnamed-chunk-17-1.png" width="672" />

We can limit our search to $\beta_0 \in (-10,10)$ and $\beta_1 \in (2,3)$.


```r
beta0 <- seq(-10, 10, by = 0.05)
beta1 <- seq(2, 3, by = 0.05)
b <- expand.grid(beta0,beta1)
ss <- apply(b,1,f)

b[ss == min(ss),]
```

```
##      Var1 Var2
## 6142 -3.7 2.75
```

We have the minimum point. Over the grid of pairs of values, the minimum sum of squared residuals happens when the intercept is -3.7 and the slope is 2.75.

<div class="mathbox">
<p>(Optional) Alternative ways (faster than exhaustive search) to find the minimum sum of squared residuals:</p>
<ul>
<li>We could try a numerical optimization algorithm such as steepest descent.</li>
<li>We could use multivariable calculus (find partial derivatives, set equal to 0, and solve).</li>
</ul>
<p>To get started on the calculus, solve the following two equations for the two unknowns (<span class="math inline">\(\beta_0\)</span> and <span class="math inline">\(\beta_1\)</span>):</p>
<p><span class="math display">\[\frac{\partial }{\partial \beta_0}\sum_{i=1}^n (y_i - (\beta_0 + \beta_1\,x_i))^2 = 0\]</span> <span class="math display">\[\frac{\partial }{\partial \beta_1}\sum_{i=1}^n (y_i - (\beta_0 + \beta_1\,x_i))^2 = 0\]</span></p>
<p>If you are a math/stat/physics/cs major, you should try this by hand and see if you can get the solutions below.</p>
</div>

If you find the minimum using calculus (super useful class!), you'll find that we can write the Least Squares solution in an equation format as functions of summary statistics (!), the estimated slope is

$$ \hat{\beta}_1  = r\frac{s_y}{s_x}$$

and the estimated intercept is

$$ \hat{\beta}_0 = \bar{y} - \hat{\beta}_1\bar{x} $$

where $\bar{x}$ is the mean of the variable on the x-axis, $\bar{y}$ is the mean of the variable on the y-axis, $s_x$ is the standard deviation of the variable on the x-axis, $s_y$ is the standard deviation of the variable on the y-axis, and $r$ is the correlation coefficient between the two variables.

Let's do that calculation "by hand" first in R.


```r
body %>%
    summarize(sy = sd(Chest), sx = sd(Neck), r = cor(Chest,Neck), ybar = mean(Chest), xbar = mean(Neck)) %>%
    mutate(beta1 = r*sy/sx, beta0 = ybar - beta1*xbar) %>%
    select(beta0, beta1)
```

```
##       beta0    beta1
## 1 -3.188483 2.736883
```

Wow. That was quite a bit of coding. From now on, we'll take the shortcut and use the `lm()` function which stands for **l**inear **m**odel. This function gives us the least squares solution to the "best" fitting line, as defined by minimizing the sum of squared residuals.


```r
lm(Chest ~ Neck, data = body) # When you see ~, think "as a function of"
```

```
## 
## Call:
## lm(formula = Chest ~ Neck, data = body)
## 
## Coefficients:
## (Intercept)         Neck  
##      -3.188        2.737
```


## Properties of Least Squares Line

- $(\bar{x},\bar{y})$ is ALWAYS on the least squares line. 

- The residuals from the least squares line ALWAYS sum to 0.

- The mean of the residuals from the least squares line is ALWAYS 0.

- The **standard deviation of the residuals** gives us a sense of how bad our predictions (based on the line) could be. 

$$s_e = \sqrt{\frac{\sum^n_{i=1} (y_i-\hat{y}_i)^2}{n-2}}  = \sqrt{\frac{\sum^n_{i=1} (e_i-0)^2}{n-2}} $$

In R: the standard deviation of the residuals, $s_e$, is referred to as the "residual standard error". Don't confuse this with "Std. Error," whic is something else that we'll get to later in the semester.


```r
body %>%
    lm(Chest ~ Neck, data = .) %>% #If you don't want the pipe to pass to the first argument, you can specify the argument with .
    summary()
```

```
## 
## Call:
## lm(formula = Chest ~ Neck, data = .)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -15.1447  -3.3328  -0.5342   3.0738  16.5974 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  -3.1885     5.4951   -0.58    0.562    
## Neck          2.7369     0.1446   18.93   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 5.225 on 248 degrees of freedom
## Multiple R-squared:  0.5911,	Adjusted R-squared:  0.5894 
## F-statistic: 358.5 on 1 and 248 DF,  p-value: < 2.2e-16
```

This fit simple linear regression line is

$$ \widehat{\hbox{Chest}} = -3.18 + 2.73\,\hbox{Neck}$$

If you have a neck size of 38 cm, then we predict the chest size of an ideal shirt is about 100.5 cm:


```r
# The intercept and slope are rounded here first (not great):
-3.18 + 2.73*38
```

```
## [1] 100.56
```

```r
# The intercept and slope are not rounded before prediction (better!)
body %>%
    lm(Chest ~ Neck, data = .) %>% 
    predict(newdata = data.frame(Neck = 38))
```

```
##        1 
## 100.8131
```


Given your neck size, we can probably predict your chest size within 5 to 10 cm since $s_e = 5.22$ (1 to 2 SD's -- recall Section \@ref(intro-zscore)). 

<div class="reflect">
<p>If you were a shirt manufacturer, is this a good enough prediction? What is the impact on the customer? Think of if the prediction were an overestimate (loose) or an underestimate (too tight).</p>
</div>

### Real companies

Let's see how some real companies create shirts. In the plots below, the red boxes represent the advertised range (in cm) for Neck and Chest sizes for each brand.

<img src="Photos/shirtchart.png" width=".25\textwidth" />


<img src="03-linear-regression_files/figure-html/unnamed-chunk-26-1.png" width="672" />

For Calvin Klein, we see that the red boxes are below the least squares line (black line). So for a given neck size, Calvin Klein makes shirts that are a little bit too small at the chest.


<img src="03-linear-regression_files/figure-html/unnamed-chunk-27-1.png" width="672" />

For Express, we see that the red boxes are generally on the least squares line, except for the smallest size. This means that Express shirts are generally a good fit at the chest and neck for the 4 largest sizes, but the smallest shirt size is a bit too small at the chest for the neck size.


<img src="03-linear-regression_files/figure-html/unnamed-chunk-28-1.png" width="672" />

For Brooks Brothers, the red boxes are a bit below the least squares line for the 3 smallest sizes and a little above the line for the largest size. This means that the 3 smallest sizes are a bit too small in the chest for our customers (in our data set) with those neck sizes and that the largest shirt is a bit big at the chest for that neck size.

<div class="reflect">
<p>We haven’t told you how the customer data we’ve been using was collected. As you compared the brands to this sample data, what assumptions were you making about the population that the sample was drawn from?</p>
<p>What questions do you have about the sampling procedure?</p>
</div>

## Model Interpretation

Let's look at the summary output of the `lm()` function in R again. We'll highlight some of the most important pieces of this output and discuss how you interpret them.


```r
tshirt_mod <- lm(Chest ~ Neck, data = body)
summary(tshirt_mod)
```

```
## 
## Call:
## lm(formula = Chest ~ Neck, data = body)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -15.1447  -3.3328  -0.5342   3.0738  16.5974 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  -3.1885     5.4951   -0.58    0.562    
## Neck          2.7369     0.1446   18.93   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 5.225 on 248 degrees of freedom
## Multiple R-squared:  0.5911,	Adjusted R-squared:  0.5894 
## F-statistic: 358.5 on 1 and 248 DF,  p-value: < 2.2e-16
```

### Intercept ($\hat{\beta}_0$)

- **Where to find:** In the table called "Coefficients", look for the number under "Estimate" for "(Intercept)". It is -3.1885 for this model. 
- **Definition:** The intercept gives the average value of $y$ when $x$ is zero (**think about context**)
- **Interpretation:** If Neck size = 0, then the person doesn't exist. In this context, the intercept doesn't make much sense to interpret. 
- **Discussion:** If the intercept doesn't make sense in the context, we might refit the model with the $x$ variable once it is **centered**. That is, the mean of $x$ for all individuals in the sample is computed, and this mean is subtracted from each person's $x$ value. In this case, the intercept is interpreted as the average value of $y$ when $x$ is at its (sample) mean value. See the example below -- we get an intercept of 100.66, which is the average Chest size for customers with average Neck size. We could also choose to center Neck size at value other than the mean. For example, we could center it at 30 cm by subtracting 30 from the Neck size of all cases.


```r
body <- body %>% mutate(CNeck = Neck - mean(Neck))
tshirt_mod2 <- lm(Chest ~ CNeck, data = body)
summary(tshirt_mod2)
```

```
## 
## Call:
## lm(formula = Chest ~ CNeck, data = body)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -15.1447  -3.3328  -0.5342   3.0738  16.5974 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 100.6620     0.3304  304.63   <2e-16 ***
## CNeck         2.7369     0.1446   18.93   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 5.225 on 248 degrees of freedom
## Multiple R-squared:  0.5911,	Adjusted R-squared:  0.5894 
## F-statistic: 358.5 on 1 and 248 DF,  p-value: < 2.2e-16
```


### Slope ($\hat{\beta}_1$)

- **Where to find:** In the table called "Coefficients", look for the number under "Estimate" for the name of the variable, "Neck" or "CNeck". It is 2.7369 for this model. (Note: the slopes for both `tshirt_mod` and `tshirt_mod2` are the same. Why?) 
- **Definition:** The slope gives the change in average $y$ per 1 unit increase in $x$ (**not for individuals and not a causal statement**)
- **Interpretation:** If we compare two groups of individuals with a 1 cm difference in the neck size (e.g., 38 cm and 39 cm OR 40 cm and 41 cm), then we'd expect the average chest sizes to be different by about 2.7 cm.
- **Another Interpretation:** We'd expect the average chest size to increase by about 2.7 cm for each centimeter increase in neck size.
- **Discussion:** Note that both interpretations are not written about an individual because the least squares line (our fit model) is a description of the **general trend** in the population. The slope describes the change in the average, not the change for one person.

### Least Squares Regression Line ($\hat{y} = \hat{\beta}_0 + \hat{\beta}_1x$)

- **Where to find:** In the table called "Coefficients", all of the values that you need to write down the formula of the line are in the "Estimate" column.
- **Definition:** The line gives the estimated average of $y$ for each value of $x$ (**within the observed range of x**)
- **Interpretation:** The fit regression line of (Predicted Chest = -3.18 + 2.73*Neck) gives the estimated average Chest size for a given Neck size, based on our sample of data.
- **Discussion:** The "within the observed range of $x$" phrase is very important. We can't predict values of $y$ for values of $x$ that are very different from what we have in our data. Trying to do so is a big no-no called **extrapolation**. We'll discuss this more in a bit.

### Correlation or Association vs. Causation

<center>
![[xkcd: Correlation](https://imgs.xkcd.com/comics/correlation.png)](Photos/correlation.png)
Source: [xkcd](https://xkcd.com/552/)
</center>

What is causation? What is a causal effect? Are there criteria for defining a cause?

These are deep questions that have been debated by scientists of all domains for a long time.We are at a point now where there is some consensus on the definition of a causal effect. The causal effect of a variable $x$ on another variable $y$ is the amount that we expect $y$ to change if we **intervene on/manipulate** $x$ by changing it by one unit.

When can we interpret an estimated slope $\beta_1$ from a simple linear regression as a causal effect of $x$ on $y$? Well, in the simple linear regression case, almost never. To interpret the slope causally, there would have to be **no confounding variables** (no variables that cause **both** $x$ **and** $y$). If we performed an experiment, this might be possible, but in most situations, this is not feasible.

Then what are we to do? 

1. Think about the possible causal pathways and try and create a DAG (from the first chapter).
2. Try to **adjust/control for** possible confounders using multiple linear regression (coming up soon). 
3. After fitting a model, we should step back and consider other criteria such as [Hill's Criteria](https://en.wikipedia.org/wiki/Bradford_Hill_criteria) before concluding there is a cause and effect relationship. Think about whether there is a large effect, whether it can be reproduced in another sample, whether the the effect happens before the cause in time, etc. 

**Multiple linear regression** offers us a way to estimate causal effects of variables *if we use it carefully*. It will be tempting to say: control for everything we can! But we will see that this is not the correct thing to do, as there are very real dangers of over-controlling for variables (known as throwing everything in the model). For now, let it suffice to say that multiple linear regression is as useful as the corn kerneler below. Immensely useful in the right circumstances - but only those circumstances.

<center>
![Source: [Walmart](https://www.walmart.com/ip/Corn-Kerneler-w-Stainless-Steel-Blades/139735616)](Photos/corn.jpeg)
</center>

## Model Evaluation

In this section, we consider model evaluation. We seek to develop tools that allow us to answer: is this a "good" model?

### Prediction


Let's consider another data example. Can we predict your college GPA based on your high school GPA? (Disclaimer: this is not Macalester data)



```r
sat <- read.csv("Data/sat.csv")
```


```r
sat %>%
    ggplot(aes(x = high_GPA, y = univ_GPA)) +
    geom_point(color = 'steelblue') +
    geom_smooth(method = 'lm', se = FALSE) +
    xlab('High School GPA') +
    ylab('College GPA') + 
    theme_minimal()
```

<img src="03-linear-regression_files/figure-html/unnamed-chunk-33-1.png" width="672" />

First things first. Describe the scatterplot. 

- **Direction**: Positive relationship (higher high school GPA is associated with higher college GPA)
- **Form**: generally linear
- **Strength**: There is a weak relationship when high school GPA < 3.0 ($r = 0.32$) and a fairly strong relationship when high school GPA > 3.0 ($r = 0.68$).
- **Unusual**: As seen with the strength, there is greater variability in college GPA among individuals with lower high school GPA. That variability decreases with increased high school GPA. We call this pattern of unequal variation as **"thickening"** or **heteroscedasticity** (this terms is used quite a bit in Econometrics). 

The code below computes the correlation coefficients separately for students with high school GPAs above 3 and for students with high school GPAs less than or equal to 3. We see that the correlation is higher for the high GPA group.


```r
sat %>%
    mutate(HighHSGPA = high_GPA > 3) %>%
    group_by(HighHSGPA) %>%
    summarize(Cor = cor(high_GPA,univ_GPA))
```

```
## # A tibble: 2 x 2
##   HighHSGPA   Cor
##   <lgl>     <dbl>
## 1 FALSE     0.316
## 2 TRUE      0.687
```

Let's build a model to predict college GPA based on high school GPA based on this sample data. Since we noted that there was a linear relationship, let's find the least squares regression line.


```r
sat %>%
  lm(univ_GPA ~ high_GPA, data = .) %>%
  summary()
```

```
## 
## Call:
## lm(formula = univ_GPA ~ high_GPA, data = .)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -0.69040 -0.11922  0.03274  0.17397  0.91278 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  1.09682    0.16663   6.583 1.98e-09 ***
## high_GPA     0.67483    0.05342  12.632  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.2814 on 103 degrees of freedom
## Multiple R-squared:  0.6077,	Adjusted R-squared:  0.6039 
## F-statistic: 159.6 on 1 and 103 DF,  p-value: < 2.2e-16
```


The best fitting line is

$$ \hbox{Predicted College GPA} = 1.09 + 0.675 \times \hbox{High School GPA} $$


Let's plug in a few values.

- If High School GPA = 2:    
$$ \hbox{Predicted College GPA} = 1.09 + 0.675 \times 2 = 2.44 $$


```r
## Calculation by hand; rounded before prediction
1.09 + 0.675*2
```

```
## [1] 2.44
```

```r
## Calcuation using R's predict() function
sat %>%
  lm(univ_GPA ~ high_GPA, data = .) %>%
  predict(newdata = data.frame(high_GPA = 2))
```

```
##        1 
## 2.446483
```

- If High School GPA = 3.5:    
$$ \hbox{Predicted College GPA} = 1.09 + 0.675 \times 3.5 = 3.45 $$


```r
1.09 + 0.675*3.5 #rounded before prediction
```

```
## [1] 3.4525
```

```r
sat %>%
  lm(univ_GPA ~ high_GPA, data = .) %>%
  predict(newdata = data.frame(high_GPA = 3.5)) #rounded after prediction
```

```
##        1 
## 3.458728
```

- If High School GPA = 4.5:    
$$ \hbox{Predicted College GPA} = 1.09 + 0.675 \times 4.5 = 4.13 $$


```r
1.09 + 0.675*4.5 #rounded before prediction
```

```
## [1] 4.1275
```

```r
sat %>%
  lm(univ_GPA ~ high_GPA, data = .) %>%
  predict(newdata = data.frame(high_GPA = 4.5)) #rounded after prediction 
```

```
##        1 
## 4.133558
```

<div class="reflect">
<p>Does it make sense to use this model for high school GPA’s &gt; 4? Some high schools have a max GPA of 5.0 due to the weighting of advanced courses.</p>
</div>

- What is the maximum high school GPA in this data set? 
- What if your college doesn't allow for GPA's above 4.0? 



```r
sat %>%
    summarize(max(high_GPA)) # Max high school GPA in this data set
```

```
##   max(high_GPA)
## 1             4
```

Making predictions beyond the observed range of values is called **extrapolation** and is generally a risky thing to do. If you make prediction for values of $x$ beyond the minimum or maximum of the observed values, then you are assuming that the relationship you observe can be extended into the new prediction range. This is the main issue of **forecasting**, making predictions in the future. You have to assume that the trend that you observe now will continue in the future and that the current state of affairs will stay the same. For an infamous case of extrapolation, check out [this article](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3173856/) that appeared in the journal Nature.

### Prediction Errors

Recall that a residual, $e_i$, for the $i$th data point is the difference between the actual and predicted values: $e_i = y_i - \hat{y}_i$.

If the residuals were approximately unimodal and symmetric, we expect about 95% of the residuals to be within 2 standard deviations of 0 (the mean residual). (Recall Section \@ref(intro-zscore).)


```r
sat %>%
  lm(univ_GPA ~ high_GPA, data = .) %>%
  residuals() %>%
  hist(main = "Distribution of residuals in the GPA model")
```

<img src="03-linear-regression_files/figure-html/unnamed-chunk-41-1.png" width="672" />

Below we calculate $SSE$ (the sum of squared errors/residuals), and the standard deviation of the residuals ($s_e$).


```r
lm.gpa <- sat %>%
  lm(univ_GPA ~ high_GPA, data = .)
SSE <- sum(residuals(lm.gpa)^2)
n <- length(residuals(lm.gpa)) # Sample size
s <- sqrt(SSE/(n-2)) #sd of residuals
s
```

```
## [1] 0.2814443
```

```r
2*s
```

```
## [1] 0.5628886
```

Using this model (that is, using your high school GPA), we can predict your college GPA within about $0.56$ GPA points. Is this useful? Is predicting within a margin of $0.56$ GPA points good enough? Let's compare this margin of error with the margin of error about the mean (the standard deviation):


```r
sd(sat$univ_GPA)
```

```
## [1] 0.4471936
```

```r
2*sd(sat$univ_GPA)
```

```
## [1] 0.8943873
```

*Without* knowing your high school GPA, we could have just guessed your college GPA as the overall mean college GPA in the sample, and this guess would probably be within $\pm 0.89$ of your actual college GPA. This is a higher margin of error than the approximate $0.56$ if we did use your high school GPA (in the simple linear regression model). We are able to predict your college GPA with a smaller margin of error than if we just guessed your college GPA with the mean. Our model has *explained* some (but not all) of the variation in college GPA.

The standard deviation of college GPA is based on the sum of squared total variation, SSTO (variation around the mean),

$$ SSTO = \sum{(y_i -\bar{y})^2} $$

$SSTO$ is the numerator of the standard deviation of $y$ (without knowing anything about $x$).


```r
(SSTO = sum((sat$univ_GPA - mean(sat$univ_GPA))^2))
```

```
## [1] 20.79814
```

We define $SSTO$ here because it will help to compare $SSTO$ to $SSE$ (sum of squared residuals from the model) to obtain a measure of how well our models are **fitting** the data - how well they are predicting the outcome.

### $R^2$

Let's study how *models reduce unexplained variation*.

- Before a model is fit, the unexplained variation is given by $SSTO$. It is the overall variability in the outcome. Think back to interpreting standard deviation and variance as measures of spread. We used these to describe broadly how much the outcome varys
- Unexplained variation in the outcome that remains after modeling is given by $SSE$, the sum of squared residuals.

So to study how models reduce unexplained variation, we compare the magnitude of the residuals from a linear regression model (which uses the predictor $x$) with the original deviations from the mean (which do not use the predictor $x$).

<img src="03-linear-regression_files/figure-html/unnamed-chunk-45-1.png" width="672" />


We started with the sum of the deviations from the mean $SSTO = \sum{(y_i - \bar{y})^2}$ before we had info about high school GPA ($x$).

- Now, with our knowledge of $x$, we have $SSE = \sum{(y_i - \hat{y_i})^2}$

- $SSE$ should be smaller than $SSTO$ (!)

Two extreme cases: 

- If the error $SSE$ goes to zero, we'd have a "perfect fit". 
- If $SSE = SSTO$, $x$ has told us nothing about $y$.

- So we define a measure called **R-squared**, which is the *fraction* or *proportion* of the total variation in $y$ "accounted for" or "explained" by the model in $x$. 



<div class="mathbox">
<p><span class="math display">\[ R^2 = 1 - \frac{SSE}{SSTO} = 1 - \frac{ \sum{(y_i - \hat{y_i})^2}}{ \sum{(y_i - \bar{y})^2}}\]</span></p>
</div>

In R, `lm()` will calculate R-Squared ($R^2$) for us, but we can also see that it equals the value from the formula above. 



```r
1 - SSE/SSTO
```

```
## [1] 0.6077187
```

```r
glance(lm.gpa) #r.squared = R^2, sigma = s_e (ignore the rest)
```

```
## # A tibble: 1 x 12
##   r.squared adj.r.squared sigma statistic  p.value    df logLik   AIC   BIC
##       <dbl>         <dbl> <dbl>     <dbl>    <dbl> <dbl>  <dbl> <dbl> <dbl>
## 1     0.608         0.604 0.281      160. 1.18e-22     1  -14.9  35.7  43.7
## # … with 3 more variables: deviance <dbl>, df.residual <int>, nobs <int>
```

- Is there a "good" value of $R^2$? Same answer as correlation -- no.

- $R^2$ doesn't tell you the direction or the form of the relationship.

- Note: $R^2 = r^2$ for simple linear models with one x variable (where $r$ is the correlation coefficient).



## Multiple Linear Regression

We can generalize the idea of a simple linear regression model by including many predictor variables ($x$'s). A **multiple linear regression model** can be written as:

$$ E[Y] = \beta_0 + \beta_1\,x_{1} + \cdots + \beta_k\,x_{k} = \hat{y} $$

- Each coefficient $\beta_j$ can be interpreted as the increase in the predicted/average $y$ associated with a 1 unit increase in $x_j$, **keeping all other variables constant**. (\*There are some exceptions - we'll get there.)

- These explanatory variables can be:
    - Quantitative variables (or transformations of them)
    - [Indicator variables][Indicator Variables] for categorical variables (only need $L-1$ indicators for a variable with $L$ categories)
    - [Interaction terms][Interaction Variables] (product of two variables, which allows for *effect modification*)
    
Let's talk about a new data example: home prices. We want to build a model to predict the price of a home based on its many characteristics. Here we have a dataset of homes recently sold in New England with many variables such as the age of the home, the land value, whether or not it has central air conditioning, the number of fireplaces, the sale price, and more...


```r
homes <- read.delim('Data/Saratoga.txt')
head(homes)
```

```
##    Price Lot.Size Waterfront Age Land.Value New.Construct Central.Air Fuel.Type
## 1 132500     0.09          0  42      50000             0           0         3
## 2 181115     0.92          0   0      22300             0           0         2
## 3 109000     0.19          0 133       7300             0           0         2
## 4 155000     0.41          0  13      18700             0           0         2
## 5  86060     0.11          0   0      15000             1           1         2
## 6 120000     0.68          0  31      14000             0           0         2
##   Heat.Type Sewer.Type Living.Area Pct.College Bedrooms Fireplaces Bathrooms
## 1         4          2         906          35        2          1       1.0
## 2         3          2        1953          51        3          0       2.5
## 3         3          3        1944          51        4          1       1.0
## 4         2          2        1944          51        3          1       1.5
## 5         2          3         840          51        2          0       1.0
## 6         2          2        1152          22        4          1       1.0
##   Rooms
## 1     5
## 2     6
## 3     8
## 4     5
## 5     3
## 6     8
```

\*The exception to the interpretation comment above is if some of our $x$ variables are strongly correlated (called **multicollinearity**). In this case, we cannot hold all other variables constant (at least not well) because if we increase the value of one variable, then variables highly correlated with it will also likely change in value.


### Indicator Variables

In New England, fireplaces are often used as a way to provide supplementary heat to the house. Let's study the impact of a fireplace has on the sale price of a home. In particular, we only care if the home has 1 or more fireplaces or no fireplaces. So we make a new variable that is `TRUE` if there is at least one fireplace in a home and `FALSE` otherwise.


```r
homes <- homes %>%
    mutate(AnyFireplace = Fireplaces > 0)
```

In order to include this information in our linear regression model, we need to turn that categorical variable (`AnyFireplace` with values of `TRUE` or `FALSE`) into an **indicator variable**, which has a numeric value of 0 or 1:

$$ \hbox{AnyFireplaceTRUE} = \begin{cases}1 \quad \text{ if a home has at least one fireplace}\\ 0\quad \text{ if a home does not have a fireplace} \end{cases}$$

In fact, R creates this indicator for you when you include a categorical variable as an $x$ variable the `lm()` function.


```r
lm.home <- lm(Price ~ AnyFireplace, data = homes)
summary(lm.home)
```

```
## 
## Call:
## lm(formula = Price ~ AnyFireplace, data = homes)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -234914  -59653  -18784   42145  585347 
## 
## Coefficients:
##                  Estimate Std. Error t value Pr(>|t|)    
## (Intercept)        174653       3419   51.08   <2e-16 ***
## AnyFireplaceTRUE    65261       4522   14.43   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 93020 on 1726 degrees of freedom
## Multiple R-squared:  0.1077,	Adjusted R-squared:  0.1072 
## F-statistic: 208.3 on 1 and 1726 DF,  p-value: < 2.2e-16
```

Our "best fitting line" is

$$ E[\hbox{Price}] = 174653 + 65261\,\hbox{AnyFireplaceTRUE} $$

**What does this mean?**

Let's think about two types of homes: a home with one or more fireplaces and a home without a fireplace. Let's write out the equations for those two types of homes. 

- Home with fireplace (indicator variable = 1):    
$$ E[\hbox{Price}] = 174653 + 65261 \times 1 = \$ 239,914 $$


```r
174653 + 65261*1
```

```
## [1] 239914
```

- Home without fireplace (indicator variable = 0):    
$$ E[\hbox{Price}] = 174653 + 65261 \times 0 = \$ 174,653 $$

The difference between these predicted prices is \$65,261, the value of the "slope" for the indicator variable (also referred to as the `AnyFireplaceTRUE` coefficient).

<div class="reflect">
<p>So is this how much a fireplace is worth? If I installed a fireplace in my house, should the value of my house go up $65,260?</p>
<p><strong>No</strong>, because we should not make causal statements based on observational data without thinking deeply about the context. What could be confounding this relationship? What third variable may be related to both the price and whether or not a house has a fireplace?</p>
</div>

Let's consider the size of the house. Is price related to the area of living space (square footage)?


```r
homes %>%
    ggplot(aes(x = Living.Area, y = Price)) + 
    geom_point(color = 'steelblue') +
    theme_minimal()
```

<img src="03-linear-regression_files/figure-html/unnamed-chunk-53-1.png" width="672" />

Is the presence of a fireplace related to area of living space?


```r
homes %>%
    ggplot(aes(x = AnyFireplace, y = Living.Area)) + 
    geom_boxplot() +
    theme_minimal()
```

<img src="03-linear-regression_files/figure-html/unnamed-chunk-54-1.png" width="672" />

We see that the amount of living area differs between homes with fireplaces and homes without fireplaces. Thus, `Living.Area` could confound the relationship between `AnyFireplace` and `Price` because it is related to both variables. That is, it is possible that Living Area is a cause of having a fireplace and Living Area also clearly is a cause of Price.

Let's put `Living.Area` in the model along with `AnyFireplace` to account for it (to control/adjust for it).


```r
lm.home2 <- lm(Price ~ AnyFireplace + Living.Area, data = homes)
summary(lm.home2)
```

```
## 
## Call:
## lm(formula = Price ~ AnyFireplace + Living.Area, data = homes)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -271421  -39935   -7887   28215  554651 
## 
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      13599.164   4991.695   2.724  0.00651 ** 
## AnyFireplaceTRUE  5567.377   3716.947   1.498  0.13436    
## Living.Area        111.218      2.968  37.476  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 69080 on 1725 degrees of freedom
## Multiple R-squared:  0.5081,	Adjusted R-squared:  0.5076 
## F-statistic:   891 on 2 and 1725 DF,  p-value: < 2.2e-16
```

Our "best fitting line" is

$$ E[\hbox{Price}] = 13599.16 + 5567.37\,\hbox{AnyFireplaceTRUE} + 111.21\,\hbox{Living.Area} $$

Note that the slope for the indicator variable is very different. This suggests that `Living.Area` was confounding the relationship between `Price` and `AnyFireplace`.

**What does this mean?** 

Let's think about two types of homes: a home with one or more fireplaces and a home without a fireplace.

- Home with fireplace (indicator = 1):    

\begin{align*}
E[\hbox{Price}] &= 13599.16 + 5567.37 \times 1 + 111.21 \times \hbox{Living.Area} \\
&= 19166.53 + 111.21\,\hbox{Living.Area}
\end{align*}


*Among homes with a fireplace, we have one linear relationship between living area and price.*

- Home without fireplace (indicator = 0):    

\begin{align*}
E[\hbox{Price}] &= 13599.16 + 5567.37 \times 0 + 111.21 \times \hbox{Living.Area} \\
&= 13599.16 + 111.21 \times \hbox{Living.Area}
\end{align*}


*Among homes without a fireplace, we have a different linear relationship between living area and price.*


- For either type of home, \$111.21 is the increase in the predicted or average Price associated with a 1 square footage increase in Living.Area, **holding the number of fireplaces constant**.



Now let's compare homes that are the same size. 

*If we keep Living.Area constant by considering two equally sized homes, then we'd expect the home with the fireplace to be worth \$5567.37 more than a home without a fireplace.*

We see this by taking the difference between the two equations:

$$ \hbox{Predicted Price (with Fireplace)} - \hbox{Predicted Price (without Fireplace)} $$

$$
\begin{align*}
\,&= (\$19,166.53 + \$111.21 \times \hbox{Living.Area}) - ( \$13599.16 + \$111.21 \times \hbox{Living.Area})
\,&= \$19,166.53 - \$13,599.16 = \$5567.37
\end{align*}
$$

The difference between the intercepts is 5567.37.

- This was the estimated coefficient or slope for AnyFireplaceTRUE. 

- So the \$5567.37 is the increase in the predicted or average Price associated with a 1 unit increase in AnyFireplace (going from FALSE to TRUE), **holding all other variables (Living.Area) constant**.


Let's look back at the relationship between Living.Area and Price and color the scatterplot by AnyFireplace. So we are now looking at three variables at a time. The above model with AnyFireplace and Living.Area results in two lines for Living.Area v. Price, with different intercepts but the same slope (parallel lines).


```r
homes %>%
    ggplot(aes(x = Living.Area, y = Price, color = AnyFireplace)) + 
    geom_point() +
    theme_minimal()
```

<img src="03-linear-regression_files/figure-html/unnamed-chunk-56-1.png" width="672" />

Let's try and fit two separate lines to these two groups of homes, home with any fireplaces and home with no fireplaces. Do these lines have the same intercepts? Same slopes?


```r
homes %>%
    ggplot(aes(x = Living.Area, y = Price, color = AnyFireplace)) + 
    geom_point() +
    geom_smooth(method = 'lm', se = FALSE) +
    theme_minimal()
```

<img src="03-linear-regression_files/figure-html/unnamed-chunk-57-1.png" width="672" />

In this case, it look as though having a fireplace in your house slightly changes the relationship between Living.Area and Price. In fact, having a fireplace in your house, the increase in your price for every 1 square foot is greater than that for homes without fireplaces (slopes are different).


### Interaction Variables

We can allow for different slopes within one regression model (!), rather than fitting two separate models. 

<div class="reflect">
<p>When should we fit only one model; when should we fit separate models?</p>
<p>If we fit separate models, we are <strong>stratifying</strong> and then modeling. But what if some of the strata are small?</p>
<p>Fitting one model allows us to “borrow information across groups.”</p>
<p>There is no one right answer. Researchers struggle with this decision <a href="https://www.ncbi.nlm.nih.gov/pubmed/22125224">to stratify or not to stratify</a>.</p>
</div>

- If we add a variable in the model (without an interaction), it only changes the intercept.

- We can achieve different slopes by allowing a variable $x_1$ to affect the slope for another variable $x_2$. That is, $x_1$ impacts the effect of $x_2$ on the outcome $y$. (Fireplace presence impacts the effect of living area on house price.)
$$\beta_2 = a + bx_1$$
This is called **effect modification** (when one variable can modify the effect of another variable on the outcome).

- A model with effect modification looks like this:
$$\hat{y} = \beta_0 + \beta_1x_{1} + \beta_2x_{2}= \beta_0 + \beta_1x_{1} + (a+bx_1)x_{2}= \beta_0 + \beta_1x_{1} +ax_2+bx_1x_{2}$$
The model above has an **interaction term,** which is the product of two variables. Here we have $x_1*x_2$.

Let's build a model with effect modification for our housing data. Let's include an interaction term between AnyFireplace and Living.Area to allow for different slopes.


```r
lm.home3 <- lm(Price ~ AnyFireplace*Living.Area, data = homes)
summary(lm.home3)
```

```
## 
## Call:
## lm(formula = Price ~ AnyFireplace * Living.Area, data = homes)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -241710  -39588   -7821   28480  542055 
## 
## Coefficients:
##                                Estimate Std. Error t value Pr(>|t|)    
## (Intercept)                   40901.294   8234.665   4.967 7.47e-07 ***
## AnyFireplaceTRUE             -37610.413  11024.853  -3.411 0.000661 ***
## Living.Area                      92.364      5.412  17.066  < 2e-16 ***
## AnyFireplaceTRUE:Living.Area     26.852      6.459   4.157 3.38e-05 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 68760 on 1724 degrees of freedom
## Multiple R-squared:  0.513,	Adjusted R-squared:  0.5122 
## F-statistic: 605.4 on 3 and 1724 DF,  p-value: < 2.2e-16
```

**What does this mean?** 

Let's think about two types of homes: a home with one or more fireplaces and a home without a fireplace.

- Home with fireplace (indicator = 1):    

$$
\begin{align*}
\hbox{Predicted Price} &= 40901.29 + -37610.41 \times 1 + 92.36391 \times \hbox{Living.Area} + 26.85 \times \hbox{Living.Area} \times 1 \\
& = \$3,290.88 + \$119.21 \times \hbox{Living.Area}
\end{align*}
$$


```r
40901.29 + -37610.41*1
```

```
## [1] 3290.88
```

```r
92.36391 + 26.85*1
```

```
## [1] 119.2139
```

- Home without fireplace (indicator = 0):    

$$
\begin{align*}
\hbox{Predicted Price} &= 40901.29 + -37610.41 \times 0 + 92.36391 \times \hbox{Living.Area} + 26.85 \times \hbox{Living.Area} \times 0 \\
&= \$40,901.29 + \$92.36 \times \hbox{Living.Area}
\end{align*}
$$

Note that there are different intercepts **and** different slopes for these two groups. Thus, including an interaction term between a categorical and a quantitative predictor variable allows us to describe **effect modification**. How does the effect of one variable on the response variable differ according to another variable? How is the effect modified by another variable?

What this means in the context of this model of price as a function of living area and fireplaces: different slopes means that the average rate of price increase for every additional square foot is **different between homes with and without fireplaces**.

### Causation

We alluded earlier that multiple linear regression could provide estimates of causal effects in the right circumstances. What are those circumstances? When we include **all** confounding variables. 

Remember, a confounder is a **common cause** of both the causal variable of interest and the outcome (e.g. living area could be a confounder of fireplace presence and house price).

We also alluded earlier that we should not just throw every variable we have into a multiple regression model. Why? Imagine a scenario for understanding how smoking affects lung cancer development. It is very important to consider whether a variable is a **mediator** of the relationship between the cause and the outcome. A mediator  is a variable in a chain or along the path (Section \@ref(dag)) between the cause and the outcome. In the smoking and lung cancer example, one mediator could be tar in the lungs. Suppose that smoking only affects lung cancer risk by creating tar on the lungs. If we adjust for tar (by holding it constant), then we also effectively hold smoking constant too! If smoking is held constant, then we cannot estimate its effect on cancer risk because it is not varying!

Wait - we could never possibly know of or measure all confounding variables, could we!? This is true, but that doesn't mean that our endeavor to understand causation is fruitless. As long as we can describe the relationship between known confounders as precisely as possible with writing down a DAG (causal model), we have a starting ground for moving forward. 

We collect data, analyze how well our model predicts that data, and collect more data based on that, perhaps measuring more potential confounders as our scientific knowledge grows. We can also conduct sensitivity analyses by asking: how strongly must a confounder affect the variable of causal interest and the outcome to completely negate or reverse the association we see? Such endeavors and more are the subject of the field of *causal inference*.

*Reminder: If you want a "gentle" but mathematical introduction to Causal Inference, we suggest taking the class at Macalester and/or reading "Causal Inference in Statistics: A Primer" by Judea Pearl, Madelyn Glymour, Nicholas P. Jewell). Fun Fact: Nicholas Jewell was Prof. Heggeseth's PhD advisor!*

### Conditions for Multiple Linear Regression

In order for a multiple linear regression model to make sense, 

1. Relationships between each pair of quantitative $x$'s and $y$ are **straight enough** (check scatterplots and residual plot)
2. There is about **equal spread** of residuals across fitted values (check residual plot)
3. There are no extreme outliers (points far away in $x$'s can have **leverage** to change the line)


### Is the Difference Real?

We could ask: is there *really* a difference in the slopes for Living Area and Price between homes with and without a fireplace?


```r
lm.home4 <- lm(Price ~ Living.Area*AnyFireplace, data = homes) 
summary(lm.home4)
```

```
## 
## Call:
## lm(formula = Price ~ Living.Area * AnyFireplace, data = homes)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -241710  -39588   -7821   28480  542055 
## 
## Coefficients:
##                                Estimate Std. Error t value Pr(>|t|)    
## (Intercept)                   40901.294   8234.665   4.967 7.47e-07 ***
## Living.Area                      92.364      5.412  17.066  < 2e-16 ***
## AnyFireplaceTRUE             -37610.413  11024.853  -3.411 0.000661 ***
## Living.Area:AnyFireplaceTRUE     26.852      6.459   4.157 3.38e-05 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 68760 on 1724 degrees of freedom
## Multiple R-squared:  0.513,	Adjusted R-squared:  0.5122 
## F-statistic: 605.4 on 3 and 1724 DF,  p-value: < 2.2e-16
```

If we ask ourselves this question, we are assuming a few things:

1. We would like to make a general statement about a **target population of interest**.

2. We don't have data for everyone in our population (we don't have a **census**).

3. Depending on who **randomly** ends up in our **sample**, the relationship may change a bit.

4. We want to know how much the relationship may change based on **sampling variation.**  

- Let's treat our sample (of size $n$) as a 'fake' population (we don't have the full population but if the sample is representative, then it will be a good proxy).
    - Randomly resample from our sample (with replacement) a new sample of size $n$
- Estimate the least squares regression line.
- Repeat.


```r
set.seed(333) ## Setting the seed ensures that our results are reproducible
## Repeat the sampling and regression modeling 1000 times
boot <- do(1000)*lm(Price ~ Living.Area*AnyFireplace, data = resample(homes))

## Plot the distribution of the 1000 slope differences
boot %>%
    ggplot(aes(x = Living.Area.AnyFireplaceTRUE)) +
    geom_histogram() +
    xlab('Bootstrap Difference in Slopes') +
    theme_minimal()
```

<img src="03-linear-regression_files/figure-html/unnamed-chunk-62-1.png" width="672" />

We see that if we were to have a slightly different sample (drawn from our "fake" population), then the difference in the slope could be as long as 0 and as large as 50.

This process of resampling from the sample is called **Bootstrapping** and it is used to:

1. Measure the variability in the estimate (here we are interested in the difference in slopes) between random samples and
2. Provide an interval of plausible values for the estimate (the difference in slopes here).

Let's first look at the variability of the difference in slopes across the bootstrap samples. The standard deviation of the slopes will be similar to the `Std. Error` from the linear model output.


```r
boot %>%
  summarize(sd(Living.Area.AnyFireplaceTRUE)) #this is going to be of similar magnitude (not exactly the same) to the Std. Error in output
```

```
##   sd(Living.Area.AnyFireplaceTRUE)
## 1                         9.332937
```

```r
summary(lm.home4)
```

```
## 
## Call:
## lm(formula = Price ~ Living.Area * AnyFireplace, data = homes)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -241710  -39588   -7821   28480  542055 
## 
## Coefficients:
##                                Estimate Std. Error t value Pr(>|t|)    
## (Intercept)                   40901.294   8234.665   4.967 7.47e-07 ***
## Living.Area                      92.364      5.412  17.066  < 2e-16 ***
## AnyFireplaceTRUE             -37610.413  11024.853  -3.411 0.000661 ***
## Living.Area:AnyFireplaceTRUE     26.852      6.459   4.157 3.38e-05 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 68760 on 1724 degrees of freedom
## Multiple R-squared:  0.513,	Adjusted R-squared:  0.5122 
## F-statistic: 605.4 on 3 and 1724 DF,  p-value: < 2.2e-16
```

This standard deviation is somewhat close to the $6.459$ in the Std. Error column of the `summary(lm.home4)` output above.

To get an interval of plausible values for the difference in the slopes, we look at the histogram and take the middle 95%. The lower end will be the 2.5th percentile and the upper end will be the 97.5th percentile.


```r
boot %>%
  summarize(lower = quantile(Living.Area.AnyFireplaceTRUE, 0.025), upper = quantile(Living.Area.AnyFireplaceTRUE, 0.975))
```

```
##      lower    upper
## 1 7.686552 45.43513
```

<div class="reflect">
<p>Based on this evidence, do you think it is possible that the slopes are the same for the two types of homes (with and without fireplaces)? How would you justify your answer?</p>
</div>

### Dealing with Non-Linear Relationships

If we notice a curved relationship between two quantitative variables, it doesn't make sense to use a straight line to approximate the relationship. 

What can we do?

#### Transform Variables

One solution to deal with non-linear relationships is to **transform** the predictor ($x$) variables or transform the outcome variable ($y$).

**Guideline #1:** If there is unequal spread around the curved relationship, focus first on transforming $y$. If the spread is roughly the same around the curved relationship, focus on transforming $x$. 

When we say transform a variable, we are referring to taking the values of a variable and plugging them into a mathematical function such as $\sqrt{x}$, $\log(x)$ (which represents natural log, not log base 10), $x^2$, $1/x$, etc. 

**Guideline #2:** To choose the mathematical function, we focus on power functions and organize them in a **Ladder of Powers** of $y$ (or $x$):



\begin{align}
\vdots\\
y^3\\
y^2\\
\mathbf{y = y^1}\\
\sqrt{y}\\
y^{1/3}\\
y^{0} ~~~  (we~use~\log(y)~here; natural log)\\
y^{-1/3}\\
1/\sqrt{y}\\
1/y\\
1/y^2\\
\vdots
\end{align}


To choose a transformation, we start at $y$ (power  = 1) and think about going up or down the ladder. 

But which way?

Our friend [J.W. Tukey](https://en.wikipedia.org/wiki/John_Tukey) (the same guy who invented the boxplot) came up with an approach to help us decide. You must ask yourself: Which part of the circle does the scatterplot most resemble (in terms of concavity and direction)? Which quadrant?

**Guideline #3:** The sign of $x$ and $y$ in the quadrant tells you the direction to move on the ladder (positive = up, negative = down).

<img src="Photos/tukey.png" width="400" height="500" />

Practice: Which quadrant does this relationship below resemble? 


```r
require(gapminder)

gapminder %>% 
  filter(year > 2005) %>%
  ggplot(aes(y = lifeExp, x =  gdpPercap)) + 
  geom_point()
```

<img src="03-linear-regression_files/figure-html/unnamed-chunk-66-1.png" width="672" />

Based on this plot, we see that the spread is roughly equal around the curved relationship (-> focus on transforming $x$) and that it is concave down and positive (quadrant 2: top left). This suggests that we focus on going down the ladder with $x$. 

Try these transformations until you find a relationship that is roughly straight. If you go too far, the relationship will become more curved again.

Let's try going down the ladder.


```r
gapminder %>% 
  filter(year > 2005) %>%
  mutate(TgdpPercap = sqrt(gdpPercap)) %>% #power = 1/2
  ggplot(aes(y = lifeExp, x =  TgdpPercap)) + 
  geom_point()
```

<img src="03-linear-regression_files/figure-html/unnamed-chunk-67-1.png" width="672" />

Not quite straight. Let's keep going.



```r
gapminder %>% 
  filter(year > 2005) %>%
  mutate(TgdpPercap = gdpPercap^(1/3)) %>% #power = 1/3
  ggplot(aes(y = lifeExp, x =  TgdpPercap)) + 
  geom_point()
```

<img src="03-linear-regression_files/figure-html/unnamed-chunk-68-1.png" width="672" />

Not quite straight. Let's keep going.



```r
gapminder %>% 
  filter(year > 2005) %>%
  mutate(TgdpPercap = log(gdpPercap)) %>% #power = 0
  ggplot(aes(y = lifeExp, x =  TgdpPercap)) + 
  geom_point()
```

<img src="03-linear-regression_files/figure-html/unnamed-chunk-69-1.png" width="672" />

Getting better. Let's try to keep going.


```r
gapminder %>% 
  filter(year > 2005) %>%
  mutate(TgdpPercap = -1/gdpPercap) %>%  #power = -1 (added a negative sign to maintain positive relationship)
  ggplot(aes(y = lifeExp, x =  TgdpPercap)) + 
  geom_point()
```

<img src="03-linear-regression_files/figure-html/unnamed-chunk-70-1.png" width="672" />

TOO FAR! Back up. Let's stick with log(gdpPercap).

Now we see some unequal spread so let's also try transforming $y$.


```r
gapminder %>% 
  filter(year > 2005) %>%
  mutate(TgdpPercap = log(gdpPercap)) %>% 
  mutate(TlifeExp = lifeExp^2) %>% 
  ggplot(aes(y = TlifeExp, x =  TgdpPercap)) + 
  geom_point()
```

<img src="03-linear-regression_files/figure-html/unnamed-chunk-71-1.png" width="672" />

That doesn't change it much. Maybe this is as good as we are going to get. 

Transformations can't make relationships look exactly linear with equal spread, but sometimes we can make it closer to that ideal.

Let's try and fit a model with these two variables.


```r
lm.gap <- gapminder %>% 
  filter(year > 2005) %>%
  mutate(TgdpPercap = log(gdpPercap)) %>% 
  lm(lifeExp ~ TgdpPercap, data = .)

summary(lm.gap)
```

```
## 
## Call:
## lm(formula = lifeExp ~ TgdpPercap, data = .)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -25.947  -2.661   1.215   4.469  13.115 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   4.9496     3.8577   1.283    0.202    
## TgdpPercap    7.2028     0.4423  16.283   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 7.122 on 140 degrees of freedom
## Multiple R-squared:  0.6544,	Adjusted R-squared:  0.652 
## F-statistic: 265.2 on 1 and 140 DF,  p-value: < 2.2e-16
```

**Interpretations**

What does the slope of this model, $\beta_1 = 7.2028$, mean in this data context?

$$\widehat{LifeExp} = \beta_0 + \beta_1 log(Income)$$


- The slope is the the additive increase in the predicted $\widehat{LifeExp}$ when $log(Income)$ increases to $log(Income) + 1$.

- Let's think about $log(Income) + 1$. Using some rules of logarithms:

$$log(Income) + 1 = log(Income) + log(e^1) = log(e*Income) = log(2.71*Income)$$
So adding 1 to $log(Income)$ is equivalent to multiplying Income by 2.71.


In our model, we note that if a country's income measured by GDP increased by a multiplicative factor of 2.71, the predicted average life expectancy of a country increases by about 7.2 years. 


For the sake of illustration, imagine we fit a model where we had transformed life expectancy with a log transformation. What would the slope, $\beta_1$, mean in this context?

$$\widehat{log(LifeExp)} = \beta_0 + \beta_1 Income$$


- The slope is the the additive increase in $\widehat{log(LifeExp)}$ when $Income$ increases to $Income + 1$.


- Let's think about $\widehat{log(LifeExp)} + \beta_1$. Using rules of logarithms: 
$$\widehat{log(LifeExp)} + \beta_1 = \widehat{log(LifeExp)} + log(e^{\beta_1}) = log(\widehat{LifeExp} * e^{\beta_1}) $$
The additive increase of $\beta_1$ units in $\widehat{log(LifeExp)}$ is a multiplicative increase of $\widehat{LifeExp}$ by a factor of $e^{\beta_1}$.

#### Alternative Solutions

We could also model non-linear relationships by including higher degree terms in a linear model like the example below. By using `poly()`, we now include $x$ and $x^2$ as variables in the model.


```r
x <- rnorm(100, 5, 1)
y <- 200 + 20*x - 5*x^2 + rnorm(100,sd = 10)
dat <- data.frame(x,y)
dat %>%
    ggplot(aes(x = x, y = y)) +
    geom_point() + 
    geom_smooth(se = FALSE)
```

<img src="03-linear-regression_files/figure-html/unnamed-chunk-73-1.png" width="672" />

```r
lm(y ~ poly(x, degree = 2, raw = TRUE), data = dat)
```

```
## 
## Call:
## lm(formula = y ~ poly(x, degree = 2, raw = TRUE), data = dat)
## 
## Coefficients:
##                      (Intercept)  poly(x, degree = 2, raw = TRUE)1  
##                          192.806                            23.305  
## poly(x, degree = 2, raw = TRUE)2  
##                           -5.379
```

A more advanced solution (which is not going to be covered in class) is a **generalized additive model** (GAM), which allows you to specify which variables have non-linear relationships with $y$ and estimates that relationship for you using spline functions (super cool stuff!). We won't talk about how this model is fit or how to interpret the output, but there are other cool solutions out there! If you are interested


```r
require(gam)
plot(gam(y ~ s(x), data = dat))
```

<img src="03-linear-regression_files/figure-html/unnamed-chunk-74-1.png" width="672" />


## Diagnostics

<span style="color:red">
This section is under construction and will be changing soon.
</span>

Residuals are what's left over from a model. We can actually learn a lot by studying what is left over, what is left unexplained by the model. (INSERT SCATOLOGY JOKE.)

What do we need for a simple linear model to make sense?

  * Variables are both **Quantitative**
  
  * Relationship is **Straight Enough**
  
  * There are no extreme **Outliers** 
  
  * Spread is roughly same throughout -- **No Thickening**
  
To check these, we look at the original scatterplot as well as a plot of the *residuals* against *fitted or predicted values*,


```r
augment(lm.gpa, data = sat) %>%
    ggplot(aes(x = .fitted, y = .resid)) +
    geom_point() +
    geom_smooth(se = FALSE) +
    geom_hline(yintercept = 0) +
    labs(x = "Fitted values (predicted values)", y = "Residuals") +
    theme_minimal()
```

<img src="03-linear-regression_files/figure-html/unnamed-chunk-75-1.png" width="672" />

What do you think?

  * Is there any pattern in the residuals? (Is the original scatterplot straight enough?)
  * Is there equal spread in residuals across prediction values?
  
Studying the residuals can highlight subtle non-linear patterns and thickening in the original scatterplot. Think of it as a magnifying glass that helps you see these patterns.

Additionally, we want to avoid extreme outliers because points that are both far from the mean of $x$ and do not fit the overall relationship have **leverage** or the ability to change the line.

See the example below. See how the relationship changes with the addition of one point, one extreme outlier.  

<img src="03-linear-regression_files/figure-html/unnamed-chunk-76-1.png" width="672" /><img src="03-linear-regression_files/figure-html/unnamed-chunk-76-2.png" width="672" />

Check out this interactive visualization to get a feel for outlier points and their potential leverage: http://omaymas.github.io/InfluenceAnalysis/


### Solutions to Regression Issues

If the observed data do not satisfy the conditions above, what can we do? Should we give up using a statistical model? No!

- Problem: Both variables are NOT **Quantitative**
    - If your x-variable is categorical, we'll turn it into a quantitative variable using [**indicator variables**][Indicator Variables] (coming up) 
    - If you have a binary variable (exactly 2 categories) that you want to predict as your y variable, we'll use [**logistic regression**][Logistic Regression] (coming up)

- Problem: Relationship is NOT **Straight Enough**

    - If the plot does not thicken, we can add higher degree (e.g. $x^2, x^3, x^4$, etc.) terms to the model ([**multiple linear regression**][Multiple Linear Regression] - coming up).
    - If the plot does thicken, see solutions below.

- Problem: **Spread** is NOT the same throughout
    - You may be able to transform the y-variable using mathematical functions ($log(y)$, $y^2$, etc.) to make the spread more consistent (one approach is to use [**Box-Cox Transformation**](https://en.wikipedia.org/wiki/Power_transform#Box%E2%80%93Cox_transformation) -- take more statistics classes to learn more)
    - Be careful in interpreting the standard deviation of the residuals; be aware of the units. (For example, if you take a log transformation of the outcome, the units of the standard deviation of the residuals will be on the log scale.)

- Problem: You have **extreme outliers**
    - Look into the outliers. Determine if they could be due to human error or explain their unusual nature using the context. Think carefully about them, dig deep.
    - Do a **sensitivity analysis**: Fit a model with and without the outlier and see if your conclusions drastically change (see if those points had leverage to change the model).


## Chapter 3 Major Takeaways

1. All models are wrong, but some are useful and fair for our goals (prediction or description). 

2. We want a model with small residuals or a low number of prediction errors.

3. To determine if a model is useful and fair, we study what is left over (the residuals). 

4. We use models to describe phenomena by interpreting slope coefficients. But make sure you are talking about the average or predicted outcome! If you have multiple variables, you are keeping all others fixed (if possible).

5. We also use models to make predictions, but be careful about **extrapolating**: predicting outside the observed range of our predictor ($x$) variables.


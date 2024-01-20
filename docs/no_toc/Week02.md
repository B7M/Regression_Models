

# Week 2

## Statistical linear regression models

Up to this point, we’ve only considered estimation. Estimation is useful, but we also need to know how to extend our estimates to a population. This is the process of statistical inference. Our approach to statistical inference will be through a statistical model. At the bare minimum, we need a few distributional assumptions on the errors. However, we’ll focus on full model assumptions under Gaussianity.  

### Statistical Linear Regression Models

Finding a good regression line using least squares is a mathematical procedure. However, we'd like to do statistics. We'd like to draw emphasis based on our data. In other words we'd like to generalize from our data to a population using statistical models. Consider the probabilistic model for linear regression 
$$Y_i = \beta_0 + \beta_1 X_i + \epsilon_{i}$$
The values of $\beta_0, \beta_1$ are the population parameters that we would like to estimate. $X_i$ is a collection of explanatory variables that we do know, and $\epsilon_i$ is iid Gaussian errors. 

* Here the $\epsilon_{i}$ are assumed iid $N(0, \sigma^2)$.

Understanding independent errors in regression can be approached in various ways. One relatively straightforward interpretation is to consider them as the cumulative effect of unmodeled variables that might collectively influence the response. These unmodeled variables act on the response in a manner that can be statistically modeled as independent and identically distributed Gaussian errors.

Setting aside the complexities of interpretation, let's focus on the mechanics of working with statistical inference for regression. It's important to note that the expected value of the response given a specific value of the regressor is simply the line at that regressor, represented as $β_0 + β₁x_i$. Additionally, the variance of the response at any given value of the regressor is denoted as σ². It's crucial to clarify that this variance pertains to the variation around the regression line and not the overall response variance. Conditioning on X reduces the variation, making it lower than the unconditional response variance.

* Note, $E[Y_i ~|~ X_i = x_i] = \mu_i = \beta_0 + \beta_1 x_i$
* Note, $Var(Y_i ~|~ X_i = x_i) = \sigma^2$.

Both the expected value and variance mentioned here are population quantities. Although there are sample analogs that estimate these values, it's essential to recognize that, at this point, we are referring to population quantities—these are the estimands that we ideally want to know.

Now that we have a formal statistical framework, we can interpret our regression coefficients with respect to that framework. Take for example, the intercept. It is the expected value $Y$ given that the regressor is 0. $$E[Y | X = 0] =  \beta_0 + \beta_1 \times 0 = \beta_0$$

Note that the regressor being equal to zero is often not of interest in the study. For example, if the regression variable is blood pressure, probably you're not interested in the response for among people with blood pressure of zero. However, there is an easy fix for this. Consider just shifting our regression variable by a constant $a$. 
$$
Y_i = \beta_0 + \beta_1 X_i + \epsilon_i
= \beta_0 + a \beta_1 + \beta_1 (X_i - a) + \epsilon_i
= \tilde \beta_0 + \beta_1 (X_i - a) + \epsilon_i
$$
We see a new regression line with a new intercept and the same slope. So, shifting your $X$ values by value $a$ changes the intercept, but not the slope. 

* Often $a$ is set to $\bar X$ so that the intercept is interpretted as the expected response at the average $X$ value.

For slope, we can interpret it as the expected change in response for a 1 unit change in the predictor.
$$
E[Y ~|~ X = x+1] - E[Y ~|~ X = x] =
\beta_0 + \beta_1 (x + 1) - (\beta_0 + \beta_1 x ) = \beta_1
$$
* Consider the impact of changing the units of $X$. 
$$
Y_i = \beta_0 + \beta_1 X_i + \epsilon_i
= \beta_0 + \frac{\beta_1}{a} (X_i a) + \epsilon_i
= \beta_0 + \tilde \beta_1 (X_i a) + \epsilon_i
$$
We see a new regression line with a new slope and the same intercept. So, multiplying your $X$ values by value $a$ changes the slope, but not the intercept. For example, $X$ is height in $m$ and $Y$ is weight in $kg$. Then $\beta_1$ is $kg/m$. Converting $X$ to $cm$ implies multiplying $X$ by $100 cm/m$. To get $\beta_1$ in the right units, we have to divide by $100 cm /m$ to get it to have the right units. 

$$
X m \times \frac{100cm}{m} = (100 X) cm
~~\mbox{and}~~
\beta_1 \frac{kg}{m} \times\frac{1 m}{100cm} = 
\left(\frac{\beta_1}{100}\right)\frac{kg}{cm}
$$

If we would like to guess the outcome at a particular value of the predictor, say $X$, the regression model guesses $$\hat \beta_0 + \hat \beta_1 X$$

This doesn't mean that we can only predict at the fitted values. We can predict at any value of $X$ by plugging in the value of $X$ into the equation. However, we're going to have more reasonable predictions if the value of $X$ that we plug in is in the cloud of data that we used to build the model. Later on, we'll also talk about how to account for that kind of uncertainty with prediction intervals. But for the time being, let's just talk about how we get a prediction. Let's go through an example to interpret the regression
coefficients and show running of the regression coefficient. The dataset is the diamond dataset from the `UsingR` package. The data is diamond prices in Singapore dollars and diamond weight in carats, which is a standard measure of diamond mass. 


```r
library(UsingR)
```

```
## Loading required package: MASS
```

```
## Loading required package: HistData
```

```
## Loading required package: Hmisc
```

```
## Loading required package: lattice
```

```
## Loading required package: survival
```

```
## Loading required package: Formula
```

```
## Loading required package: ggplot2
```

```
## 
## Attaching package: 'Hmisc'
```

```
## The following objects are masked from 'package:base':
## 
##     format.pval, units
```

```
## 
## Attaching package: 'UsingR'
```

```
## The following object is masked from 'package:survival':
## 
##     cancer
```

```r
data(diamond)
library(ggplot2)
g = ggplot(diamond, aes(x = carat, y = price))
g = g + xlab("Mass (carats)")
g = g + ylab("Price (SIN $)")
g = g + geom_point(size = 7, colour = "black", alpha=0.5)
g = g + geom_point(size = 5, colour = "blue", alpha=0.2)
g = g + geom_smooth(method = "lm", colour = "black")
g
```

```
## `geom_smooth()` using formula 'y ~ x'
```

<img src="resources/images/Week02_files/figure-html/unnamed-chunk-1-1.png" width="672" />
In this code we assign variable `g` to the `ggplot`, the dataset is diamond, the aesthetic has the horizontal axis variable as carat and the y-axis variable as price, we add a layer where the xlab is `Mass in carats` and the y label price in Singapore dollars. We also add the points of the black background and then a light alpha blending color on top. Afterwards we add a layer that is `geom_smooth` where `method = "lm"` will add the regression line. If you omit any arguments, it's just going to assume the regression
line with $Y$ as the outcome and $X$ as the predictor. Finally, we indicate the color of the regression line as black and call the plot.

Notice what we are plotting is the fitted line, the line that minimizes the sum of the squared vertical distances between the points and the lines. 
By default, `lm` includes an intercept, if you don't want an intercept, you have to explicitly force it in the model. We also want the dataset to be the diamond dataset in other words, we have to give it the data frame. Otherwise, `lm` looks in the regular R environment for variables in the model. After running the code it basically just prints out the coefficients $\beta_0, \beta_1$, which are the intercept and labels it as Intercept and the regression variable for the carat, the slope for the carat regression variable.


```r
fit <- lm(price ~ carat, data = diamond)
coef(fit)
```

```
## (Intercept)       carat 
##   -259.6259   3721.0249
```
Let's look at this $3,721$ variable and try to interpret it. It's saying that we have an expected $3,721$ Singapore dollar increase in price for every carat increase in mass of the diamond. The intercept, $-259$ is the expected price of a $0$ carat diamond not very interesting, because we're not interested in zero carat diamonds. 

**A side note**, if you want a much more detailed printout by doing `summary(fit)` which is the summary of the outputted variable from `lm` and you get this more elaborate printout. 


```
## 
## Call:
## lm(formula = price ~ carat, data = diamond)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -85.159 -21.448  -0.869  18.972  79.370 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  -259.63      17.32  -14.99   <2e-16 ***
## carat        3721.02      81.79   45.50   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 31.84 on 46 degrees of freedom
## Multiple R-squared:  0.9783,	Adjusted R-squared:  0.9778 
## F-statistic:  2070 on 1 and 46 DF,  p-value: < 2.2e-16
```

If we mean center our $X$ variable, so that the intercept is on a more interpretable scale. Here we assign the output to a different variable, `fit2` instead of `fit`, because we don't want to overwrite the original `fit`. 


```r
fit2 <- lm(price ~ I(carat - mean(carat)), data = diamond)
coef(fit2)
```

```
##            (Intercept) I(carat - mean(carat)) 
##               500.0833              3721.0249
```

As you notice in code: `lm` is again the linear model procedure, the outcome stays the same and we use `carat - mean(carat)`, and the `I` is to indicate that we want to do arithmetic on the variable. So, we want to subtract the mean of the carat variable from the carat variable. This is a way to mean center the variable. 
As we expected the slope stays the same, 3,721, but the intercept has changed to $500$, meaning $\$ 500$, Singapore dollars is the expected price of the average sized diamond. In this case, the average diamond is about 0.2 carats. A one carat increase is actually kind of big. What about changing the units to one-tenth of a carat? We can do this just by dividing the coefficient by ten. So we know that we would expect to see a $372 increase in price for every %0.1$ of a carat increase in the mass of a diamond. 



In the linear model fit instead of putting in carat, we put in $carat * 10$, the units of this new variable is one-tenth of a carat. The data is of course, still the diamond dataset.

Imagine if someone came to you with three new diamonds that they had 0.16 carats, 0.27 carats and 0.35 carats, and they wanted to know what you would estimate the price would be. Well, you could do it manually by grabbing the two coefficients in multiplying the intercept or adding the intercept plus the slope times these new values. Let's do that:


```r
newx <- c(0.16, 0.27, 0.34)
coef(fit)[1] + coef(fit)[2] * newx
```

```
## [1]  335.7381  745.0508 1005.5225
```

```r
predict(fit, newdata = data.frame(carat = newx))
```

```
##         1         2         3 
##  335.7381  745.0508 1005.5225
```

Often, you don't want to do even that much coding, you want to more general method, especially when you get lots of regression variables. So there's this general method called
predict that will take the output from several different kinds of model fits. Linear models are one example, but predict is a generic function, and it applies to several different prediction models. The new data is a `data.frame` that has the new values of $X$ for the carat variable. Then when we do that, what you'll see is the same answer. The difference is that it scales up when we have lots of regressors in much more complicated settings. In general, we want to predict using the predict function. If you omit this new data statement if you just do predict fit, it predicts at the observed $X$ values, so it gives you the $\hat Y$ values. If you want it at new $X$ values, you have to give it this new data argument.


```r
data(diamond)
plot(diamond$carat, diamond$price,  
     xlab = "Mass (carats)", 
     ylab = "Price (SIN $)", 
     bg = "lightblue", 
     col = "black", cex = 1.1, pch = 21,frame = FALSE)
abline(fit, lwd = 2)
points(diamond$carat, predict(fit), pch = 19, col = "red")
lines(c(0.16, 0.16, 0.12), 
      c(200, coef(fit)[1] + coef(fit)[2] * 0.16,
      coef(fit)[1] + coef(fit)[2] * 0.16))
lines(c(0.27, 0.27, 0.12), 
      c(200, coef(fit)[1] + coef(fit)[2] * 0.27,
        coef(fit)[1] + coef(fit)[2] * 0.27))
lines(c(0.34, 0.34, 0.12), 
      c(200, coef(fit)[1] + coef(fit)[2] * 0.34,
        coef(fit)[1] + coef(fit)[2] * 0.34))
text(newx, rep(250, 3), labels = newx, pos = 2)
```

<img src="resources/images/Week02_files/figure-html/unnamed-chunk-6-1.png" width="672" />

To illustrate, here's our observe data points in blue. The fitted values when we do the predict command, the fitted values in red all of the observed $X$ values and their associated fitted points on the line. These are if we were to draw vertical lines from the observed data points on to the fitted line, they would occur on these red points. When we predicted a new value of $X$, we're finding a point along this horizontal axis. In this example we want, 0.16, 0.27 and 0.34. We're drawing a line up to the fitted regression line and then over to dollars and those are our predicted dollar amounts.

## Residuals



## Inference in regression



## For the project


## Practical R Exercises in swirl


## Week 2 Quiz

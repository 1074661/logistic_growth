# Logistic Growth

# Question 1

## Plotting data

Script 1 (plot_data.R) plots the dataset from experiment.csv (a simulated experiment that visualises the logistic model). The x-axis shows time (t) and the y-axis shows the number of bacteria (N). Plotting was conducted using ggplot2 package on R to produce publication-quality figures. 

The second plot in this script is a semi-log plot with a linear x-axis and a log-transferred y-axis. This semi-log plot shows that at early time points, there is an increasing linear relationship, while at later time points, population size remains constant. This semi-log plot is useful for future linear model analysis. 

```{r}
#Code to plot logistic growth data
install.packages("ggplot2")
library(ggplot2)
growth_data <- read.csv("experiment.csv")

#Logistic plot
ggplot(aes(t,N), data = growth_data) +
  geom_point() +
  xlab("Time (minutes)") +
  ylab("Population Size (number of bacteria)") +
  theme_bw()

#Semi-log plot
ggplot(aes(t,N), data = growth_data) +
  geom_point() +
  xlab("Time (minutes)") +
  ylab("Population Size (number of bacteria)") +
  scale_y_continuous(trans='log10') +
  theme_bw()
  
#Semi-log plot - distinguishing when t is small or large 

growth_data$threshold <- ifelse(
  growth_data$t<=2000, 'Below', 'Above'
)

ggplot(aes(t,N, color = threshold), data = growth_data) +
  geom_point() +
  xlab("Time (minutes)") +
  ylab("Population Size (number of bacteria)") +
  scale_y_continuous(trans='log10') +
  theme_bw() +
  theme(legend.position = 'none')
```

#### Insert Logistic growth plot

#### Insert Semi-log plot showing relationship between t and N

#### Insert Semi-log plot showing relationship between t and N (with colour demarcating when t is small (light blue) or large (red)).

## Fitting linear models

Script 2 (fit_linear_model.R) generates two different linear models for the semi-logged data. In the first model, t is small, while in the second model, t is large. In these models, two distinct relationships occur as interpreted from the semi-log plot. For this, dplyr package was used to filter the dataset (filter () when t is large or small), enabling prediction of underlying logistic parameters. 

To generate linear models (t as explanatory variable and log_N as response variable), lm() function was used. For parameter estimates of slopes and intercepts in the two distinct linear models, summary() function was used. 

The code below shows the subsetting of data and generation of linear models. 

```{r}
install.packages("dplyr")
library(dplyr)

# Scenario 1. K >> N0, t is small

data_subset1 <- growth_data %>% 
  filter(t<1500) %>% 
  mutate(N_log = log(N))

linearmodel1 <- lm(N_log ~ t, data_subset1)
summary(linearmodel1)

#Scenario 2. N(t) = K

data_subset2 <- growth_data %>% filter(t>2500)

linearmodel2 <- lm(N ~ 1, data_subset2)
summary(linearmodel2)
```
## Plotting my own models

Script 3 (plot_data_and_model.R) generates the 'logistic_function' function. This allows generation of my own logistic curve, in which logistic parameters (N0, r and K) can be modified, followed by superimposition onto "experiment.csv" dataset. This enables evaluation of whether my own logistic parameters are consistent with those underlying the logistic curve in "experiment.csv" dataset. 

## Results

## Linear Model 1 (small t)

#### Insert summary table for Linear Model 1

The summary table provides the parameters for Linear Model 1 (in the form 'y = a + bx') where $$ln(N) = ln(N0) + rt$$

Per the summary table, intercept (ln(N0)) is 6.8941709; therefore, **N0 is 986** (since e^6.8941709 = 986.5074723).

Per the summary table, t (r) is 0.0100086; therefore, **r is 0.01**. 

## Linear Model 2 (large t)

#### Insert summary table for Linear Model 2

Per the summary table, intercept (K) is **6 x 10^10**. 

Confirmation of these logistic parameters was achieved by superimposing my linear function (with these logistic parameters) over experimental datapoints. This is shown by the figure below. 

#### Insert figure of logistic function with logistic parameters obtained from linear model superimposed over experimental datapoints

This figure validates determination by linear model analyses of the logistic parameters underlying the experimental dataset, since the superimposed linear function is consistent with experimental datapoints. 

This enables use of my logistic function for interpolation of population size at specified timepoints. 

















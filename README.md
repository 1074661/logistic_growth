# Logistic Growth

# Question 1

## Plotting data

The first script (plot_data.R) plots the dataset from experiment.csv (a simulated experiment that visualises the logistic model). The x-axis shows time (t) and the y-axis shows the number of bacteria (N). Plotting was conducted using ggplot2 package on R to produce publication-quality figures. 

The second plot in the script is a semi-log plot with a linear x-axis and a log-transferred y-axis. This semi-log plot shows that at early time points, there is an increasing linear relationship, while at later time points, population size remains constant. This semi-log plot is useful for future linear model analysis. 

```{r}
#Script to plot logistic growth data
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

The second script (fit_linear_model.R) generates two different linear models for the semi-logged data. In the first model, t is small, while in the second model, t is large. In these models, two distinct relationships occur as interpreted from the semi-log plot. For this, dplyr package was used to filter the dataset (filter () when t is large or small), enabling prediction of underlying logistic parameters. 

To generate linear models (t as explanatory variable and log_N as response variable), lm() function was used. For parameter estimates of slopes and intercepts in the two distinct linear models, summary() function was used. 

The code below explains the subsetting of data and generation of linear models. 

```{r}
# Scenario 1. K >> N<sub>0, t is small

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














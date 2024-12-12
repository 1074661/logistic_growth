# Logistic Growth

## Question 1

## Plotting data

The first script (plot_data.R) plots the dataset from experiment.csv (a simulated experiment that visualises the logistic model). The x-axis shows time (t) and the y-axis shows the number of bacteria (N). Plotting was conducted using ggplot2 package on R to produce publication-quality figures. 

The second plot in the script is a semi-log plot with a linear x-axis and a log-transferred y-axis. This semi-log plot shows that at early time points, there is an increasing linear relationship, while at later time points, population size remains constant. This semi-log plot is useful for future linear model analysis. 

```{r}
#Script to plot logistic growth data
library(ggplot2)
growth_data <- read.csv("experiment.csv")

#Logistic plot
ggplot(aes(t,N), data = growth_data) +
  geom_point() +
  xlab("Time (minutes)") +
  ylab("Population Size (number of bacteria)" +
  theme_bw()

#Semi-log plot
ggplot(aes(t,N), data = growth_data) +
  geom_point() +
  xlab("Time (minutes)") +
  ylab("Population Size (number of bacteria)" +
  scale_y_continuous(trans='log10') +
  theme_bw()
  
#Semi-log plot - distinguishing when t is small or large 

growth_data$threshold <- ifelse(
  growth_data$t<=2000, 'Below', 'Above'
)

ggplot(aes(t,N, color = threshold), data = growth_data) +
  geom_point() +
  xlab("Time (minutes)") +
  ylab("Population Size (number of bacteria") +
  scale_y_continuous(trans='log10') +
  theme_bw() +
  theme(legend.position = 'none')
```
#### Insert logistic growth plot

#### Semi-log plot showing relationship between t and N




















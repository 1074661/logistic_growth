# Logistic Growth

# Question 1

From a simulated experiment, the experimental data in the file 'experiment.csv' are measurements obtained from a test tube containing 900μl of growth media and an isolate of the bacterium Escherichia coli suspended in 100μl of the same media. An Escherichia coli isolate divides and generates a population in culture through different phases of logistic population growth. During the lag phase of E. coli logistic population growth, the population size of this bacterium (number of bacteria) in culture is small. During the exponential phase of E. coli logistic population growth, the population size of this bacterium in culture is rapidly increasing. As the population size of the bacterium increases and approaches the environmental carrying capacity in the test tube (governed by limiting resources), E. coli enters stationary equilibrium phase of logistic population growth. 

## Plotting data

Script 1 (plot_data.R) plots the dataset from experiment.csv. The x-axis shows time (t) and the y-axis shows the number of bacteria (N). Plotting was conducted using ggplot2 package on R to produce publication-quality figures. 

The second plot in this script is a semi-log plot with a linear x-axis and a log-transformed y-axis. This semi-log plot shows that at early time points, there is an increasing linear relationship, while at later time points, population size remains constant. This semi-log plot is useful for future linear model analysis. 

```{r}

install.packages("ggplot2")
library(ggplot2)

#Code to plot logistic growth data
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
#### Logistic growth plot (lag phase, exponential phase and stationary phase of bacterial population growth in temporal order)
![image](https://github.com/user-attachments/assets/516c5c4e-8a08-4a8f-958b-db2cf1131afd)

#### Semi-log plot showing relationship between t and N
![image](https://github.com/user-attachments/assets/c1dc79dd-5996-4225-ad95-5cf1d46cc995)

#### Semi-log plot showing relationship between t and N (with colour demarcating when t is low (light blue) and high (red)).
![image](https://github.com/user-attachments/assets/e092a0f4-6059-49ca-a446-88124d35b127)

## Fitting linear models

Script 2 (fit_linear_model.R) generates two different linear models for the semi-logged data. In the first model, t is small, while in the second model, t is large. In these models, two distinct relationships occur as interpreted from the semi-log plot. For this, dplyr package was used to filter the dataset (filter () when t is large or small), enabling prediction of underlying logistic parameters. 

To generate linear models (t as explanatory variable and log_N as response variable), lm() function was used. For parameter estimates of slopes and intercepts in the two distinct linear models, summary() function was used. 

The code below shows the subsetting of data and generation of linear models. 

```{r}

install.packages("dplyr")
library(dplyr)

# Scenario 1. K >> N0, t is small

data_subset1 <- growth_data %>% 
  filter(t<1500) %>% # Filtering dataset to timepoints below t=1500 to ensure capture only of period of exponential growth of E. coli population.
  mutate(N_log = log(N))

linearmodel1 <- lm(N_log ~ t, data_subset1)
summary(linearmodel1)

#Scenario 2. N(t) = K

data_subset2 <- growth_data %>% filter(t>2500) # Filtering dataset to timepoints above t=2500 to ensure capture of E. coli population held at the stationary phase at environmental carrying capacity.

linearmodel2 <- lm(N ~ 1, data_subset2)
summary(linearmodel2)
```
## Plotting my own models

Script 3 (plot_data_and_model.R) produces the 'logistic_function' function. This allows generation of my own logistic curve, in which logistic parameters (N0, r and K) can be modified. This logistic curve can be superimposed onto "experiment.csv" dataset to enable evaluation of whether my own logistic parameters are consistent with the logistic parameters underlying the logistic curve in "experiment.csv" dataset. 

## Results

## Linear Model 1 (low t)

#### Summary table for Linear Model 1
![image](https://github.com/user-attachments/assets/8bdcd0f7-e606-4da3-a942-758761c646d2)

The summary table provides the parameters for Linear Model 1 (in the form 'y = a + bx') where $$ln(N) = ln(N0) + rt$$

Per the summary table, intercept (ln(N0)) is 6.8941709; therefore, **N0 is 986** (since e^6.8941709 = 986.5074723).

Per the summary table, t (r) is 0.0100086; therefore, **r is 0.01**. 

## Linear Model 2 (high t)

#### Summary table for Linear Model 2
![image](https://github.com/user-attachments/assets/5c94705c-4645-4f4d-acb1-fca6c98fb327)

Per the summary table, intercept (K) is 6 x 10^10; therefore, **K is 60000000000**.

Validation of these logistic parameters was achieved by superimposing my linear function (with these logistic parameters) over experimental datapoints. This is shown by the figure below, generated using my filled-in Script 3 (plot_data_and_model.R):

```{r}

install.packages("ggplot2")
library(ggplot2)

N0 <- 986 # Initial bacterial population size

r <- 0.01 # Growth rate

K <- 6.00e+10 # Environmental carrying capacity 

logistic_function <- function(t) {
  
  N <- (N0*K*exp(r*t))/(K-N0+N0*exp(r*t))
  
  return(N)
  
}

# Superimposing model over experimental data

ggplot(aes(t, N), data = growth_data) +
  
  geom_function(fun=logistic_function, colour="darkorange") +
  
  geom_point() +
  
  labs(
    x = "Time (minutes)",
    y = "E. coli population size (number of bacteria)"
  )
```
#### Plot of logistic function with logistic parameters obtained from linear model superimposed over experimental datapoints
![image](https://github.com/user-attachments/assets/ab450e5f-ad83-4cba-bcea-2c2e5a1be4a5)

This figure validates determination by linear model analyses of the logistic parameters underlying the experimental dataset, since the superimposed linear function is consistent with experimental datapoints. 

This enables use of my logistic function for interpolation of population size at specified timepoints. 

# Question 2

Under exponential population growth, N(t) = N0 * e^(r*t)

Substituting my estimates of N0 and r to calculate bacterial population size at t=4980 min: N(4980) = 986 * e^(0.01*4980) = 4.19 x 10^24

Under logistic population growth, bacterial population size at t=4980 min would be 6 x 10^10 since by this timepoint the bacterial population would have already reached and be held constant at the environmental carrying capacity.

Therefore, at time = 4980 minutes, bacterial population size would be 6.98x10^13 times larger under exponential growth than under logistic growth (since (4.19 x 10^24)/(6 x 10^10) = 6.98x10^13). 

# Question 3

Code used to construct a graph comparing the exponential and logistic growth curves is shown below.

```{r}

install.packages("ggplot2")
library(ggplot2)

# Defining logistic function
logistic_function <- function(t) {
  N <- (N0 * K * exp(r * t)) / (K - N0 + N0 * exp(r * t))
  return(N)
}

# Defining exponential function
exponential_function <- function(t) {
  N <- (N0 * exp(r * t))
  return(N)
}

# Setting parameters estimated from linear models for both exponential and logistic functions
N0 <- 986
r <- 0.01
K <- 60000000000

# Plotting both functions
ggplot() +
  geom_function(aes(colour = "Logistic"), fun = logistic_function, linewidth = 1.2) +  # Increasing line width
  geom_function(aes(colour = "Exponential"), fun = exponential_function, linewidth = 1.2) +  # Increasing line width
  xlim(0, 5000) +
  scale_y_continuous(trans = 'log10') +
  scale_colour_manual(values = c("Logistic" = "darkgreen", "Exponential" = "darkblue")) +
  xlab("Time (minutes)") +
  ylab("Population size (number\nof bacteria, log10 transformed)") +  # Stacking y-axis label
  ggtitle("Comparison of exponential and logistic\nmodels of bacterial population growth") +  # Stacking title
  theme_bw() +
  theme(
    legend.title = element_blank(),
    plot.title = element_text(face = "bold", hjust = 0.5),  # Centering title with hjust
    axis.title = element_text(face = "bold"), # Emboldening axes labels
    legend.text = element_text(face = "bold") # Emboldening legend labels
  )
```

#### Plot comparing exponential and logistic growth curves.
![image](https://github.com/user-attachments/assets/8710f49e-01e1-4adc-a0e3-2096ccbae9f2)

This graph illustrates the difference between exponential and logistic models of E. coli population growth. The graph suggests that over short time periods (t<1750 minutes), accurate forecasting of E. coli population size in-vitro is possible using an exponential growth model. The graph suggests that over long time periods, accurate forecasting of E. coli population size in-vitro can only be achieved using a logistic growth model that accounts for limited resources in the growth environment in-vitro, defined by carrying capacity (K). This is because the exponential growth model inaccurately predicts continual population growth under limited resource availability in the culture environment.

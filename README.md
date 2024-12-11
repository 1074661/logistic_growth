Logistic Growth

This R markdown contains a reproducible analysis of logistic growth.

Question 1

Plotting logistic growth data

install.packages("ggplot2")
library(ggplot2)

growth_data <- read.csv("experiment.csv")

#Plot 1: Logistic Growth Plot
ggplot(aes(t,N), data = growth_data) +
  
  geom_point() +
  
  xlab("Time (minutes)") +
  
  ylab("Population Size (no. of bacteria)") +
  
  theme_bw() +
  
  ggtitle("Figure 1: Logistic Growth Plot") +
  
  theme(plot.title = element_text(hjust = 0.5))

#Plot 2: Semi-Log Plot
ggplot(aes(t,N), data = growth_data) +
  
  geom_point() +
  
  xlab("Time (minutes)") +
  
  ylab("Population Size (no. of bacteria)") +
  
  scale_y_continuous(trans='log10') +
  
  theme_bw() +
  
  ggtitle("Figure 2: Semi-Log Plot") +
  
  theme(plot.title = element_text(hjust = 0.5))

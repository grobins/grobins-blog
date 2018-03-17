---
title: Visualising Departure and Arrival Times
author: null
date: '2018-03-18'
slug: visualising-departure-and-arrival-times
categories: []
tags: []
draft: yes
editor_options: 
  chunk_output_type: console
---


Imagine you are required to analyse the relationships between the times vehicles leaving one location and the times they arrive at the next.  This could be to determine which vehicles might have been stuck in congestion, or perhaps you want to find the optimal time to leave one location to arrive at the next.  Whatever the reason, there's a need to firstly visualise the data.  This post explores a few ways we can do that.  But first we need to create some data.


```{r}
require(lubridate)

randstart <- rbeta(1000, 2, 5)
randstop  <- rbeta(1000, 5, 2)

normalise <- function(x){
  return (x - min(x))/(max(x) - min(x))
}

randstart = normalise(randstart)
randstop  = normalise(randstop)


randstart <- ymd('2018-03-01') + seconds(86400 * randstart)
randstop  <- ymd('2018-03-01') + seconds(86400 * randstop)


randstart <- as.POSIXct(randstart)
randstop <- as.POSIXct(randstop)

df <- tibble(randstart, randstop) %>% 
  filter(randstart < randstop)


```




```{r}
require(ggplot2)
require(tidyverse)


df %>%
  ggplot() +
  geom_segment(aes(x = 0, xend = 1, y = randstart, yend = randstop), alpha = 0.1) +
  labs(x = "", y = "", title = "Departure and Arrival times.") +
  scale_x_continuous(breaks = c(0, 1), labels = c("Depart", "Arrive")) +
  theme_minimal() 

```


Average travel time
```{r}


travel_time <- df %>% 
  mutate(travel_time = randstop - randstart) %>% 
  ggplot(aes(travel_time)) +
  geom_density()

start_time <- df %>% 
  ggplot(aes(randstart)) +
  geom_density()

stop_time <- df %>% 
  ggplot(aes(randstop)) +
  geom_density()

require(gridExtra)


grid.arrange(travel_time, start_time, stop_time)

```





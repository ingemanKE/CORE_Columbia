---
title: "CORE Quick Time Series"
author: "Kurt Ingeman"
date: "2/18/2019"
output: 
  html_document: 
    keep_md: yes
---

#### Time series of counts of Chinook Salmon at Ice Harbor dam from 1962 with sea lion male counts and Southern Resident Killer Whales

```r
rm(list = ls())

library(dplyr)
library(gridExtra)
library(ggplot2)
```



```r
# call in data
fall <- read.csv("data/IHR_Fall_Chinook.csv")
spr <- read.csv("data/IHR_SprSum_Chinook.csv")
PDO <- read.csv("data/PDO.csv")
lion <- read.csv("data/sealion.csv")
killer <- read.csv("data/SRKW.csv")
```



```r
# combine data sets
fall <- fall %>% 
  rename(fall.adult = adult) %>% 
  mutate(fall.total = fall.adult + jack) %>% 
  select(-jack) 

spr <- spr %>% 
  rename(spr.adult = adult) %>% 
  mutate(spr.total = spr.adult + jack) %>% 
  select(-jack)

dat <- full_join(fall, spr, by = "year") %>% 
  left_join(., lion, by = c("year" = "Year")) %>% 
  left_join(., killer, by = c("year" = "Year")) %>% 
  left_join(., PDO, by = c("year" = "Year"))
```

## Plot TS

Plotting predators and prey on separate axes for now


```r
# plot time series
s <- 1.2
chinook <- ggplot(dat) +
  geom_line(aes(x=year, y=spr.adult), col = "#669900", size = s) +
  geom_line(aes(x=year, y=fall.adult), col = "#CC3300", size = s) +
  ylab("Chinook(Sp+Fl)")  +
  theme_classic() +
  theme(axis.text.y=element_blank(), 
        axis.text.x=element_blank(),
        axis.ticks.x=element_blank(),
        axis.title.x=element_blank())

lions <- ggplot(dat) +
  geom_line(aes(x=year, y=Male), col = "gray", size = s) +
  ylab("CSL(male)")  +
  theme_classic() +
  theme(axis.text.y=element_blank(),
        axis.text.x=element_blank(), 
        axis.ticks.x=element_blank(),
        axis.title.x=element_blank())

srkw <- ggplot(dat) +
  geom_line(aes(x=year, y=SR_Orca), col = "black", lty = 2, size = s) +
  scale_y_continuous(limits = c(65, 100)) +
  ylab("SRKW")  +
  theme_classic() +
  theme(axis.text.y=element_blank())

chinook
```

![](CORE_ts_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

```r
lions
```

![](CORE_ts_files/figure-html/unnamed-chunk-4-2.png)<!-- -->

```r
srkw
```

![](CORE_ts_files/figure-html/unnamed-chunk-4-3.png)<!-- -->

```r
# CORE_ts <- arrangeGrob(chinook, lions, srkw, nrow = 3) 

# arrangeGrob(chinook, lions, srkw, nrow = 3)
# ggsave("output/CORE_IHR_Time_Series.pdf", CORE_ts)
```


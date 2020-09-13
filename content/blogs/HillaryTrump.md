---
categories:
- ""
- ""
date: "2017-10-31T22:42:51-05:00"
description: Nullam et orci eu lorem consequat tincidunt vivamus et sagittis magna sed nunc rhoncus condimentum sem. In efficitur ligula tate urna. Maecenas massa sed magna lacinia magna pellentesque lorem ipsum dolor. Nullam et orci eu lorem consequat tincidunt. Vivamus et sagittis tempus.
draft: false
image: pic01.jpg
keywords: ""
slug: HillaryTrump
title: Hillary vs. Trump
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## 2016 California Contributors plots

This was a project that we created during our course to understand the difference in contribution received (in $) b/w Hillary and Trump in 2016. The graphs illustrate the top 10 cities (in California) from where Hillary and Trump recieved their contributions from. The R code is as shown below: 

We started by importing the California contributors dataset, the zip code data base and merged both datasets.

```{r, load_CA_data, warnings= FALSE, message=FALSE}

# Loading dataframes
CA_contributors_2016 <- vroom::vroom(here::here("data","CA_contributors_2016.csv"))
zip_code <- vroom::vroom(here::here("data","zip_code_database.csv"))

# Changing the column zip of our zip_code dataframe to a numeric value
zip_code <- zip_code %>% 
  mutate(zip = as.numeric(zip))

# Merging dataframes
CA_contributors_2016 <- CA_contributors_2016 %>% 
  inner_join(zip_code, by =  "zip")
```

Next, we cleaned our dataframe and calculated the variables that were used in our analysis.

```{r, clean_data}

CA_contributors_2016_cleaned <- CA_contributors_2016 %>%
  group_by(cand_nm,primary_city) %>% 
  summarise(total_amt = sum(contb_receipt_amt)) %>% 
  select(candidate = cand_nm, city = primary_city, money_raised = total_amt)

```

We then plotted Trump and Hillary's top 10 contribution cities graphs separately using the following R code.

```{r, hillary_trump_top10}
library(scales)

# Plot Hillary's top 10 contribution cities
hillary <- CA_contributors_2016_cleaned %>%
  filter(candidate == "Clinton, Hillary Rodham") %>%
  top_n(10, wt = money_raised) %>% 
  ggplot(aes(x = money_raised, y = reorder(city, money_raised))) + geom_col(fill = "dodgerblue3") +   
  facet_grid(.~candidate) +
  labs(title = NULL, x = NULL, y = NULL) +
  scale_x_continuous(labels = scales::dollar_format()) + 
  theme_update(plot.title = element_text(hjust = 0.5)) + #center the title
  theme(strip.background = element_rect(fill = "grey"), 
        stip.text = element_text(size = 8, color = "black"),
  ) + 
  theme_bw()

# Plot Trump's top 10 contribution cities
trump <- CA_contributors_2016_cleaned %>%
  filter(candidate == "Trump, Donald J.") %>%
  top_n(10, wt = money_raised) %>% 
  ggplot(aes(x = money_raised, y = reorder(city, money_raised))) + geom_col(fill = "brown3") +   
  facet_grid(.~candidate) +
  labs(title = NULL, x = NULL, y = NULL) +
  scale_x_continuous(labels = scales::dollar_format()) + 
  theme_update(plot.title = element_text(hjust = 0.5)) + #center the title
  theme(strip.background = element_rect(fill = "grey"), 
        stip.text = element_text(size = 8, color = "black"),
  ) + 
  theme_bw()

library(patchwork)

# Combine them together and add a main title/x axis
hillary_trump_top10 <- hillary + trump +
  plot_annotation(title = "Where did candidates raise most money?", caption = "Amount Raised") 
  theme_update(plot.title = element_text(size = 10, hjust = 0.1, face = "bold"),
               aspect.ratio = 9/4,
               plot.caption = element_text(hjust = 0.55, vjust = 0.5, face = "bold"))

```  

## Plot created

```{r hillary_trump_top10 , echo=FALSE}
plot(hillary_trump_top10)
```
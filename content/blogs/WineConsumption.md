---
categories:
- ""
- ""
date: "2017-10-31T22:42:51-05:00"
description: Nullam et orci eu lorem consequat tincidunt vivamus et sagittis magna sed nunc rhoncus condimentum sem. In efficitur ligula tate urna. Maecenas massa sed magna lacinia magna pellentesque lorem ipsum dolor. Nullam et orci eu lorem consequat tincidunt. Vivamus et sagittis tempus.
draft: false
image: pic07.jpg
keywords: ""
slug: WineConsumption
title: Top 25 consumers of wine
---

---
title: "WineConsumption.md"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

```{r, setup}
knitr::opts_chunk$set(
  message = FALSE, 
  warning = FALSE, 
  tidy=FALSE,     # display code as typed
  size="small")   # slightly smaller font for code
options(digits = 3)

# default figure size
knitr::opts_chunk$set(
  fig.width=6.75, 
  fig.height=6.75,
  fig.align = "center"
)
```


```{r load-libraries, warning=FALSE, message=FALSE, echo=FALSE}
library(tidyverse)  # Load ggplot2, dplyr, and all the other tidyverse packages
library(mosaic)
library(ggthemes)
library(lubridate)
library(fivethirtyeight)
library(here)
library(skimr)
library(janitor)
library(vroom)
library(tidyquant)
```

# Where Do People Drink The Most Beer, Wine And Spirits?

First, lets load our dataframe.

```{r, load_alcohol_data}
library(fivethirtyeight)
data(drinks)
```


Now we will check the variable types with glimpse and check if there are any missing values with skim.

```{r glimpse_skim_data}
glimpse(drinks)
skim(drinks)
```

There are no missing values, the variables are either integers, character or double.

Let's make a plot that shows the top 25 beer consuming countries.

```{r beer_plot}
beer_25 <- drinks %>% 
  arrange(desc(beer_servings)) %>% 
  top_n(25, beer_servings)

ggplot(beer_25,aes(x=beer_servings, y=reorder(country, beer_servings))) + 
  geom_col() +
  geom_text(aes(label = beer_servings, hjust = -0.5)) +
  labs(title = "Top 25 beer consuming countries", x="Beer servings", y="Country") +
  theme_bw()
```

Then, let's look at the top 25 wine consuming countries.

```{r wine_plot}
wine_25 <- drinks %>% 
  arrange(desc(wine_servings)) %>% 
  top_n(25, wine_servings) 

p1 <- ggplot(wine_25, aes(x=wine_servings, y=reorder(country, wine_servings))) + 
  geom_col() + 
  geom_text(aes(label = wine_servings, hjust = -0.5)) +
  labs(title = "Top 25 wine consuming countries", x="Wine servings", y="Country") +
  theme_bw()
  
p1
```

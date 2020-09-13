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

---
title: "blog5.md"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## R Markdown

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

# Challenge 2: 2016 California Contributors plots

In this challenge we will reproduce the plots of the 2016 California Contributors, shown below.

```{r challenge2, echo=FALSE, out.width="100%"}

knitr::include_graphics(here::here("images", "challenge2.png"), error = FALSE)

```

Firstly, we will import the CA contributors dataset and the zip code data base and merge both.

```{r, load_CA_data, warnings= FALSE, message=FALSE}

# Load dataframes
CA_contributors_2016 <- vroom::vroom(here::here("data","CA_contributors_2016.csv"))
zip_code <- vroom::vroom(here::here("data","zip_code_database.csv"))

# Changing the column zip of our zip_code dataframe to a numeric value
zip_code <- zip_code %>% 
  mutate(zip = as.numeric(zip))

# Merging dataframes
CA_contributors_2016 <- CA_contributors_2016 %>% 
  inner_join(zip_code, by =  "zip")

```

Secondly, we will clean our dataframe and select/calculate the variables that will be used in our analysis.

```{r, clean_data}

CA_contributors_2016_cleaned <- CA_contributors_2016 %>%
  group_by(cand_nm,primary_city) %>% 
  summarise(total_amt = sum(contb_receipt_amt)) %>% 
  select(candidate = cand_nm, city = primary_city, money_raised = total_amt)

```

Then, we will plot Trump's and Hillary's top 10 contribution cities graphs separately, and change its aesthetics.

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

```

Now using patchwork it is possible to join both graphs in a singular visualization

```{r,hillary_trump}
library(patchwork)

# Combine them together and add a main title/x axis
hillary_trump_top10 <- hillary + trump +
  plot_annotation(title = "Where did candidates raise most money?", caption = "Amount Raised") 
  theme_update(plot.title = element_text(size = 10, hjust = 0.1, face = "bold"),
               aspect.ratio = 9/4,
               plot.caption = element_text(hjust = 0.55, vjust = 0.5, face = "bold"))

# Save it as a picture and set the correct proportion
ggsave("hillary_trump_top10.jpg", plot = hillary_trump_top10, width = 9, height = 4, path = here::here("images"))

# Show created picture
knitr::include_graphics(here::here("images", "hillary_trump_top10.jpg"))

```  


```{r cars}
summary(cars)
```

## Including Plots

You can also embed plots, for example:

```{r pressure, echo=FALSE}
plot(pressure)
```

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.

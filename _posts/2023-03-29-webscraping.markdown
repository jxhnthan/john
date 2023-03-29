---
title: "Webscraping Google News"
layout: post
date: 2023-01-23 22:10
tag: r
image: false
headerImage: false
projects: true
hidden: true # don't count this post in blog pagination
description: "This project showscases how one can webscrape news articles from Google News."
category: project
author: johndoe
externalLink: false
---

## What exactly is webscraping?

Webscraping is a technique used to collect content and data from the internet.

## What software can we use?

For this example, we will be using R. Typically, both [R][1] and [Python][2] works well.

## Let's jump straight into the code:

Start by loading the libraries that we will be using.

```{r}
library(rvest)
library(tidyverse)
library(openxlsx)
library(dplyr)
```

Next, we will be setting the base website URL that R will scrape the data from:

```{r}
base_url <- "https://news.google.com"
```

Alright, here's where the magic happens:

```{r}
for (page_num in 1:20) {
  url <- paste0(base_url, "/search?q=Climate%20Change&hl=en-SG&gl=SG&ceid=SG%3Aen",(page_num - 1) * 100)
  webpage <- read_html(url)
  news_div <- html_nodes(webpage, ".xrnccd")
  news_title <- html_nodes(news_div, ".DY5T1d") %>% html_text()
  news_date <- html_nodes(news_div, ".WW6dff") %>% html_text()
  news_url <- html_nodes(news_div, ".DY5T1d") %>% html_attr("href") %>% str_remove("^\\.")
  news_outlet <- html_nodes(news_div, ".wEwyrc") %>% html_text()
  news_link <- lapply(news_url, function(url) paste0(base_url, url))
  page_df <- data.frame(title = news_title, outlet = news_outlet, link = unlist(news_link), date = news_date)
  if (page_num == 1) {
    news_df <- page_df
  } else {
    news_df <- rbind(news_df, page_df)
  }
}
```

## Breaking down the code

So what exactly are we doing here? Let's break down the code bit by bit:

```{r}
for (page_num in 1:20) {
   ...
}
```
Firstly, we're using sets up a *for loop* that will iterate through 20 pages of Google News Singapore search results related to climate change. The loop variable, page_num, will take on values from 1 to 20.

```{r}
url <- paste0(base_url, "/search?q=Climate%20Change&hl=en-SG&gl=SG&ceid=SG%3Aen",(page_num - 1) * 100)
```
Next, this line of code constructs a URL for a specific page of Google News search results. The URL is built using the paste0() function, which concatenates its arguments into a single string with no separator.

The expression *(page_num - 1) * 100* calculates the starting index for the search results to be displayed on each page.



[1]: https://posit.co/products/open-source/rstudio/
[2]: https://www.python.org



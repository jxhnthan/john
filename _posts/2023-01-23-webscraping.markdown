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
author: john
externalLink: false
---

## What exactly is webscraping?

Webscraping is a technique used to collect content and data from the internet.

## What software can we use?

For this example, we will be using R to scrape Google News & store the output into an excel file.

Typically, both [R][1] and [Python][2] works well.

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

Alright, here's where the magic happens - we will be searcing for articles relating to "Climate Change".

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

Firstly, we're using sets up a *for loop* that will iterate through 20 pages of Google News Singapore search results related to climate change. The loop variable, page_num, will take on values from 1 to 20.

```{r}
for (page_num in 1:20) {
   ...
}
```

Next, this line of code constructs a URL for a specific page of Google News search results. The URL is built using the paste0() function, which concatenates its arguments into a single string with no separator.

The expression *(page_num - 1) * 100* calculates the starting index for the search results to be displayed on each page.

```{r}
url <- paste0(base_url, "/search?q=Climate%20Change&hl=en-SG&gl=SG&ceid=SG%3Aen",(page_num - 1) * 100)
```

Here, we're extracting the information that we want from each search result - the title of the news article, the date of the news article, the URL of the news article, the news outlet and the relevant article link.

```{r}
 news_div <- html_nodes(webpage, ".xrnccd")
  news_title <- html_nodes(news_div, ".DY5T1d") %>% html_text()
  news_date <- html_nodes(news_div, ".WW6dff") %>% html_text()
  news_url <- html_nodes(news_div, ".DY5T1d") %>% html_attr("href") %>% str_remove("^\\.")
  news_outlet <- html_nodes(news_div, ".wEwyrc") %>% html_text()
  news_link <- lapply(news_url, function(url) paste0(base_url, url))
```
Lastly, the *if-else statement* checks whether the current page is the first page of search results (if (page_num == 1)). If it is, the news_df data frame is assigned to the page_df data frame from the current page, and news_df is initialized with this data frame.

If the current page is not the first page (else), the rbind() function is used to combine the data from the current page (page_df) with the existing news_df data frame. rbind() stacks the rows of the two data frames on top of each other and returns a new data frame with the combined data.

```{r}
if (page_num == 1) {
    news_df <- page_df
  } else {
    news_df <- rbind(news_df, page_df)
  }
```


[1]: https://posit.co/products/open-source/rstudio/
[2]: https://www.python.org



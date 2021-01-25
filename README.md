# Twitter-shiny

For this assignment, you will learn to use data from twitter to develop a dashboard that has multiple interactive data visualizations. 

For references to using R markdown with Shiny, I recommend the rstudio website [Introduction to interactive documents](https://shiny.rstudio.com/articles/interactive-docs.html). For a deeper dive into Shiny's capabilities, you can also watch a full 2.5 hour [video tutorial](https://shiny.rstudio.com/tutorial/) or app development or the full module [Interactive Documents](http://rmarkdown.rstudio.com/authoring_shiny.html).


You may also be interested in the [R Markdown cheat sheet](https://shiny.rstudio.com/articles/rm-cheatsheet.html). 

# Sources

This assignment was inspired by an article on Medium called [Exploring tweets in R](https://medium.com/@traffordDataLab/exploring-tweets-in-r-54f6011a193d) by [Trafford Data lab](https://medium.com/@traffordDataLab). Additional resources were gathered from [Create a Shiny app to search Twitter with rtweet and R](https://www.infoworld.com/article/3516150/create-a-shiny-app-to-search-twitter-with-rtweet-and-r.html) by [Sharon Machlis](https://www.infoworld.com/author/Sharon-Machlis/).


# Instructions

## Creating a Rmd file for Shiny

To begin, load a Shiny document by selecting **New File** and then selecting **R Markdown...**. When prompted select **Shiny** and select **Shiny document**. Title this document **twitter-shiny**, add your name as author and then save the `.Rmd` file in the assignment folder. You will also find a new R script titled `twitter-shiny.Rmd`. 

Open the file and then use the **Run Document** button to load the interactive data visualization.

You should be able to see two input widgets that allow you to alter the histogram being displayed. Play with these features and read the full document. By the end of this assignment you should be able to make a simple widget for some twitter data.

At this point, save the file, write a commit message and ***commit***.

## Loading twitter data

For this assignment, you will have the option of authorizing a twitter account through `rtweet` to gather real-time data or use pre-loaded data to build your dashboard. 
Optional: For real-time updates , you may consider obtaining developer permission from Twitter to use their [API](https://developer.twitter.com/en/docs/twitter-api) directly and with large data queries. 

### Setup chunk

We will update the library to use some of the unique libraries that will help us to work with twitter data. Update the setup chunk to load packages as shown below.

```
{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
library('rtweet') ; library('tidyverse') ; library('syuzhet') ;library('emo') ; library('wordcloud') ; library('DT') ; library('tidytext')
```

You may not have these packages installed, so you will want to use the following code in the console to get the packages. 

```
install.packages(c('tidyverse','rtweet','tidytext','syuzhet', 'devtools', 'wordcloud', 'DT'))
devtools::install_github("hadley/emo")
```

Save the file, write a commit message and ***commit***.

### Introduction

Underneath the setup chunk, replace the text with the following header and text.

```## Introduction```

>This R Markdown document is made interactive using Shiny to demonstate my abilities to make interactive data visualizations. 

Save the file, write a commit message and ***commit***.

### Load data chunk

Create a new chunk under your replaced text called `{r load data, include = FALSE}`
 
Inside this chunk, load the twitter data of tweets containing the #rstats hashtag and those sent by the @RLadiesSeattle account. You have two options:

1. *If and only if* you have a twitter account and feel conformable giving permission to rstats2twitter's application to share your data then use the following code in the load data chunk. If you wish to visualize data of a different hashtag or account, you can do so with another term. Note that rtweet is limited to up to 18000 entries every 15 minutes, and only a few days. Our search filters language and excludes retweets and replies

```
tweets <- search_tweets(q = "#rstats", 
                        n = 16000,
                        include_rts = FALSE,
                        `-filter` = "replies") #%>%
  #mutate(favorite_count = as.numeric(favorite_count), 
         #retweet_count = as.numeric(retweet_count))
         
                        
user_timeline  <- get_timeline("RLadiesSeattle", n = 900)

```

2. If you do not have twitter or do not wish to share data with rstats2twitter developers, then load the default twitter data made on this files creation.

```
load("data.Rdata")
```
If this doesn't work you may have to specify the working directory. 
After successfully loading the stored data, you should be able to see a `tweets` data table in the environment tab. 

Save the file, write a commit message and ***commit***.

### Select 









# Twitter-shiny

Author: Caleb Trujillo

For this assignment, you will learn to use data from twitter to develop a dashboard that has multiple interactive data visualizations. 

For references to using R markdown with Shiny, I recommend the rstudio website [Introduction to interactive documents](https://shiny.rstudio.com/articles/interactive-docs.html). For a deeper dive into Shiny's capabilities, you can also watch a full 2.5 hour [video tutorial](https://shiny.rstudio.com/tutorial/) on app development, the LinkedIn Learning Course [Building Data Apps with R and Shiny: Essential Training](https://www.linkedin.com/learning-admin/content/courses/772334?account=67682169) or the full module [Interactive Documents](http://rmarkdown.rstudio.com/authoring_shiny.html).

You may also be interested in the [R Markdown cheat sheet](https://shiny.rstudio.com/articles/rm-cheatsheet.html). 

## Sources

This assignment was inspired by an article on Medium called [Exploring tweets in R](https://medium.com/@traffordDataLab/exploring-tweets-in-r-54f6011a193d) by [Trafford Data lab](https://medium.com/@traffordDataLab). Additional resources were gathered from [Create a Shiny app to search Twitter with rtweet and R](https://www.infoworld.com/article/3516150/create-a-shiny-app-to-search-twitter-with-rtweet-and-r.html) by [Sharon Machlis](https://www.infoworld.com/author/Sharon-Machlis/).


# Instructions

Make sure to write out the code to get in the habit of understanding the grammar of the code. Do not copy directly. The power of learning code is the creative avenues it unlocks. If you need help, remember there are lots of resources including:

- Discord
- Canvas Community Q&A
- The internet
- Your professor

## Creating a Rmd file for Shiny

To begin, load a Shiny document by selecting *New File* and then selecting **R Markdown...**. When prompted select **Shiny** and select **Shiny document**. Title this document ***twitter-shiny***, add your name as author and then save the `.Rmd` file in the assignment folder. You will also find a new R script titled `twitter-shiny.Rmd`. 

Open the file and then use the *Run Document* button to load the interactive data visualization.

You should be able to see two input widgets that allow you to alter the histogram being displayed. Play with these features and read the full document. By the end of this assignment you should be able to make a simple widget for some twitter data.

At this point, save the file, write a commit message and ***commit***.

Next delete the previous code so you can write your own, save the file, write a commit message and ***commit***.

## Loading twitter data

For this assignment, you will have the option of authorizing a twitter account through `rtweet` to gather real-time data or use pre-loaded data to build your dashboard. I provide sample twitter data in case you can't get rtweet to work.
Optional: For real-time updates , you may consider obtaining developer permission from Twitter to use their [API](https://developer.twitter.com/en/docs/twitter-api) directly and with large data queries. 

### Setup chunk

We will update the library to use some of the unique libraries that will help us to work with twitter data and manipulating text data. Update the setup chunk to load packages as shown below.

```
{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
pacman::p_load(rtweet,syuzhet, tidyverse, DT, stringr, wordcloud, tidytext)
```

You may not have these packages installed, so you will want to use the following code in the console to get the packages needed for this assignment. 

```
install.packages(c('tidyverse','rtweet','tidytext','syuzhet', 'devtools', 'wordcloud', 'DT'))
```

Save the file, write a commit message and ***commit***.

### Introduction

Underneath the setup chunk, replace the text with the following header and text.

```## Introduction```

`This R Markdown document is made interactive using Shiny to demonstrate my abilities to make interactive data visualizations. `

Save the file, write a commit message and ***commit***.

### Load data chunk

Create a new chunk under your replaced text called `{r load data, include = FALSE}`
 
Inside this chunk, load the twitter data of tweets containing the #rstats hashtag and those sent by the @RLadiesSeattle account. You have two options:

1. If you do not have twitter or do not wish to share data with rstats2twitter developers, then load the default twitter data made on this files creation.

```
load('data.Rdata')
```
If this doesn't work you may have to specify the working directory. 
```
load(`~/workspace/Twitter-shiny/data.RData`)
```
2. *If and only if* you have a twitter account and feel conformable giving permission to rstats2twitter's application to share your data then use the following code in the load data chunk. If you wish to visualize data of a different hashtag or account, you can do so with another term. Note that rtweet is limited to up to 18000 entries every 15 minutes, and only a few days. Our search filters language and excludes retweets and replies. You should see a download bar as this function gathers tweets. The download may stop before reaching 100% if there are fewer tweets in the last 9 days than the amount specified by n, in this case, fewer than 16000 tweets. 

```
{r load data, include = FALSE} 
tweets <- search_tweets(q = "#rstats", 
                        n = 16000,
                        include_rts = FALSE,
                        `-filter` = "replies") #%>%
  #mutate(favorite_count = as.numeric(favorite_count), 
         #retweet_count = as.numeric(retweet_count))
         
                        
user_timeline  <- get_timeline("RLadiesSeattle", n = 900)
```


After successfully loading the stored data, you should be able to see a `tweets` data table in the environment tab. 

Save the file, write a commit message and ***commit***.

### Create two tabs for organizing the data.

After your load data chunks, clear the remaining text, headers, and code chunks so you can have a blank space to add more.

Add headers and subheaders as follows:

```
## Twitter data {.tabset}

### Recent Rstats tweets

### Tweets from @RLadiesSeattle
```

You may want to change the name of the headers if using a different hashtag or user that you want to display.  The `{.tabset}` will enable your webpage to have the subordinate tabs as headers. 

Save the file, write a commit message and ***commit***.

## Recent Rstats tweets

### inputPanel

Under the first sub-header, `Recent Rstats tweets` we will add some text to describe what the visual will show.

`The plot below shows time series of #rstats tweets and can be filtered by language.`

Next, create a new chunk, titled `{r tweets, echo=FALSE}`. We will use this to add components of a shiny app to make the document interactive. Inside the chunk add an input panel which will allow you to collect information from your user. In this case we can ask the user to select the languages of tweets related to #rstats to include in the visual.

```
{r tweets, echo=FALSE}
inputPanel(
  selectInput("tweetlang", label = "Language of tweets:",
              choices = unique(tweets$lang), selected = "en", multiple = TRUE)
)
```

The input panel allows users to select from the choices from a menu of all options found in the twitter data sample. As a default, English `"en"` is selected. The label prompts users what to select and stores the value as an object that can be called by `input$tweetlang` 

Save the file, write a commit message and ***commit***.

Next, try to run the document using the *Run Document* button to see if you can use the input to find different languages by abbreviation.

### renderPlot

Continue to work in the `tweets` chunk and add code to render a plot so your code looks as follows:

```
{r tweets, echo=FALSE}
inputPanel(
  selectInput("tweetlang", label = "Language of tweets:",
              choices = unique(tweets$lang), selected = "en", multiple = TRUE)
)

renderPlot({
  ts_plot(tweets %>% filter(lang %in% input$tweetlang) , by = "hours", col = "blue")  +
    labs(x = NULL, y = NULL,
       title = "Frequency of tweets from containing #Rstats",
       subtitle = paste0(format(min(tweets$created_at), "%d %B %Y"), " to ", format(max(tweets$created_at),"%d %B %Y")),
       caption = "Data collected from Twitter's REST API via rtweet") + 
    theme_minimal()
})
```

The `renderPlot()` function will use the inputs to make a graph that can react to the information being changed. First specify that time-series plot function, `ts_plot` and use this to specify the data. Here we use piping `%>%` and the `filter()` function so that only data that is include are the tweets that have a language, `lang`, in the list of selected inputs `tweetlang`, which we specified in the `inputPanel`.  We can also add some additional features to make the plot look cleaner and have additional declarative information such as the title, source, and details.

Save the file, write a commit message and ***commit***.

Next, try to run the document using the *Run Document* button to see if you can use the input to graph different languages.

### renderDataTable

Continue to work in the `tweets` chunk and add code to render a data table so your code looks as follows:

```
{r tweets, echo=FALSE}
inputPanel(
  selectInput("tweetlang", label = "Language of tweets:",
              choices = unique(tweets$lang), selected = "en", multiple = TRUE)
)

renderPlot({
  ts_plot(tweets %>% filter(lang %in% input$tweetlang) , by = "hours", col = "blue")  +
    labs(x = NULL, y = NULL,
       title = "Frequency of tweets containing #Rstats",
       subtitle = paste0(format(min(tweets$created_at), "%d %B %Y"), " to ", format(max(tweets$created_at),"%d %B %Y")),
       caption = "Data collected from Twitter's REST API via rtweet") + 
    theme_minimal()
})

renderDataTable({
  tweets %>% 
    filter(lang %in% input$tweetlang) %>% 
    select(screen_name,text,lang,favorite_count,retweet_count)
})
```

Here, we will add a data table to give details about the content of the tweets in our data set. For showing data in an interactive data format, we can add the `renderDataTable()` function to use filters and searches in a user friendly way. Like in the `renderPlot()` we can use piping,`%>%`, and the filter with the input variable, `tweetlang`, so that the data contains only the languages specified by the user. Here the `select()` function specifies only a few of the 90 variables in the original data table.

Save the file, write a commit message and ***commit***.

Next, try to run the document with the *Run Document* button to see if you can use the input to alter the table to different languages.

## Tweets from @RLadiesSeattle

Next we will go to the next sub-header to create a new tab for us to build a data table:

`### Tweets from @RLadiesSeattle `

Add descriptive text such as:

```
The plots below show a wordle of terms, and a time series of tweets by @RLadiesSeattle. 
The time series can be plotted with different sources to see when different devices were used to tweet.
```

Then, add a sub-header:

`#### Time series plot of tweets`

Here we will add another chunk which we will call `user` with echo set to false, `{r user, echo=FALSE}`.

For the user, we will have multiple data displays, but will gather one input for the sake of creating interactive plots.

### Device inputPanel

In the user chunk, collect information using an `inputPanel` from the user with another multiple-select option. Instead of language, we will use the source of the tweet so we can see what device was used to write it. 
```
{r user, echo=FALSE}
inputPanel(
  selectInput("tweetsource", label = "Source of tweets:",
              choices = unique(user_timeline$source), selected = unique(user_timeline$source), multiple = TRUE)
)
```
Like before, we have choices available based on what is present in the data set, and as a default, we will visualize all select sources with the ability of the user to select multiple and storing these as a variable `input$tweetsource` so that we can retrieve this for the plots below. 

Save the file, write a commit message and ***commit***.

Click the *Run Document* button to see if the data input panel appears.

### User tweets renderPlot

Continue to work in the `user` chunk and specify the plot as a time-series of the number of tweets over time. Since this user has fewer tweets, we can extend the time to months. 

```
{r user, echo=FALSE}
inputPanel(
  selectInput("tweetsource", label = "Source of tweets:",
              choices = unique(user_timeline$source), selected = unique(user_timeline$source), multiple = TRUE)
)

renderPlot({
  ts_plot(user_timeline %>% filter(source %in% input$tweetsource), by = "months", col = "blue") +
    labs(x = NULL, y = NULL,
       title = "Frequency of tweets from @RLadiesSeattle",
       subtitle = paste0(format(min(user_timeline$created_at), "%d %B %Y"), " to ", format(max(user_timeline$created_at),"%d %B %Y")),
       caption = "Data collected from Twitter's REST API via rtweet") + 
    theme_minimal()
})
```
The `renderPlot()` function will use the inputs to make a reacting set of graphs. Time-series plot function, `ts_plot`,  piping `%>%` and the `filter()` function will make it so only data from our selected tweet source is collected via `source` and the list of selected inputs `tweetsource`, which we specified in the `inputPanel`. Like before, 
we can add features to make the plot  cleaner.

Save the file, write a commit message and ***commit***.

Click the *Run Document* button to see if the data plot appears.


### User tweets renderDataTable

Next we provide a table full of details that can be easily filtered using our input or user actions for more information. To do this, we continue to add to the `user` chunk. 

```
{r user, echo=FALSE}
inputPanel(
  selectInput("tweetsource", label = "Source of tweets:",
              choices = unique(user_timeline$source), selected = unique(user_timeline$source), multiple = TRUE)
)

renderPlot({
  ts_plot(user_timeline %>% filter(source %in% input$tweetsource), by = "months", col = "blue") +
    labs(x = NULL, y = NULL,
       title = "Frequency of tweets from @RLadiesSeattle",
       subtitle = paste0(format(min(tweets$created_at), "%d %B %Y"), " to ", format(max(tweets$created_at),"%d %B %Y")),
       caption = "Data collected from Twitter's REST API via rtweet") + 
    theme_minimal()
})

renderDataTable({
  user_timeline %>% 
    filter(source %in% input$tweetsource) %>% 
    select(text, favorite_count, retweet_count, source) %>%
    arrange(desc(retweet_count))
})
```

Notice that this time we use the `user_timeline` data, use piping to filter to the input-selected sources, then select a few of the needed columns from the full data table, and then arrange these by the number of retweets in descending order.

Save the file, write a commit message and ***commit***.

Click the *Run Document* button to see if the data table of tweets appears.


### Wordle

A wordle is one visualization that is often used as a novelty but can be great for summarizing impressions.
We will make a world that will help summarize the tweets. 

Start by adding a sub-header to distinguish the section, `#### Wordle: A graphic of frequently used words`.

Next, create a chunk called `userwordle` and add a `renderPlot()` function.

```
{r userwordle , echo=FALSE}
renderPlot({
 words <- user_timeline %>%
  mutate(text = str_remove_all(text, "&amp;|&lt;|&gt;"),
         text = str_remove_all(text, "\\s?(f|ht)(tp)(s?)(://)([^\\.]*)[\\.|/](\\S*)"),
         text = str_remove_all(text, "[^\x01-\x7F]")) %>% 
  unnest_tokens(word, text, token = "tweets") %>%
  filter(!word %in% stop_words$word,
         !word %in% str_remove_all(stop_words$word, "'"),
         str_detect(word, "[a-z]"),
         !str_detect(word, "^#"),         
         !str_detect(word, "@\\S+")) %>%
  filter(source %in% input$tweetsource) %>%
  count(word, sort = TRUE)

words %>% 
  with(wordcloud(word, n, random.order = FALSE, max.words = 100, colors = "#F29545")) 
})
```

This set of code is beyond the scope of what we are learning in data visualization, but it uses regular expressions, *regex*, to extract words that can be standardized for counting without pesky username handles or hashtags that might alter our count. Notice we still have a filter function that uses `input$tweetsource` to make the information reactive. Once the word data is stored as an object `words` it can be called up as a wordle using the `wordcloud()` function.

Save the file, write a commit message and ***commit***.

Click the *Run Document* button to see if the wordle appears.

## Congratulations

Well done! You made an interactive data visualization using twitter data! We will discuss different ways to publish your work to the web but for now anyone who has your code can reproduce your visualizations!

Look over your work, make sure you are ready to push your changes, and then use Git to ***Push*** to GitHub Classroom.

# A simple data dashboard of IMDB movie ranking
Lesson author: Caleb Trujillo

## Learning Objectives
By the end of this assignment, you will have practiced:

* Scripted programming to process and filter data, develop workflows, perform statistical analysis, and build a data dashboard
* Applying data analytics principles and methods to work with and visualize authentic data.
* Using industry-standard approaches for project management and code development
* Designing engaging and informative visualizations using computational methods.
* Disseminating visualizations and research results in a webpage format such as a data dashboard.

## Introduction
For this assignment, you will learn to use data from IMDB to develop a dashboard that has multiple interactive data visualizations. 

For references to using R markdown with Shiny, I recommend the rstudio website [Introduction to interactive documents](https://shiny.rstudio.com/articles/interactive-docs.html). 
For a deeper dive into Shiny's capabilities, you can also watch a full 2.5 hour [video tutorial](https://shiny.rstudio.com/tutorial/) on app development, the LinkedIn Learning Course [Building Data Apps with R and Shiny: Essential Training](https://www.linkedin.com/learning-admin/content/courses/772334?account=67682169) or the full module [Interactive Documents](http://rmarkdown.rstudio.com/authoring_shiny.html).
You may also be interested in the [R Markdown cheat sheet](https://shiny.rstudio.com/articles/rm-cheatsheet.html). 

## Sources

This assignment was inspired by an article on Medium called [Exploring tweets in R](https://medium.com/@traffordDataLab/exploring-tweets-in-r-54f6011a193d) by [Trafford Data lab](https://medium.com/@traffordDataLab). 
Additional resources were gathered from [Create a Shiny app to search Twitter with rtweet and R](https://www.infoworld.com/article/3516150/create-a-shiny-app-to-search-twitter-with-rtweet-and-r.html) by [Sharon Machlis](https://www.infoworld.com/author/Sharon-Machlis/).

# Instructions

## Start 

### Don't copy, practice

Make sure to write out the code to get in the habit of understanding the grammar of the code. 
**Do not copy directly!** The power of learning code is the creative avenues it unlocks.

### Old recording using similar methods

I've recorded a video in class where I worked on an earlier version of this code with Twitter (not  IMDB) that build the dashboard for data analysis. 
It can be found on YouTube:
[https://youtu.be/U7jrdHFl6gA](https://youtu.be/U7jrdHFl6gA)

### Help
If you need help, remember there are lots of resources, including:

- Discord
- Canvas Community Q&A
- The internet
- Your professor

### Download IMDb data

For this assignment, you will have to download the data yourself.
To obtain the data through the IMDb webpage by downloading grom the [Non-Commerical Datasets](https://developer.imdb.com/non-commercial-datasets/).
Optional: For real-time updates, you may consider obtaining developer permission from IMDb to use their *[API](https://developer.imdb.com/documentation/?ref_=side_nav) directly and with large data queries.*

Before coding, download the titles of films using the "title basics" and title rating data. These will come as zipped data directories. 
Unzip each on to reveal `title.ratings.tsv` and `title.titles.tsv`

Move these files from your download folder to the Rproject directory you are using to complete this Github assignment
Insure that the `.gitignore` file contains the names of these files, so that git doesn't try to synchronize these large files.

## Creating a Rmd file for Shiny

To begin, load a Shiny document by selecting *New File* and then selecting **R Markdown...**. 
When prompted select **Shiny** and select **Shiny document**. 
Title this document ***IMDb Shiny***, add your name as author and then save the `.Rmd` file in the assignment folder. 
You will also find a new R script titled `imdb-shiny.Rmd`. 

Open the file and then use the *Run Document* button to load a sample the interactive data visualization.

You should be able to see two input widgets that allow you to alter the histogram being displayed. 
Play with these features and read the full document. By the end of this assignment you should be able to make a simple widget for some IMDb data.

At this point, save the file, write a commit message and ***commit***.

Next delete the previous code so you can write your own, save the file, write a commit message and ***commit***.

### Setup chunk

We will update the library to use some of the unique libraries that will help us to work with IMDb data and manipulating text data. 

Update the setup chunk to load packages as shown below.

```
{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
pacman::p_load(tidyverse, stringr)
```

You may not have these packages installed, so you will want to use the `install.package()` for the missing data set in the *console* to get the packages needed for this assignment.

Save the file, write a commit message and ***commit***.

### Introduction

Underneath the setup chunk, replace the text with the following header and text.

```
## Introduction

This R Markdown document is made interactive using Shiny to demonstrate my abilities to make interactive data visualizations.
```

Save the file, write a commit message and ***commit***.

### Load data chunk

Create a new chunk under your replaced text called `{r load data, include = FALSE}`

Write commands to load the two data files using the `read_tsv()` commands that are stored as `title` and `ratings` respectively.

In the console:

- `title <- read_tsv('title.basics.tsv')`

- `ratings <- read_tsv('title.ratings.tsv')`

Next, merge these data sets and filter the sample to keep the films with more than 1000 raters.
Use the function `merge()`, a convenient join function, to combine the ratings and titles.
Finally, to do some simple pre-processing, reformat the dates for all films.


```
film <- title %>%
  merge(ratings) %>%
  filter(numVotes > 100) %>%
  mutate(startYear = as.Date(as.character(startYear), format = "%Y"),
         endYear= as.Date(as.character(endYear), format = "%Y"))
```

This will reduce the data set from 1.5 million entries to about 95,000.

After successfully running the script to load the stored data, you should be able to see a `film` data table in the environment tab. 

Add commands `rm(ratings,title)` and `gc()` to remove the large objects from your database and remove unused objects.

Save the file, write a commit message and ***commit***.

### Create two tabs for organizing the data.

After your load data chunks, clear the remaining text, headers, and code chunks so you can have a blank space to add more.

Add headers and a subheader as follows:

```
## IMDb data{.tabset}

### Top movies over time

```

You may want to change the name of the headers if using a different hashtag or user that you want to display.  
The `{.tabset}` will enable your webpage to have the subordinate tabs as headers. 

Save the file, write a commit message and ***commit***.

## Top Movies 

### inputPanel

Under the first sub-header, `Top movies by year` we will add some text to describe what the visual will show.

`The plot below shows time series of movies with an average rating higher than an 8 by more than 1,000 raters.`

Next, create a new chunk, titled `{r top movies, echo=FALSE}`. We will use this to add components of a shiny app to make the document interactive. 
Inside the chunk add an input panel which will allow you to collect information from your user. 
In this case we can ask the user to select a time frame of films to include in the visual.

```
{r type, echo=FALSE}
inputPanel(

  selectInput("filmtype", label = "Type of film:",
              choices = unique(film$titleType), selected = "movie", multiple = TRUE)
)
```

The input panel allows users to select from the choices from a menu of all options found in the IMDb data sample. 
As a default, movies `"movie"` is selected. The label prompts users what to select and stores the value as an object that can be called by `film$type` 

Save the file, write a commit message and ***commit***.

Next, try to run the document using the *Run Document* button to see if you can use the input to find different film types.

### renderPlot

The `renderPlot()` function will use the inputs to make a graph that can react to the information being changed. 
First specify that time-series plot function with ggplot and pipe in the filtered data. 
Here we use piping `%>%` and the `filter()` function. 
The filter allows that only data that is include are the film that have a type, `titleType`, in the list of selected inputs `filmtype`, which we specified in the `inputPanel`.  
We can also add some additional features to make the plot look cleaner and have additional declarative information such as the title, source, and details.

Continue to work in the `type` chunk and add code to render a plot so your code looks as follows:

````
```{r type, echo=FALSE}
inputPanel(
  selectInput("filmtype", label = "Type of film:",
              choices = unique(film$titleType), 
              selected = "movie", 
              multiple = TRUE)
)

renderPlot({
  film %>% 
    filter(titleType %in% input$filmtype, averageRating >=8, numVotes >=1000) %>%
    ggplot(aes(x = startYear, y = averageRating, size = numVotes, color = titleType)) +
    geom_point()+
      labs(x = NULL, y = NULL,
         title = "Ratings with an 8 or higher",
         caption = "Source: IMDb") + 
      theme_minimal()
})
```
````

Note that we can add R objects into the titles and subtitles so that they update as the user's inputs are added.

Save the file, write a commit message and ***commit***.

Next, try to run the document using the *Run Document* button to see if you can use the input to graph different languages.


### Add a slider bar for date.

Lets repeat these same steps by adding a date input using a slider.
This will help users by not overwhelming them too quickly.

```
inputPanel(
  selectInput(...),
  sliderInput("daterange", 
              label = "Date range:",
              min = 1890, 
              max = 2025,
              value = 
                c(2019, 2025),
          sep = "")
  )
```            

Notice that we can set the start of the slider at 2019-2025. 
Notice this input has two values.
For the slider tool, we can set the upper and lower limits based on the maximum and minimum years.
After setting the range, we can call this input "datarange"" so that we can call it using `input$datarange`.

Now, lets update the renderPlot function with the new filters that also behave like dates.

````
```{r type, echo=FALSE}
inputPanel(...)

renderPlot({
  film %>%
    filter(titleType %in% input$filmtype) %>%
    filter(averageRating >=8 &
             numVotes >=1000 &
             startYear >=
               as.Date(as.character(input$daterange[1]),format = "%Y") &
             startYear <=
               as.Date(as.character(input$daterange[2]),format = "%Y")
           ) %>%
    ggplot(aes(x = startYear, y = averageRating, size = numVotes, color = titleType)) +
    geom_point()+
      labs(
         title = "Ratings with an 8 or higher",
         caption = "Source: IMDb") + 
      theme_minimal()
})
```
````

Now that the dashboard is interactive and functional for the time slider, lets make a checkpoint to return back to as we go deeper.

Save the file, write a commit message and ***commit***.


### Stylistic touches

Notice that the dashboard will update as we change user selected dates and film types.
However, The dashboard is not very pleasing to look at.
Lets imagine what we could add. 
Clear labels, a descriptive subtitle, clear units in the legend, and removing distracting labels could help clarify the graph.

Continue to work in the `type` chunk and add code to work on the deeper layers of ggplot so your code looks as follows:

````
```{r type, echo=FALSE}
inputPanel(...)

renderPlot({
  film %>%
    filter(...) %>%
    filter(...) %>%
    ggplot(...) +
    geom_point()+
      labs(x = NULL, y = NULL,
         title = "Ratings with an 8 or higher",
         subtitle = paste0("Date range: ",
                           input$daterange[1],
                           " to ",
                           input$daterange[2],"."
                           ),
         caption = "Source: IMDb") +
    scale_x_date()+
    scale_size_continuous(labels = scales::unit_format(
      unit = "k", 
      scale = 1e-3,
      accuracy = 1, 
      sep = ""
      #round = 4
      ), name = 'Votes') +
    scale_color_discrete(name = 'Media') +
      theme_minimal()+
    theme(
      legend.position = "top"
    )
})
```
````

Adjusting the labels, scales, and theme layers of ggplot allow for a fluid look.

Finally, lets add some labels for the top most films each year.
To do so, we need to remap the aesthetics layer.

```
  film %>%
    filter(...) %>%
    filter(...) %>%
    ggplot(aes(x = startYear, y = averageRating)) +
      geom_point(aes(size = numVotes, color = titleType))+
      geom_text(data = .%>% group_by(startYear) %>% slice_max(order_by = numVotes, n=3), 
                mapping = aes(x= startYear, y = averageRating, label = originalTitle, size = numVotes), check_overlap = TRUE, position = position_dodge(0.9)
              )+...
```

This will provide a unique approach to splice to label the most popular films.
Instead of using `numVotes`, this could easily be adapted to the `averageRating`.
Notice that when the user replaces movies with video games in the selection, the labels contain video games.

Save the file, write a commit message and ***commit***.

### Add descriptions and clean Up

Add a second subheader. 

```
### Description
```
Add text under this header to describe, what you accomplished and why it matters. 

Delete any remaining headers, text or code.

Save the file, write a commit message and ***commit***.

## Congratulations

Well done! You made an interactive data visualization using IMDb data! 
We will discuss different ways to publish your work to the web but for now anyone who has your code can reproduce your visualizations!

If you want to keep practicing, try to add additional inputs, build a second render plot or a render table in a different subheader.

Look over your work, make sure you are ready to push your changes, and then use Git to ***Push*** to GitHub Classroom.

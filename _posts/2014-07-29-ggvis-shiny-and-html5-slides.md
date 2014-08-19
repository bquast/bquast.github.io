---
layout: post
title: ggvis, shiny, and HTML5 slides
permalink: ggvis-shiny-html5-slides
---
[ggvis](http://ggvis.rstudio.com) is wonderful new tool to create interactive graphics, which was build with [Shiny apps](http://shiny.rstudio.com/) in mind. In this post I will go over how you can create a Shiny app using ggvis and incorporating the 'app' in an rmarkdown slideshow (interactively).

[Sepal-Modeling](http://bquast.shinyapps.io/Iris-App) is a **shiny app** ([repo](https://github.com/bquast/Iris-App)),
which uses **ggvis** to fit LOESS smoothers on the sepal ratios of the **iris** dataset.
There are separate smoothers  for every species, as well as a general smoother for all observations.
The span can be adjusted in order to see if we need to model the sepal ratio per species or if we can just model it jointly.

The [HTML5 **dynamic** presentation](http://bquast.shinyapps.io/Iris-Presentation) ([code](https://github.com/bquast/Iris-Presentation/blob/master/dynamic.Rmd) )
and a [HTML5 **static** presentation](http://bquast.github.io/Iris-Presentation) ([code](https://github.com/bquast/Iris-Presentation/blob/master/static.Rmd)) are both build using **rmarkdown v2**.
The code in the presentation is most straight forward, it uses only ggvis code for the interactivity.


{% highlight r linenos %}
# load the ggvis package
library(ggvis)

# load the dplyr package
# for use of magrittr pipe: %>%
library(dplyr)

# lazyload the dataset and pass it to
iris %>%
  # the ggvis function, now set the axis
  ggvis(~Sepal.Width, ~Sepal.Length) %>%
  # add the smoother for all points
  layer_smooths(span = input_slider(0.5, 1, 0.5,
                                    label = 'General Model'),
                se = TRUE) %>%
  # now group by species
  group_by(Species) %>%
  # add points per species and colour them
  layer_points(size := input_slider(10, 200, 50,
                                    label = 'Point Size'),
               opacity := input_slider(0.5, 1, 0.5,
                                       label = 'Point Opacity'),
               fill = ~Species) %>%
  # now add smoothers per species and colour them
  layer_smooths(span = input_slider(0.5, 1, 0.5,
                                    label = 'Species Models'),
                fill = ~Species, se=TRUE) %>%
  # add new labels
  add_axis("x", title = "Sepal Width") %>%
  add_axis("y", title = "Sepal Length") %>%
  # finally declare as shiny object
  bind_shiny("ggvis", "ggvis_ui")
{% endhighlight %}



{% highlight text %}
## Error: bind_shiny() must be run inside a shiny app.
{% endhighlight %}

As you can see, running the code through knitr produces an error message. If we want to correctly parse the slidy graph in slides, we need to specify the **runtime** in the **YAML** front matter as such:

```
---
title: 'Iris: Sepal-Ratio Modeling'
author: "Bastiaan Quast"
date: "Thursday, July 18, 2014"
output:
  ioslides_presentation:
    keep_md: yes
    transition: faster
runtime: shiny
---
```

The standalone shiny app ([repository](https://github.com/bquast/Iris-App)) is slightly more complex in the sense that the input widgets are created using shiny code (which is actually very similar).

The **ui.R** looks like this:


{% highlight r linenos %}
# ui.R
# Bastiaan Quast
# bquast@gmail.com

library(shiny)
library(ggvis)

# Define UI for application that draws a histogram
shinyUI(fluidPage(

  # Application title
  titlePanel("Iris: Sepal-Ratio Modeling"),

  # Sidebar layout
  sidebarLayout(

    # Sidebar panel
    sidebarPanel(
      h3('Controls'),
      sliderInput('general.span',
                  'General Model',
                  0.5, 1, value=0.5),
      sliderInput('species.span',
                  'Individual Models',
                  0.5, 1, value = 0.5),
      sliderInput("size", "Point Size", 10, 200, value = 50),
      sliderInput('opacity', "Point Opacity", 0, 1, value = 0.5),
      p('The controls can be used to adjust
        the wigglyness of the general model,
        as well as the models fitted for each species.
        Thereby seeing if separate models are needed.
        Furthermore, the size and opacity of the points plotted
        can be adjusted to most effectively communicate the results
        of the model comparison.')
    ),

    # Main panel
    mainPanel(
      h3('We want to model sepal ratios,
         do we need separate models for each species?'),
      uiOutput("ggvis_ui"),
      ggvisOutput("ggvis"),
      p('Sepal width and sepal length are compared
        for three different species of flower.
        A model is fitted through all observations,
        as well a three separate models each species.
        The standard error is displayed as a coloured band
        around the models,
        whereby the grey band is represents
        the model fitted on all observations,
        and the coloured bands represent
        the models fitten on the respective species.
        ')
    )
  ))
)
{% endhighlight %}

and in the **server.R** we have:


{% highlight r linenos %}
# server.R
# Bastiaan Quast
# bquast@gmail.com

# load shiny package
library(shiny)

# load other packages
library(ggvis)

# interactive server code
shinyServer(
  function(input, output) {

    # A reactive expression wrapper for input$size
    input_general.span <- reactive(input$general.span)
    input_species.span <- reactive(input$species.span)
    input_size <- reactive(input$size)
    input_opacity <- reactive(input$opacity)

    iris %>%
      ggvis(~Sepal.Width, ~Sepal.Length) %>%
      layer_smooths(span = input_general.span, se = TRUE) %>%
      group_by(Species) %>%
      layer_points(size := input_size,
                   opacity := input_opacity,
                   fill = ~Species) %>%
      layer_smooths(span = input_species.span,
                    fill = ~Species,
                    se=TRUE) %>%
      add_axis("x", title = "Sepal Width") %>%
      add_axis("y", title = "Sepal Length") %>%
      bind_shiny("ggvis", "ggvis_ui"
            )

    })
{% endhighlight %}

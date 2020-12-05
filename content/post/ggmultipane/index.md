---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Visualizing Multiple Graphs in one Figure in R"
subtitle: ""
summary: ""
authors: []
tags: []
categories: []
date: 2020-12-02T12:16:32-05:00
lastmod: 2020-12-02T12:16:32-05:00
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---


Visualizing data can be difficult when you are trying to present multiple variables in one graph. Outlined below are multiple methods that might be useful in displaying your data. In all these examples, we will use the penguins dataset in the palmerpenguins package. Instructions to download this package can be found [here](https://allisonhorst.github.io/palmerpenguins/). 

##### Load in Dataset 

This package includes two datasets but we will use "penguins", see ?penguins for more information. Before using this data in figures, we omitted any rows with one or more cells using the na.omit() function. 

```{r dataset, warning=F}
library(palmerpenguins)
data("penguins")
head(penguins)
penguins <- na.omit(penguins) 
```

## Multi-panel plots using base R

First, let's look at the relationship between bill length and bill depth in penguins. 
```{r pressure, echo=F}
plot(penguins$bill_length_mm, penguins$bill_depth_mm, 
     col= penguins$species,
     pch=16,
     xlab="Bill Length (mm)",
     ylab="Bill Depth (mm)",
     main="A: Bill Length by Bill Depth ")
```
{{< figure library="true" src="lengthvdepth.png" >}}

However, in addition to this, we want to visually display information about the distribution of our x-variable (bill length) and y-variable (bill depth) using boxplots. 

```{r box, echo=F}
a<- boxplot(penguins$bill_depth_mm ~ penguins$species,
        xlab="Species",
        ylab="Bill Depth (mm)",
        main="B: Bill Depth by species")
b<- boxplot(penguins$bill_length_mm ~ penguins$species,
        xlab="Species",
        ylab="Bill Length (mm)",
        main="C: Bill Length by species")
box <- rbind(a,b)
```
{{< figure library="true" src="Rplot.png" >}}

Using the par(mfrow=) parameter or layout() functions, we can adjust the plotting space to fit multiple plots in base R.

#### par(mfrow) parameter

Using par(mfrow=), you may get less control over the proportions of the plotting space: 

```{r option, echo=T}
parameter <- par(mfrow=c(1,3)) 
parameter <- plot(penguins$bill_length_mm, penguins$bill_depth_mm, 
     col= penguins$species,
     pch=16,
     xlab="Bill Length (mm)",
     ylab="Bill Depth (mm)",
     main="A: Bill Length by Bill Depth ")
parameter <- boxplot(penguins$bill_depth_mm ~ penguins$species,
        xlab="Species",
        ylab="Bill Depth (mm)",
        main="B: Bill Depth by species")
parameter <- boxplot(penguins$bill_length_mm ~ penguins$species,
        xlab="Species",
        ylab="Bill Length (mm)",
        main="C: Bill Length by species")
```
{{< figure library="true" src="parameter.png" >}}

#### layout() function 

The layout() function can give us more control over the plotting space. For example: 

```{r layout, echo=T}
layout(matrix(c(1,1,2,3), 2, 2, byrow = T)) 
layout.show(n=3) 
```
{{< figure library="true" src="layoutshow.png" >}}

Using the layout() function, we can plot the same graphs in an adjusted plot space: 

```{r layoutfinal, echo=T}
layout <- layout(matrix(c(1,1,2,3), 2, 2, byrow = F)) 
layout <- plot(penguins$bill_length_mm, penguins$bill_depth_mm, 
     col= penguins$species,
     pch=16,
     xlab="Bill Depth (mm)",
     ylab="Bill Length (mm)",
     main="A: Bill Length by Bill Depth ")
layout <- boxplot(penguins$bill_depth_mm ~ penguins$species,
        xlab="Species",
        ylab="Bill Depth (mm)",
        main="B: Bill Depth by species")
layout <- boxplot(penguins$bill_length_mm ~ penguins$species,
        xlab="Species",
        ylab="Bill Length (mm)",
        main="C: Bill Length by species")
layout <- mtext("Bill Length and Bill Depth by Species", side=3, outer=T, line=-1)
```
{{< figure library="true" src="layout.png" >}}


### Marginal Plots

Another way of displaying the relationship between bill length and bill depth AND information about the distributions of bill length and bill depth together is using ggExtra:ggMarginal. Using this method, we see the the marginal distributions of the x- and y- axis within the scatterplot. You do lose some information in this marginal plot, as it only gives you the boxplots of cumulative bill depth and bill length whereas the previous two plots can separate the boxplot by each species. 

```{r marginal, echo=T, warning=F}
library(ggplot2) #for creating figures 
library(ggExtra) #for building graphs into the margins
marginal <- ggplot(penguins, aes(x=bill_length_mm, y=bill_depth_mm, color=species))+
  geom_point(position="jitter")+
  theme(legend.position = "bottom")+
  labs(title= "Bill Length by Bill Depth with Boxplots", x="Bill Length (mm)", y="Bill Depth (mm)")
marginal<- ggMarginal(marginal, type="boxplot")
marginal
```
{{< figure library="true" src="marginal.png" >}}


## Multi-panel plots using ggplot2::facet_wrap

Now, we want to create figures to visualize number of penguins in the dataset by sex(2: Male and Female) and species(3: Adelie, Chinstrap, and Gentoo). Plotting all of this information in one graph can be done but it may be hard to read: 

```{r gg, echo=F, warning=F}
library(ggplot2) #for creating figures 
library(ggthemes) #for figure themes
library(RColorBrewer) #for multiple color palettes
peng <- ggplot(penguins, aes(x=species, fill=sex))+ 
  geom_bar(position="dodge") +
  scale_fill_brewer(palette="Pastel1")+ 
  theme_classic()+
  labs(title="Frequency of Species Separated by sex", #change the title of the figure
       x="Species", #change the label of the x axis
       y="Frequency", #change the label of the y axis
       fill="Penguin Sex") #change the title of the legend
peng 
```
{{< figure library="true" src="peng.png" >}}

More information about how to use the RColorBrewer package can be found [here](https://www.datanovia.com/en/blog/the-a-z-of-rcolorbrewer-palette/#:~:text=Display%20all%20brewer%20palettes,-To%20display%20all&text=The%20palettes%20names%20are%20%3A%20Blues,YlGn%2C%20YlGnBu%20YlOrBr%2C%20YlOrRd.). Further information on how to use color to your advantage in ggplots, see [here](http://rstudio-pubs-static.s3.amazonaws.com/5312_98fc1aba2d5740dd849a5ab797cc2c8d.html) and [here](https://drsimonj.svbtle.com/pretty-histograms-with-ggplot2).

A different method of displaying the count of penguins separated by sex and gender is by using ggplot2:facet_wrap function. This function will allow you to create multiple panels using information from one categorical variable. This figure might be a bit easier to read as the variables being organized can be visualized a bit clearer.

```{r facet, echo=T}
peng_facet <- ggplot(penguins, aes(x=species, fill=sex))+
  geom_bar(show.legend = F)+ #remove the legend from the graph
  facet_wrap(~sex) + #create panels differentiated by sex 
  scale_fill_brewer(palette="Pastel1")+ #altering colors of the figure 
  theme_classic()+
  labs(title="Frequency of Species Separated by sex", #change the title of the figure
       x="Species", #change the label of the x axis
       y="Frequency", #change the label of the y axis
       fill="Penguin Sex") #change the title of the legend
peng_facet
```
{{< figure library="true" src="peng_facet.png" >}}

As you can see, both these graphs display information with varying efficacy:

{{< figure library="true" src="facet.png">}}

## Multi-panel plots using ggpubr::ggarrange

Finally, we want to see how body mass (g) relates to bill length (mm), bill depth (mm), and flipper length (mm)) relate to each other.Using ggpubr::ggarrange, we can look at all these relationships side by side:

```{r arrange, echo=T, warning=F, message=F}
library(ggpubr)
plot1 <- ggplot(penguins,aes(x=body_mass_g, y=bill_depth_mm, color=species))+
  geom_point(shape=21, position="jitter")+
  geom_smooth(method=lm , color="black",se=T) +
  theme_clean()+
  theme(plot.title=element_text(size=9),
        axis.text.x = element_text(size=7),
      axis.text.y = element_text(size=7))+
  labs(title="Body Mass by Bill Depth", x="", y="Body Depth (mm)")
plot2 <- ggplot(penguins,aes(x=body_mass_g, y=bill_length_mm, color=species))+
  geom_point(shape=21, position="jitter")+
  geom_smooth(method=lm , color="black",se=T) +
  theme_clean()+  
  theme(plot.title=element_text(size=9),
        axis.text.x = element_text(size=7),
      axis.text.y = element_text(size=7))+
  labs(title="Body Mass by Bill Length", x="", y="Body Length (mm)")
plot3 <- ggplot(penguins,aes(x=body_mass_g, y=flipper_length_mm, color=species))+
  geom_point(shape=21, position="jitter")+
  geom_smooth(method=lm , color="black",se=T) +
  theme_clean()+  
  theme(plot.title=element_text(size=9),
        axis.text.x = element_text(size=7),
      axis.text.y = element_text(size=7))+
  labs(title="Body Mass by Flipper Length", x="", y="Flipper Length (mm)")
multi_plot<- ggarrange(plot1,plot2,plot3,
                       labels = c("A", "B", "C"),
                       ncol = 3, nrow = 1,
                       common.legend = T)
#add titles and labels to the multi-panel graph
multi_plot <- annotate_figure(multi_plot,
                                     top = text_grob("Measurements of Penguin Species", color = "black", face = "bold", size = 11),
                              bottom= text_grob("Body Mass (g)", color = "black"))
multi_plot 
```
{{< figure library="true" src="multiplot.png" >}}

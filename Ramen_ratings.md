---
title: "Ramen Ratings"
author: "Ifeoma Egbogah"
date: "6/10/2019"
output: 
  html_document: 
    keep_md: yes
editor_options: 
  chunk_output_type: console
---




```r
library(tidyverse)
```

```
## -- Attaching packages ------------------------------------------- tidyverse 1.2.1 --
```

```
## v ggplot2 3.1.1     v purrr   0.3.2
## v tibble  2.1.1     v dplyr   0.8.1
## v tidyr   0.8.3     v stringr 1.4.0
## v readr   1.3.1     v forcats 0.4.0
```

```
## -- Conflicts ---------------------------------------------- tidyverse_conflicts() --
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

```r
library(ggpubr)
```

```
## Loading required package: magrittr
```

```
## 
## Attaching package: 'magrittr'
```

```
## The following object is masked from 'package:purrr':
## 
##     set_names
```

```
## The following object is masked from 'package:tidyr':
## 
##     extract
```

```r
library(ggridges)
```

```
## 
## Attaching package: 'ggridges'
```

```
## The following object is masked from 'package:ggplot2':
## 
##     scale_discrete_manual
```

```r
library(RColorBrewer)
library(viridis)
```

```
## Loading required package: viridisLite
```

```r
library(patchwork)
library(countrycode)

theme_set(theme_light())

ramen_ratings <- readr::read_csv("https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2019/2019-06-04/ramen_ratings.csv")%>%
  mutate(brand = str_replace(brand, "Mama", "mama"),
         brand = str_replace(brand, "Nissin Miojo", "Nissin-Miojo"),
         brand = str_replace(brand, "MAMA", "mama"))
```

```
## Parsed with column specification:
## cols(
##   review_number = col_double(),
##   brand = col_character(),
##   variety = col_character(),
##   style = col_character(),
##   country = col_character(),
##   stars = col_double()
## )
```


###Curvature plot showing the country of origin for the top 20 ramen brands

```r
rate <- ramen_ratings%>%
  count(country = fct_lump(country, 15), brand = fct_lump(brand, 20), sort=TRUE)%>%
  mutate(colour = case_when(country == "Japan" ~ "red",
            country == "United States" ~ "blue",
            country == "South Korea" ~ "yellow",
            country == "Taiwan" ~ "green",
            country == "China" ~ "purple",
            country == "Thailand" ~ "hotpink",
            T ~ "grey60"))%>%
  ggplot(aes(x = 0,
             y = fct_rev(factor(country)),
             xend = brand,
             yend = 1, 
             colour= colour,
             alpha = (colour != "grey60")
             ))+ 
  geom_curve(aes(colour = colour), curvature = -0.3, arrow = arrow(length = unit(0.01, "npc")), show.legend = FALSE)+
  scale_x_discrete()+
  scale_y_discrete()+
  scale_color_identity()+
  scale_size_identity()+
  scale_alpha_manual(values = c(0.45, 0.45), guide = F)+
  scale_size_manual(values = c(0.5, 0.6), guide = F)+
  theme(axis.text.x = element_text(angle = 45, vjust = 1, hjust = 1))+
  labs(y= "Country",
       x= "Brand of Ramen",
       title = "Countries of Origin for the Top 20 Ramen Brands",
       caption = "Source: The Ramen Rater | Visualization: Ifeoma Egbogah")
```

```
## Scale for 'size' is already present. Adding another scale for 'size',
## which will replace the existing scale.
```

```r
rate
```

![](Ramen_ratings_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

```r
ggsave("rate.png", rate, width = 16, height =10)
```


###Extracting and creating a new column for the different varieties/flavour of ramen from the data

```r
ramen_ratings <- ramen_ratings%>%
  mutate(flavour = str_extract(variety, "Chicken"))%>%
  mutate(flavour2 = str_extract(variety, "Beef"))%>%
  mutate(flavour3 = str_extract(variety, "Shrimp"))%>%
  mutate(flavour1 = str_extract(variety, "Vegetable"))%>%
  mutate(flavour4 = str_extract(variety, "Onion"))%>%
  mutate(flavour5 = str_extract(variety, "Curry"))%>%
  mutate(flavour6 = str_extract(variety, "Pork"))%>%
  mutate(flavour7 = str_extract(variety, "BBQ"))%>%
  mutate(flavour8 = str_extract(variety, "Rice"))%>%
  mutate(flavour9 = str_extract(variety, "Chili"))%>%
  mutate(flavour11 = str_extract(variety, "Seafood"))%>%
  mutate(flavour12 = str_extract(variety, "Salt"))%>%
  mutate(flavour13 = str_extract(variety, "Sesame"))%>%
  mutate(flavour14 = str_extract(variety, "Soy"))%>%
  mutate(flavour15 = str_extract(variety, "Chill"))%>%
  mutate(flavour10 = str_extract(variety, "Spicy"))%>%
  mutate(flavour = coalesce(flavour, flavour2))%>%
  mutate(flavour = coalesce(flavour, flavour3))%>%
  mutate(flavour = coalesce(flavour, flavour1))%>%
  mutate(flavour = coalesce(flavour, flavour4))%>%
  mutate(flavour = coalesce(flavour, flavour5))%>%
  mutate(flavour = coalesce(flavour, flavour6))%>%
  mutate(flavour = coalesce(flavour, flavour7))%>%
  mutate(flavour = coalesce(flavour, flavour8))%>%
  mutate(flavour = coalesce(flavour, flavour9))%>%
  mutate(flavour = coalesce(flavour, flavour11))%>%
  mutate(flavour = coalesce(flavour, flavour12))%>%
  mutate(flavour = coalesce(flavour, flavour13))%>%
  mutate(flavour = coalesce(flavour, flavour14))%>%
  mutate(flavour = coalesce(flavour, flavour15))%>%
  mutate(flavour = coalesce(flavour, flavour10))
```


###Coord.polar plot of Ramen rating by brand, style, variety(flavour) and continent of origin

```r
ramen_ratings <- ramen_ratings%>%
   select(-flavour1,-flavour2,-flavour3,-flavour4,-flavour5,-flavour6,-flavour7,-flavour8,-flavour9,-flavour10,-flavour11,-flavour12,-flavour13,-flavour14, -flavour15)%>%
  mutate(country = str_replace(country, "Holland", "Netherlands"),
         country = str_replace(country, "Dubai", "UAE"),
         country = str_replace(country, "Phlippines", "Philippines"),
         country = str_replace(country, "Sarawak", "Malaysia"))%>%
  mutate(continent = countrycode(country, "country.name", "continent"))

ramen <- ramen_ratings%>%
  group_by(brand, continent)%>%
  summarise(stars = mean(stars))

ramen1 <- ramen%>%
  group_by(continent)%>%
  top_n(10, stars)%>%
  ggplot(aes(reorder(brand, stars), stars, fill = continent))+
  geom_col(width = 0.8, show.legend = FALSE)+
  coord_flip()+
  facet_wrap(~continent, scales = "free")+
  labs(x= "Ramen Brands",
       y = "Ramen Ratings",
       title= "Top 10 Ramen Brands In Each Continent Based On Ramen Ratings",
       subtitle = "Lots of Brands in Asia Have a Rating of 5",
       caption = "Source: The Ramen Rater | Visualization: Ifeoma Egbogah")

ramen1
```

![](Ramen_ratings_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

```r
nb.cols<-8
cols<-colorRampPalette(brewer.pal(8, "PiYG"))(nb.cols)

ramen2 <- ramen_ratings%>%
  filter(!is.na(stars), !is.na(style))%>%
  group_by(style, continent)%>%
  summarise(stars = mean(stars), num = n())%>%
  ggplot(aes(reorder(style, stars), stars))+
  geom_col(aes(fill = style), show.legend = FALSE)+
  facet_wrap(~continent)+
  coord_polar()+
  geom_text(aes(label = num))+
  scale_fill_manual(values = cols)+
  labs(x = "Style of Ramen",
       y = "Ramen Ratings",
       title = "Ratings of Ramen Styles For Each Continent")

ramen2
```

![](Ramen_ratings_files/figure-html/unnamed-chunk-4-2.png)<!-- -->

```r
nb.col<-16
col<-colorRampPalette(brewer.pal(16, "PiYG"))(nb.col)
```

```
## Warning in brewer.pal(16, "PiYG"): n too large, allowed maximum for palette PiYG is 11
## Returning the palette you asked for with that many colors
```

```r
ramen3 <- ramen_ratings%>%
  mutate(flavour = str_replace(flavour, "Chill", "Chili"))%>%
  filter(!is.na(flavour), !is.na(stars))%>%
  group_by(flavour, continent)%>%
  summarise(stars = mean(stars), num = n())%>%
  ggplot(aes(reorder(flavour, stars), stars))+
  geom_col(aes(fill = flavour), show.legend = FALSE)+
  facet_wrap(~continent)+
  coord_polar()+
  geom_text(aes(label = num))+
  scale_fill_manual(values = col)+
  labs(x = "Style of Ramen",
       y = "Ramen Ratings",
       title = "Ratings of Ramen Variety For Each Continent",
       subtitle = "Numbers are the Varieties Extracted from the Ramen Data",
       caption = "Source: The Ramen Rater | Visualization: Ifeoma Egbogah")

ramen3
```

![](Ramen_ratings_files/figure-html/unnamed-chunk-4-3.png)<!-- -->

```r
ggsave("ramen1.png", ramen1, width = 16, height =10)
ggsave("ramen2.png", ramen2, width = 16, height =10)
ggsave("ramen3.png", ramen3, width = 16, height =10)
```


###Distribution of Ramen ratings by country and style

```r
ramen4 <- ramen_ratings%>%
  filter(!is.na(style))%>%
  mutate(style = fct_reorder(style,stars))%>%
  ggplot(aes(stars, style, fill= style, colour = style))+
  geom_density_ridges_gradient(alpha= 0.8, 
                               show.legend = FALSE)+
  scale_fill_viridis_d(option = "E")+
  scale_color_viridis_d(option = "E")+
  labs(x= "Ramen Ratings",
       y= "Style of Ramen",
       title= "Distribution of Style Of Ramen",
       caption= "Source: The Ramen Rater | Visualization: Ifeoma Egbogah")

ramen4
```

```
## Picking joint bandwidth of 0.337
```

![](Ramen_ratings_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

```r
ramen5 <- ramen_ratings%>%
  mutate(country = fct_lump(country, 10))%>%
  mutate(country = fct_reorder(country, stars))%>%
  ggplot(aes(stars, country, fill = country, colour=country))+
  geom_density_ridges_gradient(alpha = 0.8, show.legend = FALSE)+
  scale_fill_viridis_d(option = "D")+
  scale_color_viridis_d(option = "D")+
  labs(y="Country",
       x= "Ramen Rating")

ramen5
```

```
## Picking joint bandwidth of 0.233
```

![](Ramen_ratings_files/figure-html/unnamed-chunk-5-2.png)<!-- -->

```r
ramen6 <- ramen_ratings%>%
  filter(!is.na(country), !is.na(stars))%>%
  mutate(country = fct_lump(country, 10))%>%
  mutate(country = fct_reorder(country, stars))%>%
  ggplot(aes(country, stars, fill = country))+
  geom_boxplot(alpha = 0.8, show.legend = FALSE)+
  scale_fill_viridis_d(option = "D")+
  coord_flip()+
  labs(x="Country",
       y= "Ramen Rating")

ramen6
```

![](Ramen_ratings_files/figure-html/unnamed-chunk-5-3.png)<!-- -->

```r
plots<- wrap_plots(ramen5, ramen6)+ plot_annotation(title= "Distribution of Ramen Ratings by Country",
  caption= "Source: The Ramen Rater | Visualization: Ifeoma Egbogah")

plots
```

```
## Picking joint bandwidth of 0.233
```

![](Ramen_ratings_files/figure-html/unnamed-chunk-5-4.png)<!-- -->

```r
ggsave("ramen4.png", ramen4, width = 16, height =10)
```

```
## Picking joint bandwidth of 0.337
```

```r
ggsave("plots.png", plots, width = 16, height =10)
```

```
## Picking joint bandwidth of 0.233
```


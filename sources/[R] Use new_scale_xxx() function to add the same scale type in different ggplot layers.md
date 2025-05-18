---
title: [R] Use new_scale_xxx() function to add the same scale type in different ggplot layers
---

# [R] Use new_scale_xxx() function to add the same scale type in different ggplot layers

url:: https://www.r-bloggers.com/2025/05/r-use-new_scale_xxx-function-to-add-the-same-scale-type-in-different-ggplot-layers/?utm_source=phpList&utm_medium=email&utm_campaign=R-bloggers-daily&utm_content=HTML
up: [[sources]]

#source

Zhenguo Zhang’s Blog /2025/05/10/r-use-new-scale-xxx-function-to-add-the-same-scale-type-in-different-ggplot-layers/ –

In one ggplot figure, normally you can only use one scale for each aesthetic mapping. For example, if you use `scale_color_manual()` to set the color scale for a layer, you cannot use another `scale_color_manual()` for another layer, or set the color scale more then once in the function `aes()`. However, you can use the `new_scale_color()` function from the `ggnewscale` package to add a new scale for the same aesthetic mapping in different layers.

In this post, I will showcase how to use the `new_scale_color()` function to add two different color scales in a ggplot figure. The first scale will be for a discrete variable (e.g., number of cylinders), and the second scale will be for a continuous variable (e.g., density level).

Load packages first.

```
library(ggplot2)
library(ggnewscale)
```

Use the mtcars dataset for the example

```
data(mtcars)
```

Create a plot with two color scales: 1. Points colored by ‘cyl’ (discrete) 2. Density contours colored by density level (continuous)

First, let’s make a scatter plot of `mpg` vs `wt` with points colored by the number of cylinders (`cyl`). We will use the `geom_point()` function for this layer.

```
plt <- ggplot(mtcars, aes(x = wt, y = mpg)) +
  # First layer: Scatter plot colored by cylinders (discrete variable)
  geom_point(aes(color = factor(cyl)), size = 3) +  
  scale_color_discrete(name = "Cylinders")

plt
```

![](https://i2.wp.com/fortune9.netlify.app/2025/05/10/r-use-new-scale-xxx-function-to-add-the-same-scale-type-in-different-ggplot-layers/index_files/figure-html/unnamed-chunk-3-1.png?w=450&ssl=1)

Set new scale for the next layer

```
# Reset the color scale for the next layer
plt <- plt + new_scale_color()
```

Add a second layer: Density contours colored by density level (continuous variable)

```
plt <- plt +
  geom_density_2d(aes(color = after_stat(level))) +  
  scale_color_viridis_c(name = "Density Level", option = "magma") +
  
  # Add labels and theme
  labs(title = "Dual Color Scales with new_scale_color()",
       x = "Weight (1000 lbs)",
       y = "Miles per Gallon") +
  theme_minimal()

plt
```

![](https://i2.wp.com/fortune9.netlify.app/2025/05/10/r-use-new-scale-xxx-function-to-add-the-same-scale-type-in-different-ggplot-layers/index_files/figure-html/unnamed-chunk-4-1.png?w=450&ssl=1)

Here I demonstrated how to use the `new_scale_color()` function from the `ggnewscale` package, one can also use `new_scale_fill()` for fill aesthetics. For other aesthetics, such as `size`, `shape`, etc., you can call `new_scale("size")`, `new_scale("shape")`, etc. to add new scales.

To learn more, check the webpage <https://eliocamp.github.io/ggnewscale/>

Happy programming 😃

- /2025/05/10/r-use-new-scale-xxx-function-to-add-the-same-scale-type-in-different-ggplot-layers/ -

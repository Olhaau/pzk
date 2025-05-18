---
title: Repost: uv, part 3: Python in R with reticulate
---

# Repost: uv, part 3: Python in R with reticulate

url:: https://www.r-bloggers.com/2025/05/repost-uv-part-3-python-in-r-with-reticulate/?utm_source=phpList&utm_medium=email&utm_campaign=R-bloggers-daily&utm_content=HTML
up: [[sources]]

#source

[This article was first published on
**[Getting Genetics Done](https://gettinggeneticsdone.blogspot.com/2025/05/uv-part-3-python-in-r-with-reticulate.html)**

, and kindly contributed to
[R-bloggers](https://www.r-bloggers.com/)

]. (You can report issue about the content on this page
[here](https://www.r-bloggers.com/contact-us/)

)

---

Want to share your content on R-bloggers?
[click here](https://www.r-bloggers.com/add-your-blog/)

if you have a blog, or
[here](http://r-posts.com/)

if you don't.

Reposted from the original at <https://blog.stephenturner.us/p/uv-part-3-python-in-r-with-reticulate>.

Two demos using Python in R via reticulate+uv: (1) Hugging Face transformers for sentiment analysis, (2) pyBigWig to query a BigWig file and visualize with ggplot2.

—

*This is part 3 of a series on uv. Other posts in this series:*

1. *[uv, part 1: running scripts and tools](https://blog.stephenturner.us/p/uv-part-1-running-scripts-and-tools)*

2. *[uv, part 2: building and publishing packages](https://blog.stephenturner.us/p/uv-part-2-building-and-publishing-packages)*

3. *This post*

4. *Coming soon…*

I get the same question all the time from up and coming data scientists in training: “should I use Python or R?” My answer is always the same: it’s not Python *versus* R, it’s python *and* R — use whatever tool is best for the job. Last year I wrote a post with resources for learning Python as an R user.  
[](https://blog.stephenturner.us/p/python-for-r-users)  
[![Python for R users](https://i1.wp.com/substackcdn.com/image/fetch/w_140,h_140,c_fill,f_auto,q_auto:good,fl_progressive:steep,g_auto/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F2049d794-5c0e-4bde-a0a9-f51d86413057_1248x920.png?resize=140%2C140&ssl=1)](https://blog.stephenturner.us/p/python-for-r-users)

“The best tool for the job” might require multilingual data science. I’m partial to R for data manipulation, visualization, and bioinformatics, but Python has a far bigger user base, and best to not reinvent the wheel if a well-tested and actively developed Python tool already exists.

If I’m doing 90% of my analysis in an R environment but I have some Python code that I want to use, **[reticulate](https://rstudio.github.io/reticulate/)** makes it easy to use Python code within R (from a script, in a RMarkdown/Quarto document, or in packages). This helps you avoid switching contexts and exporting data between R and Python.

You can import a Python package, and call a Python function from that package inside your R environment. Here’s a simple demo using the `listdir()` function in the `os` package in the Python standard library.

```
library(reticulate)
os <- import("os")
os$listdir(".")
```

[Posit recently released reticulate 1.41](https://posit.co/blog/reticulate-1-41/) which simplifies Python installation and package management by using uv on the back end. There’s one simple function: `py_require()` which allows you to declare Python requirements for your R session. Reticulate creates an ephemeral Python environment using uv. See the [function reference](https://rstudio.github.io/reticulate/reference/py_require.html) for details.

Here’s a demo. I’ll walk through how to use Hugging Face models from Python directly in R using `reticulate`, allowing you to bring modern NLP to your tidyverse workflows with minimal hassle. The code I’m using is [here as a GitHub gist](https://gist.github.com/stephenturner/2e7ee7443645b7048aa8338d79c55d04).

[See the code on GitHub](https://gist.github.com/stephenturner/2e7ee7443645b7048aa8338d79c55d04)

R has great tools for text wrangling and visualization (hello `tidytext`, `stringr`, and `ggplot2`), but imagine we want access to [Hugging Face’s transformers library](https://huggingface.co/docs/transformers/en/index), which provides hundreds of pretrained models, simple pipeline APIs for things like sentiment analysis, named entity recognition, translation, or summarization. Let’s try running sentiment analysis with the [Hugging Face transformers sentiment analysis pipeline](https://huggingface.co/blog/sentiment-analysis-python).

First, load the reticulate library and use `py_require()` to declare that we’ll need PyTorch and the Hugging Face [transformers library](https://pypi.org/project/transformers/) installed.

```
library(reticulate)
py_require("torch")
py_require("transformers")
```

Even after clearing my uv cache this installs in no time on my MacBook Pro.

```
Installed 23 packages in 411ms
```

Next, I’ll import the Python transformers library into my R environment, and create a object to use the sentiment analysis pipeline. You’ll get a message about the fact that you didn’t specify a model so it defaults to a [DistilBERT model](https://huggingface.co/distilbert/distilbert-base-uncased-finetuned-sst-2-english) fine tuned on the [Stanford Sentiment Treebank](https://huggingface.co/datasets/stanfordnlp/sst2) corpora.

```
transformers <- import("transformers")
analyzer <- transformers$pipeline("sentiment-analysis")
```

We can now use this function from a Python library as if it were an R function.

```
analyzer("It was the best of times")
```

The result is a nested list.

```
[[1]]
[[1]]$label
[1] "POSITIVE"

[[1]]$score
[1] 0.9995624
```

How about another?

```
analyzer("It was the worst of times")
```

Results:

```
[[1]]
[[1]]$label
[1] "NEGATIVE"

[[1]]$score
[1] 0.9997889
```

Let’s write an R function that gives us prettier output. This will take text and output a data frame indicating the overall sentiment and the score.

```
analyze_sentiment <- function(text) {
  result <- analyzer(text)[[1]]
  tibble(label = result$label, score = result$score)
}
```

Let’s try it out on a longer passage.

```
analyze_sentiment("it was the age of wisdom, it was the age of foolishness, it was the epoch of belief, it was the epoch of incredulity, it was the season of Light, it was the season of Darkness, it was the spring of hope, it was the winter of despair")
```

The results:

```
     label     score
1 NEGATIVE 0.5121167
```

Now, let’s create several text snippets:

```
mytexts <- c("I love using R and Python together!",
             "This is the worst API I've ever worked with.",
             "Results are fine, but the code is a mess",
             "This package manager is super fast.")
```

And using standard tidyverse tooling, we can create a table showing the sentiment classification and score for each of them:

```
library(dplyr)
library(tidyr)
tibble(text=mytexts) |>
  mutate(sentiment = lapply(text, analyze_sentiment)) |>
  unnest_wider(sentiment)
```

The result:

```
# A tibble: 4 × 3
  text                                         label    score
  <chr>                                        <chr>    <dbl>
1 I love using R and Python together!          POSITIVE 1.00 
2 This is the worst API I've ever worked with. NEGATIVE 1.00 
3 Results are fine, but the code is a mess     NEGATIVE 0.999
4 This package manager is super fast.          POSITIVE 0.995
```

This example demonstrates using [pyBigWig](https://github.com/deeptools/pyBigWig) to query a [BigWig file](https://genome.ucsc.edu/goldenpath/help/bigWig.html) in R for downstream visualization with ggplot2. All the code is [here as a GitHub Gist](https://gist.github.com/stephenturner/2e7ee7443645b7048aa8338d79c55d04).

[See the code on GitHub](https://gist.github.com/stephenturner/2e7ee7443645b7048aa8338d79c55d04)

First, let’s get [this example BigWig file](http://genome.ucsc.edu/goldenPath/help/examples/bigWigExample.bw):

```
x <- "http://genome.ucsc.edu/goldenPath/help/examples/bigWigExample.bw"
download.file(x, destfile = "bigWigExample.bw", mode = "wb")
```

Now let’s load reticulate and use the pyBigWig library:

```
library(reticulate)
py_require("pyBigWig")
pybw <- import("pyBigWig")
```

Now let’s open that example file, look at the chromosomes and their lengths, then query values near the end of chromosome 21.

```
# Open a BigWig file
bw <- pybw$open("bigWigExample.bw")

# Get list of chromosomes
chroms <- bw$chroms()
print(chroms)

# Query values near the end of chromosome 21
chrom <- "chr21"
start <- chroms[[1]]-100000L
end <- start+1000L

# Get values (one per base)
values <- bw$values(chrom, start, end)

# Close the file
bw$close()
```

Finally, we can put the results into a data frame and plot it with ggplot2:

```
# Wrap into data frame
df <- data.frame(position = start:(end - 1), 
                 signal = unlist(values))

# Plot the result
library(ggplot2)
ggplot(df, aes(x = position, y = signal)) +
  geom_line(color = "steelblue") +
  theme_minimal() +
  labs(title = paste("Signal at", chrom, "from", start, "to", end),
       x = "Genomic position",
       y = "Signal")
```

Here’s the resulting plot:  
[![](https://i2.wp.com/substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F01161181-a879-4c6a-840f-333813e0cdcd_800x570.png?resize=450%2C570&ssl=1)](https://substackcdn.com/image/fetch/f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2F01161181-a879-4c6a-840f-333813e0cdcd_800x570.png)

---
created: 2025-04-23
summary:
---

# Batch processing (experimental) 

## content

Sometimes you want to run R code on the entire Dataset, but that Dataset is much larger than memory. You can use map_batches on a Dataset query to process it batch-by-batch.

Note: map_batches is experimental and not recommended for production use.

As an example, to randomly sample a Dataset, use map_batches to sample a percentage of rows from each batch:

```r
sampled_data <- ds %>%
  filter(year == 2015) %>%
  select(tip_amount, total_amount, passenger_count) %>%
  map_batches(~ as_record_batch(sample_frac(as.data.frame(.), 1e-4))) %>%
  mutate(tip_pct = tip_amount / total_amount) %>%
  collect()

str(sampled_data)
```

```r
## 
## tibble [10,918 <U+00D7> 4] (S3: tbl_df/tbl/data.frame)
##  $ tip_amount     : num [1:10918] 3 0 4 1 1 6 0 1.35 0 5.9 ...
##  $ total_amount   : num [1:10918] 18.8 13.3 20.3 15.8 13.3 ...
##  $ passenger_count: int [1:10918] 3 2 1 1 1 1 1 1 1 3 ...
##  $ tip_pct        : num [1:10918] 0.1596 0 0.197 0.0633 0.0752 ...
```

This function can also be used to aggregate summary statistics over a Dataset by computing partial results for each batch and then aggregating those partial results. Extending the example above, you could fit a model to the sample data and then use map_batches to compute the MSE on the full Dataset.

```r
model <- lm(tip_pct ~ total_amount + passenger_count, data = sampled_data)

ds %>%
  filter(year == 2015) %>%
  select(tip_amount, total_amount, passenger_count) %>%
  mutate(tip_pct = tip_amount / total_amount) %>%
  map_batches(function(batch) {
    batch %>%
      as.data.frame() %>%
      mutate(pred_tip_pct = predict(model, newdata = .)) %>%
      filter(!is.nan(tip_pct)) %>%
      summarize(sse_partial = sum((pred_tip_pct - tip_pct)^2), n_partial = n()) %>%
      as_record_batch()
  }) %>%
  summarize(mse = sum(sse_partial) / sum(n_partial)) %>%
  pull(mse)
```

```r
## 
## [1] 0.1304284
```

## refs

### up

- [[/zettel/r-pkg-arrow]]

### down

* [[docs-r-arrow-multifile-data]]


+++
title = "Wavelet Transforms on MLB Data"
date = 2020-05-01

[taxonomies]
tags = ["mathematics", "data-science"]
categories = ["projects"]
+++

For my Applied Algebra class, I applied Haar and CDF(2,2) wavelet transforms to Major League Baseball data from the Lahman database to analyze trends in strikeouts (SO) and complete games (CG) over time.

## Data

I used the Lahman `Teams.csv` dataset, pulling yearly team-level strikeout totals from 2005–2015 and complete game totals from 1965–2015. After grouping by year to get season-wide aggregates, the raw trends were clear:

- **Strikeouts** rose steadily from ~30,644 in 2005 to ~37,446 in 2015 — a 22% increase driven by the league's shift toward power pitching and strikeout-heavy relief usage.
- **Complete games** collapsed from 739 in 1965 to just 104 in 2015, reflecting the modern reliance on bullpens and pitch-count management.

I then computed year-over-year differences for both metrics to remove the overall trend and expose the year-to-year variation.

## Wavelet Transforms

### Haar Wavelet

The Haar transform decomposes a signal into a **trend** (averages of adjacent pairs) and **detail** (differences of adjacent pairs):

```
trend[k]  = (x[2k] + x[2k+1]) / 2
detail[k] = (x[2k] - x[2k+1]) / 2
```

I applied this to both the strikeout and complete game difference series. The trend component captured the low-frequency movement (e.g., the spike in strikeout growth around 2012 corresponding to the league-wide power surge), while the detail isolated high-frequency noise.

### CDF(2,2) Wavelet

The CDF(2,2) (Cohen-Daubechies-Feauveau) biorthogonal wavelet uses a more sophisticated lifting scheme:

```
detail[k] = x[2k+1] - 0.5 * (x[2k] + x[2k+2])
trend[k]  = 0.75 * x[2k] + 0.25 * (x[2k-1] + x[2k+1]) - 0.125 * (x[2k-2] + x[2k+2])
```

Unlike Haar, CDF(2,2) incorporates neighboring samples (2 vanishing moments for the detail, 2 for the trend), producing smoother approximations. This yielded cleaner trend/detail separation, especially on the complete game data where the decline is nonlinear and the year-to-year variance is high.

## Conclusion

Both wavelets faithfully reconstructed the original signals from their trend and detail components, but the choice of wavelet mattered. Haar offered sharp, interpretable detail coefficients at the cost of a blocky trend, while CDF(2,2) traded some detail sharpness for a smoother, more faithful approximation of the underlying trajectory. This made CDF(2,2) better suited for capturing the accelerating decline in complete games and the accelerating rise in strikeouts — both nonlinear trends that Haar's simple averaging smooths over.

More broadly, this was a good demonstration of why wavelets are useful: a simple moving average or year-over-year plot can hint at a trend, but a wavelet decomposition separates signal from noise at multiple scales simultaneously, revealing structure you'd otherwise miss. Applied Algebra turned out to be a lot more interesting when you get to point it at real data.


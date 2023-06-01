# Optimal reconciliation

[Notes from Forecasting: Principles and Practice](https://otexts.com/fpp2/reconciliation.html)

All of chapter 10 of this book looks at Forecasting Hierarchical or grouped timeseries, optimal reconciliation is just section 10.7

This book was more concerned with the question of "should we forecast the fundamentals, and then aggregate up?" vs "should we forecast the aggregate and then project down via percentages?". This was a really interesting question for Stitch Fix (e.g. we used to look at forecasting autoship demand and then breaking it down via cadences).

One of the interesting points made here is that all top down methods are biased, whereas the bottom up methods are not. The optimal reconcillation approach was interesting -- you make a forecast at all levels of the hierarchy, and then combine them "optimally".



# Hotel Harmony: Data Insights for Optimized Operations

Elite Hotels International case study on the Kaggle *Hotel Booking Demand* data (`hotel_bookings.csv`, 119,390 bookings, 2015 to 2017, two hotels). Tools: Python, Pandas, Matplotlib, Seaborn, SciPy, statsmodels, scikit-learn.

## Two versions of the data
- **All rows (119,390):** the file as given, only missing values filled (this matches the brief's hints).
- **Cleaned (87,228):** 31,994 exact duplicate rows and 168 invalid rows removed (166 with no guests, 1 negative ADR, 1 ADR of 5,400).

The file has no booking ID, so a duplicate row may be a genuine second booking. Both versions are shown because duplicates change several answers a lot (cancellation rate 37.0% vs 27.5%).

## Cleaning log
| Item | Action |
|---|---|
| children (4 missing) | set to 0 |
| country (488 missing) | `Unknown` |
| agent (16,340 missing) | means no agent; kept missing for counting, 0 for modelling |
| company (112,593 missing) | 94% empty, not used |
| Dates | arrival date built from year, month, day; `reservation_status_date` converted |
| Duplicates | 31,994 removed in the cleaned version |
| Invalid rows | 168 removed (see above) |
| Derived | `nights` = weekend + week nights; `revenue` = ADR x nights for non-cancelled stays |

`reservation_status` and its date are outcomes and are not used as predictors.

## Answers: Basic
| # | Question | All rows | Cleaned |
|---|---|---|---|
| 1 | Average lead time | 104.0 days | 80.0 days |
| 2 | Bookings by hotel | City 79,330 / Resort 40,060 | City 53,273 / Resort 33,955 |
| 3 | Cancelled | 44,224 (37.0%) | 24,008 (27.5%) |
| 4 | Most common arrival month | August (13,877) | August (11,242) |
| 5 | Average special requests | 0.571 | 0.699 |
| 6 | Country with most bookings | Portugal (48,590) | Portugal (27,354) |
| 7 | ADR by hotel | City 105.30 / Resort 94.95 | City 111.17 / Resort 99.06 |
| 8 | Needing parking | 6.2% | 8.4% |
| 9 | Avg week / weekend nights | 2.50 / 0.93 | 2.62 / 1.00 |
| 10 | Bookings via travel agents | 103,050 (86.3%) | 75,087 (86.1%) |

## Answers: Medium
| # | Question | All rows | Cleaned |
|---|---|---|---|
| 1 | Cancellation rate by hotel | City 41.7% / Resort 27.8% | City 30.1% / Resort 23.5% |
| 2 | Average ADR by segment | Online TA 117.2, Direct 115.4, Aviation 100.1, Offline TA/TO 87.4, Groups 79.5, Corporate 69.4, Complementary 2.9 | Online TA 118.3, Direct 116.8, Aviation 100.6, Offline 81.6, Groups 75.2, Corporate 68.3, Complementary 3.1 |
| 3 | Lead time vs cancellation | r = 0.293; cancel rate 12.9% (0-14 days) to 67.7% (365+) | r = 0.185; 11.5% to 40.8% |
| 4 | Top distribution channel | TA/TO (97,870) | TA/TO (69,027) |
| 5 | Avg previous cancellations by hotel | City 0.080 / Resort 0.102 | City 0.036 / Resort 0.022 |
| 6 | ADR by year | 2015 87.18, 2016 98.33, 2017 114.64 | 92.36, 101.57, 118.92 |
| 7 | Highest-revenue month | August on the brief's sum of ADR (1,944,328) | August (sum of ADR 1,698,164; realised revenue 4,606,618) |
| 8 | Special requests vs ADR | r = 0.172 | r = 0.146; ADR 99.8 with none to 130 with 5 |
| 9 | Avg stay, new vs repeat | 3.48 vs 1.93 nights | 3.70 vs 1.95 nights |
| 10 | Most booked room type | A (85,994) | A (56,434) |

2015 covers Jul-Dec only and 2017 Jan-Aug only, so yearly ADR partly reflects season mix.

## Answers: Advanced (cleaned data; see notebook for all-rows figures)
1. **Cancellation drivers (logistic regression):** 5-fold CV AUC 0.795, accuracy 78.5%. Parking need lowers cancellation sharply; more special requests, more previous non-cancelled stays, Offline TA/TO and Direct lower it; previous cancellations, Online TA, Transient customers and longer lead time raise it. **Non Refund deposit** shows an odds ratio of 45, but those bookings cancel 94.7% (99.4% on all rows), a known quirk of this dataset. It is a statistical pattern, not a reason to enforce non-refundable deposits.
2. **ADR vs guests (OLS):** each extra adult +20.7, child +38.6, baby +6.7 (all p < 0.001); R-squared 0.181, so guest counts explain little.
3. **Booking changes vs special requests:** Pearson 0.018, Spearman -0.004 (all rows 0.053 / 0.042): essentially no relationship. The data has no satisfaction score, so this is only a proxy.
4. **Seasonality of cancellations:** highest in Aug 32.2%, Jul 31.8%, Apr 30.5%, Jun 30.3%; lowest in Nov 21.2% and Jan 22.1%.
5. **Lead time by segment (median days):** Groups 115, Offline TA/TO 82, Online TA 55, Direct 15, Corporate 5, Aviation 3. Box plots are in the notebook.

## Recommendations
1. Manage long-lead bookings (90+ days) with reminders and overbooking limits.
2. Watch Online TA, the largest segment with the highest cancellation rate (35%).
3. Plan rates and staffing for July and August (Resort ADR peaks at 187 in August).
4. Encourage special requests and direct or repeat bookings; both go with fewer cancellations.
5. Capture booking ID, occupancy, satisfaction and currency.

## Limitations
No satisfaction field; no room counts or occupancy (no RevPAR); no booking ID; currency not stated; uneven year coverage; associations, not causes; Non Refund pattern is a data quirk.

## Repository structure
```
data/hotel_bookings.csv            original data
data/hotel_bookings_cleaned.csv    87,228 cleaned rows with derived columns
notebooks/Hotel_Harmony_Booking_Analysis.ipynb
presentation/Hotel Harmony Booking Analysis.pptx
screenshots/hotel_dashboard.png
```

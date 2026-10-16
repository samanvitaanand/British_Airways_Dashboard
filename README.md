 # British Airways Review Dashboard

An interactive Tableau dashboard that explores customer reviews of British Airways from 2016 to 2023. It lets you see how passengers rate the airline overall and across specific parts of the experience, and how those ratings change by time, place, aircraft, and type of traveller.

![Dashboard overview](images/dash.png)

## Questions the dashboard answers

- How do passengers rate British Airways overall, and which parts of the experience score best and worst?
- How have ratings changed month to month over time?
- Which countries give the highest and lowest ratings?
- Which aircraft types are rated highest, and how many reviews back up each rating?
- Do ratings differ by traveller type, seat type, or continent?

## Features

**Summary KPIs.** The header shows the average score for six categories: cabin staff service, food and beverages, ground service, seat comfort, value for money, and entertainment.

**Pick a metric.** A selector switches the month chart, map, and aircraft bars between the overall rating and the category ratings (cabin staff service, food, ground service, seat comfort, and value). Entertainment is shown in the KPI header.

**Filters.** Every view responds to these filters:

| Filter | Options |
|---|---|
| Month of Date | March 2016 to May 2023 (range slider) |
| Traveller Type | All, Business, Couple Leisure, Family Leisure, Solo Leisure |
| Seat Type | All, Business Class, Economy Class, First Class, Premium Economy |
| Continent | All, or a single continent |

### Average rating by month

A line chart that tracks the selected metric over time, so you can spot trends and dips.

### Average rating by country

![Rating by country map](images/map.png)

A choropleth map where darker green means a higher average rating. Countries with no reviews are left blank.

### Average rating by aircraft

![Rating by aircraft](images/bar.png)

Paired bar charts show the average rating for each aircraft next to its number of reviews. Showing the review count alongside the rating makes it easier to judge how much weight each average deserves.

## Key takeaways

- Overall ratings are on a 1 to 10 scale, and the average across all 1,324 reviews is 4.2.
- By aircraft, the Boeing 747-400 has the highest average overall rating (4.7) and the A321 the lowest (3.4).
- The most-reviewed aircraft group is "Various" (319 reviews), followed by the A320 (266). Aircraft with fewer reviews, like the Boeing 787 (55), have less reliable averages.
- Among the category ratings (1 to 5 scale), cabin staff service scores highest (3.3), while food, entertainment, and value for money score lowest (all about 2.7 to 2.8) once unrated reviews are excluded (see Known limitations).

## How it works

- A **parameter** ("Pick a metric") feeds a calculated field, `Metric Selected`, which uses a `CASE` statement to switch between the overall rating and the category ratings.
- The month line chart, country map, and aircraft bars all average `Metric Selected`, so one selector updates all three views. Chart titles update to show the chosen metric.
- Continent filtering comes from a second file, `Countries.csv`, matched to each review's country (all 56 countries in the reviews matched).
- The aircraft view pairs the average rating with a count of reviews, so each average can be judged against its sample size.
- The KPI header averages each category directly and responds to the same date, traveller type, seat type, and continent filters.

## Data

- **Source:** the Kaggle dataset "Flight Chronicles: An In-depth Analysis of British Airways Reviews (2012-2023)" by willianoliveiragibin (`ba_reviews.csv`), plus `Countries.csv` as a country-to-continent lookup
- **Size:** 1,324 reviews from 56 countries, March 2016 to October 2023 (the dashboard's date filter runs March 2016 to May 2023, so the 51 reviews after that may not be shown)
- **Rating scales:** overall `rating` is 1 to 10; the six category ratings (`seat_comfort`, `cabin_staff_service`, `food_beverages`, `ground_service`, `value_for_money`, `entertainment`) are 1 to 5
- **Other fields:** `date`, `place` (reviewer country), `aircraft`, `traveller_type`, `seat_type`, `route`, `date_flown`, `recommended`, `trip_verified`
- **Prep in Tableau:** aircraft variants are grouped for the aircraft chart, and continent is joined from `Countries.csv`

## Tools

- Tableau Desktop (parameters, calculated fields, dual-axis bars, filled map)
- Mapbox basemap

## Known limitations

I documented these after finishing the dashboard and no longer have access to the Tableau workbook, so they are not fixed in the published version.

- **Unrated categories are counted as -1.** The dataset stores a missing category rating as -1, and the dashboard averages include those values. This pulls some category averages down:

  | Category | Unrated reviews | Dashboard average | Average excluding unrated |
  |---|---|---|---|
  | Entertainment | 457 of 1,324 | 1.4 | 2.7 |
  | Food & beverages | 100 of 1,324 | 2.4 | 2.7 |
  | Cabin staff service | 11 of 1,324 | 3.3 | 3.3 |
  | Seat comfort | 11 of 1,324 | 2.9 | 2.9 |
  | Ground service | 4 of 1,324 | 3.0 | 3.0 |
  | Value for money | 0 | 2.8 | 2.8 |

  Entertainment is the most affected: roughly a third of reviews have no entertainment rating, so its 1.4 on the dashboard understates the real average. Overall rating has no missing values.
- **Mixed scales.** The overall rating (1 to 10) and category ratings (1 to 5) share one selector, so the axes change scale depending on the metric chosen. The aircraft chart's axis stops at 5, but the averages shown for overall rating are out of 10.

## Design choices

- A green palette for ratings, in keeping with the British Airways Review header, with darker shades for higher values.
- A contrasting pink palette for review counts so they are not confused with ratings.
- Sorted bars on the aircraft chart to make rankings easy to read.

## What I learned

The biggest lesson was to look at the raw data before trusting the averages. Some category ratings were stored as -1 when a reviewer left them blank, and it dragged a few of the KPI numbers down without me noticing. I only caught it after going back through the CSV, and now I check for placeholder values before I build anything.

I also got a lot more comfortable with parameters. Using one selector to switch the map, the line chart, and the aircraft bars between metrics took some trial and error, but it made the dashboard feel much more interactive. Putting the number of reviews next to each aircraft's rating was another small change that made the chart easier to trust.

If I rebuilt it, I'd clean the -1 values first and keep the 1 to 10 overall rating and the 1 to 5 category ratings on separate views so the scales don't get mixed up.




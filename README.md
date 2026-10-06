## Project Overview

This project analyses 50,000 delivery orders to answer nine questions from the Operations Manager about when deliveries get slow, where they get slow, and why.

**What the data covers:** order hour, day of week, pickup and drop-off zone, weather, traffic level, number of traffic signals on the route, trip distance, delivery speed, and total time taken per order.

## Tools and Technologies

- 🐍 **Python:** Data analysis and cleaning
- 🐼 **PANDAS** / NumPy: Data manipulation and statistical calculations
- 📊 **MATPLOTLIB** / Seaborn: Data visualization
- 📓 **Jupyter Notebook:** Exploratory data analysis and documentation

**Headline finding:** deliveries slow down because of traffic, not because of order volume. Weather, CBD pickups and long signal chains add to it.

**How this README is organised**

1. Operations Manager Questions: the nine questions asked.
2. Operations Manager Report: the answers, written as a story, with a technical notes section at the bottom.

---

## Operations Manager Questions

| #   | Question                                                                                          |
| --- | ------------------------------------------------------------------------------------------------- |
| Q1  | What are our busiest hours by order volume, and does delivery time get worse when volume is high? |
| Q2  | Why do orders take longer to arrive during some hours?                                            |
| Q3  | Which days of the week have the highest order volume?                                             |
| Q4  | Which pickup zones slow us down?                                                                  |
| Q5  | Which pickup-to-drop-off zone pairs take longest?                                                 |
| Q6  | How should we change staffing or delivery radius in rain, fog and storms?                         |
| Q7  | How much time do we lose to traffic?                                                              |
| Q8  | Do more traffic signals on a route add meaningful delay?                                          |
| Q9  | How many orders are delivered per hour at peak, and how does speed change?                        |

---

## Operations Manager Report

### Our deliveries slow down because of traffic, not because of volume

**The story in four lines**

- We are busiest at lunch (12:00 to 13:00) and in the evening (18:00 to 20:00), and every day of the week looks the same.
- We are slowest at 8:00 to 10:00 and 18:00 to 21:00. Both windows run at traffic level 2; lunch, our other peak, runs at level 1 and stays fast.
- The biggest single drags are CBD pickups (about 10 minutes slower), storms (about 37 minutes slower than clear weather), severe traffic, and routes with 14 or more signals.
- So we should staff and plan by the clock and the conditions, not by the day of the week.

---

### Q1. When are we busiest, and does delivery get slower?

Demand has two clear peaks. Lunch builds to 3,905 orders at 13:00, and the evening is bigger, peaking at 4,773 orders at 19:00. Together, 12:00 to 13:00 and 18:00 to 20:00 carry about 40% of all orders in just five hours of the day. Between 0:00 and 4:00 we see fewer than 510 orders an hour.

![order volume per hour](/images/order_hour_day.png)

Across the 24 hours, order volume and average delivery time have a moderate positive correlation of +0.48: busier hours tend to be slower.

![vol avg del](/images/del_time_ord_val.png)

But the two lunch hours sit at about 81 minutes, while the three evening hours sit at about 87 to 88 minutes, with similar volume. Volume alone cannot be the whole explanation. Something else is different about the evening.

### Q3. Does the day of the week matter?

No. Tuesday is technically the busiest day with 7,264 orders, but Saturday, the quietest, still has 6,978. The whole spread is 286 orders, under 4%.

![order volume per day ](/images/order_day.png)

> Staffing should follow the hour of the day. A weekday and weekend rota based on volume would not match what the data shows.

### Q2. Why do some hours take longer to deliver?

Average delivery time sits at about 80 to 82 minutes for most of the day, then jumps in two windows: 8:00 to 10:00 (88, 87 and 89 minutes) and 18:00 to 21:00 (about 87 to 88 minutes). The fastest hour is 2:00 at 79 minutes. The overall average is about 84 minutes.

![avg del time per hour](/images/avg_del_time_hr.png)

Every slow hour has the same thing in common: a traffic level of 2, while all other hours sit at 1. The volume story does not hold up here. The morning window handles only 1,900 to 2,500 orders an hour, well below the lunch peak of 3,400 to 3,900, yet it is just as slow as the evening. Lunch is busy but fast, because the roads are clear.

> Slow hours are explained by traffic. High volume only makes the evening look worse because the evening rush happens to coincide with heavy traffic.

### Q9. What happens to speed at peak?

At 19:00 we handle 4,773 orders, our busiest hour. In the hours with traffic level 2 (8:00 to 10:00 and 18:00 to 21:00) riders average roughly 29.5 to 29.8 on the speed measure. Every other hour is between 32 and 34, about 11% faster. Lunch, with 3,400 to 3,900 orders, keeps its speed at about 33.

![speed at peak](/images/del_speed_ord_vol.png)

This is the mirror image of the Q1 scatter plot, with a correlation of -0.49. The slow cluster at the bottom of the chart is the traffic-level-2 hours, and it contains the evening peak.

### Q7. How much time do we lose to traffic?

A lot. Compared with low traffic (76 minutes), moderate traffic adds 16 minutes, high traffic adds 36, and severe traffic adds 58: a delivery in severe traffic takes 76% longer.

![time loss to traffic](/images/time_loss_traffic.png)

Severe traffic is rare, with 524 orders. Low traffic covers 32,567 orders, moderate 13,165 and high 3,744. Weighting each level by its order count, traffic costs us about 7.5 minutes on the average order. Of all the minutes lost, roughly 56% come from moderate traffic, 36% from high, and only 8% from severe. The everyday moderate conditions hurt more in total than the dramatic ones.

### Q8. Do more traffic signals add meaningful delay?

Yes, but only past a point. From 2 to 13 signals, delivery time stays between 79 and 84 minutes with no steady trend. At 14 signals it rises to 88.8, at 15 to 92.5, and from 16 to 18 it levels off near 94.5. Going from the fewest to the most signals adds 12.4%.

![traffic signal effect on del time](/images/avg_del_time_traffic_signals.png)

About 13,000 orders (roughly one in four) travel routes with 14 or more signals. One caution: we have not yet checked whether those long-signal routes are mostly CBD routes. If they are, part of this effect belongs to the CBD story below.

### Q4. Which pickup zones slow us down?

The CBD, clearly. Orders picked up there average 93.8 minutes, about 10 minutes above the next zone (suburban, 83.3) and the only zone above our overall average of roughly 84. It is also the zone with the heaviest traffic, and with 10,997 orders it is our second busiest pickup point.

![pickup zone](/images/avg_order_pick_up_zone.png)

Suburban and commercial are nearly identical on time (83.3 minutes), but commercial brings more orders (9,523 against 6,461). Residential is our largest zone with 15,786 orders and still performs well at 80.3 minutes. Industrial is the fastest at 77.9.

### Q5. Which pickup-to-drop-off pairs take longest?

The answer is decided at the pickup end. Every pair that starts in the CBD takes 93.6 to 94.5 minutes, whatever the destination. The slowest pair, CBD to suburban, takes 94.5 minutes; the fastest, industrial to CBD, takes 77.2. That is a 17-minute gap, and it is the pickup zone, not the drop-off, that drives it.

| Pickup zone | Average time across all drop-off zones |
| ----------- | -------------------------------------: |
| CBD         |                       93.6 to 94.5 min |
| Commercial  |                       82.3 to 84.8 min |
| Suburban    |                       82.2 to 84.1 min |
| Residential |                       79.4 to 81.5 min |
| Industrial  |                       77.2 to 78.6 min |

> Delivering _into_ the CBD is not the problem. Collecting _from_ it is.

### Q6. How should we adjust for rain, fog and storms?

Weather changes delivery time a lot, and distance is not the reason: the average trip is about 26 km in every condition. Storms average 114.5 minutes, 37 minutes (48%) slower than clear weather at 77.3. Rain averages 98.9, which is 21.6 minutes slower. Fog is 6.9 minutes slower at 84.2, and cloudy weather at 79.0 is almost the same as clear.

!["delivery time weather"](/images/avg_del_time_weather.png)

Storms touch about 5% of orders (2,437) and rain about 18% (9,073), so rain affects far more deliveries, while a storm is the more severe case.

The data shows what weather does to delivery time, not what staffing or radius changes would do, because those were not recorded. These are starting points to test, not proven results:

- **Storm:** plan for roughly 35 to 40 extra minutes per delivery, add rider capacity or tighten the delivery radius, and quote longer arrival times.
- **Rain:** plan for around 20 extra minutes and keep a standby pool of riders.
- **Fog and cloudy:** no change needed; fog adds less than 7 minutes.

---

### What we recommend

1. **Roster by hour, not by day.** Add capacity from 8:00 to 10:00 and 18:00 to 21:00, when traffic is worst. Lunch needs volume cover but not extra time allowance.
2. **Give CBD pickups their own plan.** They run about 10 minutes long and make up over a fifth of orders. Dispatch earlier, set longer arrival times, or dedicate riders.
3. **Adopt a weather rule.** Storm and rain trigger a staffing uplift and a longer arrival-time promise, with radius reductions piloted in storms.
4. **Review high-signal routes.** Where a route with 14 or more signals has an alternative, test it, after first checking how much of the effect is really CBD.
5. **Track the right measure.** Report delivery time by traffic level alongside volume, since volume on its own points to the wrong cause.

---

### The full dashboard

All of the charts above on one page, for reference.

![operations dashboard](/images/delivery_dashboard2.png)

---

<!-- ### Technical notes

- **Correlations:** delivery time vs hourly order volume is +0.48; delivery speed vs hourly order volume is -0.49. Both are calculated across the 24 hours of the day, so each point is one hour, not one order. They are moderate, and Q2 shows traffic explains the pattern better than volume does.
- **Calculated figures:** the 7.5 minutes lost per order and the 56% / 36% / 8% split come from the order counts and average times in the traffic table, using low traffic (76 min) as the baseline. The 11% speed drop compares roughly 29.7 (traffic level 2 hours) with roughly 33.3 (all other hours).
- **Weather averages:** the chart's dashed "Average: 90.8 min" line is the simple mean of the five weather categories, not the order-weighted average, so it is not comparable to the overall average of about 84 minutes.
- **Rounding:** the overall average appears as 83.7 on the pickup zone chart and 83.8 on the hourly chart; the report uses "about 84 minutes".
- **Speed units:** the speed measure is reported as it appears in the dataset; the unit is not shown on the charts.
- **Open check:** signal count and pickup zone may be linked (CBD routes may have more signals). A follow-up analysis should compare signal effects within a single zone. -->

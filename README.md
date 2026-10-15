# Delivery Operations and Supply Analysis: Management Report

> An analysis of 50,000 delivery orders covering when and where deliveries slow down, why they slow down on the road, and how restaurant behaviour in the kitchen adds to delivery time, with recommendations for the Operations Manager and the Restaurant Partnerships / Supply Manager.

**Prepared by:** David, Data Analyst

## Tools and Technologies

- 🐍 **Python:** Data analysis and cleaning
- 🐼 **PANDAS** / NumPy: Data manipulation and statistical calculations
- 📊 **MATPLOTLIB** / Seaborn: Data visualization
- 📓 **Jupyter Notebook:** Exploratory data analysis and documentation

## Dataset : 50,000 delivery orders

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Background and Problem Statement](#2-background-and-problem-statement)
3. [Objectives](#3-objectives)
4. [Data Overview](#4-data-overview)
5. [Findings](#5-findings)
   - [5.1 Delivery Time in Two Parts](#51-delivery-time-in-two-parts)
   - [5.2 Busy Hours and Slow Hours](#52-busy-hours-and-slow-hours)
   - [5.3 Traffic and Traffic Signals](#53-traffic-and-traffic-signals)
   - [5.4 Pickup Zones and Routes](#54-pickup-zones-and-routes)
   - [5.5 Weather](#55-weather)
   - [5.6 Preparation Time and Cuisine](#56-preparation-time-and-cuisine)
   - [5.7 Restaurant Load and Rating](#57-restaurant-load-and-rating)
   - [5.8 Order Size](#58-order-size)
   - [5.9 Slow-Preparing Pickup Zones](#59-slow-preparing-pickup-zones)
   - [5.10 Restaurant Load by Hour](#510-restaurant-load-by-hour)
6. [Recommendations](#6-recommendations)
7. [Data Notes and Limitations](#7-data-notes-and-limitations)

---

## 1. Executive Summary

Every one of the 50,000 orders has a delivery time of about **84 minutes on average**, and roughly **24 of those minutes are spent waiting for food**. The two managers' questions look at the two halves of that clock: the road (Operations) and the kitchen (Supply). The main findings are:

- **Delivery time has two parts: the kitchen and the road.** Preparation averages 24.0 minutes (range 5 to 51) and makes up about a third (32.6%) of every delivery. The rest is travel and handover, where traffic, weather and pickup zone do their work.
- **On the road, traffic is the main driver, not volume.** The slowest hours (8:00 to 10:00 and 18:00 to 21:00) all run at traffic level 2. Severe traffic makes a delivery 76% longer than low traffic, and everyday moderate traffic costs the most minutes overall (about 56% of all minutes lost).
- **Weather and long signal chains add to it.** Storms are about 37 minutes (48%) slower than clear weather, rain about 21.6 minutes slower, and routes with 14 or more signals run 88.8 to about 94.5 minutes against 79 to 84 for shorter routes.
- **In the kitchen, cuisine and order size matter most.** Biryani takes 34 minutes to prepare and 94 to deliver; bakery takes 13 and 74. Every extra item adds about 2 minutes of preparation, and delivery rises by the same amount.
- **Restaurant load and rating are minor.** High load adds only about 4 minutes, and restaurant rating shows no steady link to speed.
- **The CBD shows up on both sides.** CBD pickups average 93.8 minutes, about 10 minutes above the next zone, and 32.4% of CBD orders are slow-preparing against 17.8% to 22.8% elsewhere.
- **The day of the week does not matter.** The busiest and quietest days differ by under 4%, so planning should follow the hour and the conditions.

---

## 2. Background and Problem Statement

Two departments asked questions of the same order data, and each question looked at a different part of the delivery:

- The **Operations Manager** wanted to know when deliveries get slow, where they get slow, and why (nine questions).
- The **Restaurant Partnerships / Supply Manager** wanted to know how restaurant behaviour affects delivery time (eight questions).

Read separately, each answer is incomplete. Operations can explain the road but not the 24 minutes spent before the rider leaves, and Supply can explain the kitchen but not why the same kitchen is slower at 19:00 than at 13:00. This report puts both sets of answers on one page so that the two departments can plan from the same picture.

## 3. Objectives

| #   | Department | Question                                                                                                       |
| --- | ---------- | -------------------------------------------------------------------------------------------------------------- |
| 1   | Operations | Ops Q1: What are our busiest hours by order volume, and does delivery time get worse when volume is high?      |
| 2   | Operations | Ops Q2: Why do orders take longer to arrive during some hours?                                                 |
| 3   | Operations | Ops Q3: Which days of the week have the highest order volume?                                                  |
| 4   | Operations | Ops Q4: Which pickup zones slow us down?                                                                       |
| 5   | Operations | Ops Q5: Which pickup-to-drop-off zone pairs take longest?                                                      |
| 6   | Operations | Ops Q6: How should we change staffing or delivery radius in rain, fog and storms?                              |
| 7   | Operations | Ops Q7: How much time do we lose to traffic?                                                                   |
| 8   | Operations | Ops Q8: Do more traffic signals on a route add meaningful delay?                                               |
| 9   | Operations | Ops Q9: How many orders are delivered per hour at peak, and how does speed change?                             |
| 10  | Supply     | Supply Q1: How long does preparation take on average, and how much of total delivery time is waiting for food? |
| 11  | Supply     | Supply Q2: Which cuisines are slowest and fastest to deliver?                                                  |
| 12  | Supply     | Supply Q3: Which cuisines have the longest preparation times?                                                  |
| 13  | Supply     | Supply Q4: Does restaurant load (low, medium, high) increase preparation time and delivery time?               |
| 14  | Supply     | Supply Q5: Does a higher restaurant rating mean faster preparation or delivery?                                |
| 15  | Supply     | Supply Q6: Do larger orders (more items) take longer to prepare?                                               |
| 16  | Supply     | Supply Q7: Which pickup zones have the most slow-preparing restaurants?                                        |
| 17  | Supply     | Supply Q8: At what hours is restaurant load highest, and do delivery times rise at those hours?                |

## 4. Data Overview

| Item                                     | Detail                                                                                                                                                |
| ---------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| Total orders                             | 50,000                                                                                                                                                |
| Average delivery time                    | About 84 minutes                                                                                                                                      |
| Average preparation time                 | 24.0 minutes (range 5 to 51)                                                                                                                          |
| Share of delivery spent waiting for food | 32.6% on average                                                                                                                                      |
| Pickup zones                             | Residential 15,786; CBD 10,997; Commercial 9,523; Industrial 7,233; Suburban 6,461                                                                    |
| Operations fields                        | Order hour, day of week, pickup and drop-off zone, weather, traffic level, number of traffic signals, trip distance, delivery speed, total time taken |
| Supply fields                            | Preparation time, total time taken, cuisine type, restaurant load, restaurant rating, number of items, pickup zone, order hour                        |

---

## 5. Findings

### 5.1 Delivery Time in Two Parts

Every delivery time in this report is made of two parts. The first is the **kitchen**: the time from order to food ready. The second is the **road**: everything after the food is ready, including travel and handover. The next four sections explain the road, and the five after that explain the kitchen.

| Part    | Typical size                                     | What drives it                                         | Where to read it |
| ------- | ------------------------------------------------ | ------------------------------------------------------ | ---------------- |
| Road    | About 60 minutes (about 84 minus 24, calculated) | Hour of day and traffic, weather, pickup zone, signals | 5.2 to 5.5       |
| Kitchen | 24.0 minutes (5 to 51)                           | Cuisine and order size; load and rating only slightly  | 5.6 to 5.8       |

**Insight:** The CBD and the hours of the day appear in both halves, so they are tied together again in 5.9 and 5.10.

---

### 5.2 Busy Hours and Slow Hours

#### Ops Q1. When are we busiest, and does delivery get slower?

Demand has two clear peaks. Lunch builds to 3,905 orders at 13:00, and the evening is bigger, peaking at 4,773 orders at 19:00. Together, 12:00 to 13:00 and 18:00 to 20:00 carry about 40% of all orders in just five hours of the day. Between 0:00 and 4:00 we see fewer than 510 orders an hour.

![order volume per hour](/images/order_hour_day.png)

Across the 24 hours, order volume and average delivery time have a moderate positive correlation of +0.48: busier hours tend to be slower.

![vol avg del](/images/del_time_ord_val.png)

**Insight:** The two lunch hours sit at about 81 minutes, while the three evening hours sit at about 87 to 88 minutes with similar volume. Volume alone cannot be the whole explanation. Something else is different about the evening.

#### Ops Q3. Does the day of the week matter?

No. Tuesday is technically the busiest day with 7,264 orders, but Saturday, the quietest, still has 6,978. The whole spread is 286 orders, under 4%.

![order volume per day ](/images/order_day.png)

**Insight:** Staffing should follow the hour of the day. A weekday and weekend rota based on volume would not match what the data shows.

#### Ops Q2. Why do some hours take longer to deliver?

Average delivery time sits at about 80 to 82 minutes for most of the day, then jumps in two windows: 8:00 to 10:00 (88, 87 and 89 minutes) and 18:00 to 21:00 (about 87 to 88 minutes). The fastest hour is 2:00 at 79 minutes. The overall average is about 84 minutes.

![avg del time per hour](/images/avg_del_time_hr.png)

Every slow hour has the same thing in common: a traffic level of 2, while all other hours sit at 1. The morning window handles only 1,900 to 2,500 orders an hour, well below the lunch peak of 3,400 to 3,900, yet it is just as slow as the evening. Lunch is busy but fast, because the roads are clear.

**Insight:** Slow hours are explained by traffic. High volume only makes the evening look worse because the evening rush happens to coincide with heavy traffic.

#### Ops Q9. What happens to speed at peak?

At 19:00 we handle 4,773 orders, our busiest hour. In the hours with traffic level 2 (8:00 to 10:00 and 18:00 to 21:00) riders average roughly 29.5 to 29.8 on the speed measure. Every other hour is between 32 and 34, about 11% faster. Lunch, with 3,400 to 3,900 orders, keeps its speed at about 33.

![speed at peak](/images/del_speed_ord_vol.png)

**Insight:** This is the mirror image of the Ops Q1 scatter plot, with a correlation of -0.49. The slow cluster at the bottom of the chart is the traffic-level-2 hours, and it contains the evening peak.

---

### 5.3 Traffic and Traffic Signals

The hours explain _when_ we slow down. Traffic explains _why_, and the next question measures what it costs.

#### Ops Q7. How much time do we lose to traffic?

Compared with low traffic (76 minutes), moderate traffic adds 16 minutes, high traffic adds 36, and severe traffic adds 58: a delivery in severe traffic takes 76% longer.

![time loss to traffic](/images/time_loss_traffic.png)

| Traffic level | Orders | Share of minutes lost (%) |
| ------------- | ------ | ------------------------- |
| Low           | 32,567 | baseline (76 min)         |
| Moderate      | 13,165 | about 56                  |
| High          | 3,744  | about 36                  |
| Severe        | 524    | about 8                   |

Weighting each level by its order count, traffic costs about **7.5 minutes on the average order**.

**Insight:** Severe traffic is rare, so the everyday moderate conditions hurt more in total than the dramatic ones.

#### Ops Q8. Do more traffic signals add meaningful delay?

Yes, but only past a point. From 2 to 13 signals, delivery time stays between 79 and 84 minutes with no steady trend. At 14 signals it rises to 88.8, at 15 to 92.5, and from 16 to 18 it levels off near 94.5. Going from the fewest to the most signals adds 12.4%.

![traffic signal effect on del time](/images/avg_del_time_traffic_signals.png)

**Insight:** About 13,000 orders (roughly one in four) travel routes with 14 or more signals. We have not yet checked whether those long-signal routes are mostly CBD routes. If they are, part of this effect belongs to the CBD story in 5.4.

---

### 5.4 Pickup Zones and Routes

Traffic and signals describe the route. The pickup zone describes where the route starts, and it turns out to matter more than where it ends.

#### Ops Q4. Which pickup zones slow us down?

The CBD, clearly. Orders picked up there average 93.8 minutes, about 10 minutes above the next zone (suburban, 83.3) and the only zone above our overall average of roughly 84. It is also the zone with the heaviest traffic, and with 10,997 orders it is our second busiest pickup point.

![pickup zone](/images/avg_order_pick_up_zone.png)

Suburban and commercial are nearly identical on time (83.3 minutes), but commercial brings more orders (9,523 against 6,461). Residential is our largest zone with 15,786 orders and still performs well at 80.3 minutes. Industrial is the fastest at 77.9.

#### Ops Q5. Which pickup-to-drop-off pairs take longest?

The answer is decided at the pickup end. Every pair that starts in the CBD takes 93.6 to 94.5 minutes, whatever the destination. The slowest pair, CBD to suburban, takes 94.5 minutes; the fastest, industrial to CBD, takes 77.2. That is a 17-minute gap, and it is the pickup zone, not the drop-off, that drives it.

| Pickup zone | Average time across all drop-off zones |
| ----------- | -------------------------------------: |
| CBD         |                       93.6 to 94.5 min |
| Commercial  |                       82.3 to 84.8 min |
| Suburban    |                       82.2 to 84.1 min |
| Residential |                       79.4 to 81.5 min |
| Industrial  |                       77.2 to 78.6 min |

**Insight:** Delivering _into_ the CBD is not the problem. Collecting _from_ it is. Because the delay starts at pickup, section 5.9 checks how much of it is kitchen time.

---

### 5.5 Weather

Weather is the last road factor. It changes delivery time a lot, and distance is not the reason.

#### Ops Q6. How should we adjust for rain, fog and storms?

The average trip is about 26 km in every condition. Storms average 114.5 minutes, 37 minutes (48%) slower than clear weather at 77.3. Rain averages 98.9, which is 21.6 minutes slower. Fog is 6.9 minutes slower at 84.2, and cloudy weather at 79.0 is almost the same as clear.

![delivery time weather](/images/avg_del_time_weather.png)

Storms touch about 5% of orders (2,437) and rain about 18% (9,073), so rain affects far more deliveries, while a storm is the more severe case.

**Insight:** The data shows what weather does to delivery time, not what staffing or radius changes would do, because those were not recorded. These are starting points to test, not proven results:

- **Storm:** plan for roughly 35 to 40 extra minutes per delivery, add rider capacity or tighten the delivery radius, and quote longer arrival times.
- **Rain:** plan for around 20 extra minutes and keep a standby pool of riders.
- **Fog and cloudy:** no change needed; fog adds less than 7 minutes.

---

### 5.6 Preparation Time and Cuisine

So far every finding is about the road, which is about 60 of the 84 minutes. The other 24 minutes are spent before the rider leaves, and that is where the Supply questions begin.

#### Supply Q1. How long does preparation take, and how much of delivery is waiting for food?

Preparation takes **24.0 minutes on average**, and individual orders range from **5 to 51 minutes**. Waiting for food makes up **32.6%** of total delivery time on average, roughly one-third of the time a customer waits.

**Insight:** One minute saved in the kitchen is a minute saved on the customer's wait. Preparation is the part of the journey the platform can influence through restaurant partnerships rather than through routing.

#### Supply Q2 and Q3. Which cuisines are slowest, and which take longest to prepare?

The two questions have almost the same answer. Cuisines that take longest to prepare are the cuisines that take longest to deliver.

| Cuisine      | Orders | Avg prep time (min) | Avg delivery time (min) |
| ------------ | -----: | ------------------: | ----------------------: |
| Biryani      |  7,802 |                34.0 |                    94.0 |
| North Indian |  9,483 |                30.0 |                    90.0 |
| Pizza        |  6,661 |                26.0 |                    87.0 |
| Chinese      |  6,132 |                24.0 |                    85.0 |
| South Indian |  5,109 |                22.0 |                    81.0 |
| Burger       |  5,198 |                17.0 |                    74.0 |
| Cafe         |  4,035 |                15.0 |                    74.0 |
| Desserts     |  2,672 |                13.0 |                    76.0 |
| Bakery       |  2,908 |                13.0 |                    74.0 |

![cusine delivery and prep time](/images/cuisine_delivery_and_prep_time.png)

Biryani and North Indian are the slowest on both measures. Biryani takes about 2.6 times as long to prepare as bakery or desserts (34 against 13 minutes), and its deliveries take about 20 minutes longer (94 against 74). North Indian and biryani together account for 17,285 orders, about 35% of all volume.

**Insight:** Subtract preparation time from delivery time and every cuisine lands between about 57 and 63 minutes. Cooking and travelling are separate parts of the journey, and the road part looks the same whatever is in the bag. **The 20-minute gap between the fastest and slowest cuisines is almost entirely preparation.**

---

### 5.7 Restaurant Load and Rating

The next two questions test whether the kitchen's circumstances, rather than its menu, change the numbers above.

#### Supply Q4. Does restaurant load increase preparation and delivery time?

Only a little. High load adds about 4 minutes to both preparation and delivery compared with low load.

| Restaurant load | Avg prep time (min) | Avg delivery time (min) |
| --------------- | ------------------: | ----------------------: |
| Low             |                23.0 |                    76.0 |
| Medium          |                24.0 |                    78.0 |
| High            |                27.0 |                    80.0 |

![returant load delivery and prep time](/images/restaurant_load_vs_prep_and_delivery_time.png)

**Insight:** Time rises steadily from low to high load, but the step is small next to what cuisine does (a 21-minute spread in preparation) or what order size does (12 minutes). A busy kitchen is slightly slower, not meaningfully slower.

#### Supply Q5. Does a higher rating mean faster preparation or delivery?

No. There is no steady pattern across the rating scale. Between 3.4 and 4.9, average preparation time stays in a narrow band of about 20 to 27 minutes, and delivery time stays between about 76 and 87 minutes, going up and down from one rating to the next rather than in one direction.

The two ends of the scale stand out. Restaurants rated 5.0 average 34.5 minutes of preparation and 98.1 minutes of delivery, the slowest of any rating, while those rated 3.3 are the fastest at 70.5 minutes of delivery. Neither end is evidence that rating drives speed.

![returant rating vs prep time](/images/rating_vs_prep_and_delivery_time.png)

**Insight:** Rating tells us how customers feel about the food, not how fast it leaves the kitchen. It should not be used to predict or promise speed.

---

### 5.8 Order Size

#### Supply Q6. Do larger orders take longer to prepare?

Yes, and the relationship is clean. Each extra item adds about 2 minutes of preparation, from 21 minutes for a single item to 33 minutes for seven, an increase of about 57%.

| Items in order | Avg prep time (min) | Avg delivery time (min) |
| -------------: | ------------------: | ----------------------: |
|              1 |                21.0 |                    81.0 |
|              2 |                23.0 |                    83.0 |
|              3 |                25.0 |                    84.0 |
|              4 |                27.0 |                    86.0 |
|              5 |                29.0 |                    88.0 |
|              6 |                31.0 |                    89.0 |
|              7 |                33.0 |                    93.0 |

![order items prep and delivery time](/images/order_items_vs_time.png)

**Insight:** Delivery time rises by 12 minutes from one item to seven, matching the 12-minute rise in preparation. The extra delivery time is the extra cooking time, so larger baskets cost time in the kitchen rather than on the road.

---

### 5.9 Slow-Preparing Pickup Zones

This is where the two departments' findings meet. Section 5.4 showed that delay starts at the pickup end, and the CBD is the slowest pickup zone. The Supply data shows whether the kitchen is part of that.

#### Supply Q7. Which pickup zones have the most slow-preparing restaurants?

The CBD has the highest rate of slow orders. Nearly one in three CBD orders (32.4%) is slow, against 17.8% to 22.8% in every other zone.

| Pickup zone | Total orders | Slow orders | % of zone's orders that are slow | % of all slow orders |
| ----------- | -----------: | ----------: | -------------------------------: | -------------------: |
| CBD         |       10,997 |       3,561 |                            32.4% |                30.0% |
| Residential |       15,786 |       3,601 |                            22.8% |                30.3% |
| Commercial  |        9,523 |       2,004 |                            21.0% |                16.9% |
| Suburban    |        6,461 |       1,435 |                            22.2% |                12.1% |
| Industrial  |        7,233 |       1,284 |                            17.8% |                10.8% |

![orders by zone](/images/orders_by_pickup_zone.png)
![slow prep by zone](/images/slow_prep_by_zone.png)

Residential accounts for the largest share of slow orders (30.3%), slightly above the CBD (30.0%). That is a volume effect: residential is the biggest zone with 15,786 orders, and its slow-order rate of 22.8% is close to the overall rate of about 23.8%. The CBD has fewer orders than residential yet almost the same number of slow ones.

**Insight:** The CBD is slow on both sides of the clock: about 10 minutes longer overall (5.4) and one in three orders slow-preparing. Residential is slow only because it is large. A CBD plan therefore needs a kitchen half as well as a road half.

---

### 5.10 Restaurant Load by Hour

The last question brings the hour of day, the main finding of 5.2, back to the kitchen. Operations found that the slow windows (8:00 to 10:00 and 18:00 to 21:00) are explained by traffic. Supply Q8 tests whether restaurant load peaks at the same hours, which would mean those hours are slow on both sides of the clock.

#### Supply Q8. When is restaurant load highest, and do delivery times rise at those hours?

Restaurant load follows the same two-peak shape as order volume. It is highest at **19:00 (12,659)**, followed by 13:00 (10,282), 18:00 (10,205), 20:00 (10,117), and 12:00 (9,140). These five hours account for about **39.5%** of all load, matching the hours that account for approximately 40% of orders in Ops Q1. Load is lowest between 00:00 and 04:00, at roughly 1,200 to 1,300 per hour, about a tenth of the 19:00 peak.

| Hour  | Restaurant Load | Avg. Delivery Time (min, from Ops Q2) |
| :---: | --------------: | ------------------------------------: |
| 19:00 |          12,659 |                                 87–88 |
| 13:00 |          10,282 |                                    81 |
| 18:00 |          10,205 |                                 87–88 |
| 20:00 |          10,117 |                                 87–88 |
| 12:00 |           9,140 |                                    81 |

Delivery time does not follow restaurant load. The two lunch hours (12:00 and 13:00) have some of the highest load of the day and are still fast. The morning window (08:00–10:00) is slow at 87–89 minutes, with load of approximately 5,100–6,500. At 09:00 and 10:00 (5,262 and 5,127), load is almost the same as at 15:00 and 16:00 (5,301 and 5,124), yet those afternoon hours maintain normal delivery times of 80–82 minutes.

![hourly load sum](/images/restaurant_load_by_hour.png)

**Insight:** Restaurant load rises and falls with demand, peaking at lunch and in the evening, but only the evening experiences slow deliveries. The slow windows are explained by traffic (Ops Q2), while restaurant load adds at most about 4 minutes (Supply Q4). Load tells us when restaurants are busiest, not which hours deliveries run late.

This closes the loop with Section 5.2: both departments' hour-of-day findings point to traffic as the primary explanation for slow delivery periods, rather than restaurant load alone.

---

### The Full Operations Dashboard

All of the Operations charts on one page, for reference.

![operations dashboard](/images/delivery_dashboard2.png)

---

## 6. Recommendations

| #   | Area                         | Recommendation                                                                                                                                                                              | Evidence                                                                                    |
| --- | ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| 1   | Joint: arrival-time promise  | Build the customer's arrival time from two parts: a kitchen estimate (by cuisine and number of items) plus a road estimate (by hour, traffic, weather and pickup zone). Test before rollout | Kitchen is about 24 of about 84 minutes; each part has its own drivers (5.1)                |
| 2   | Road: roster by hour         | Add capacity from 8:00 to 10:00 and 18:00 to 21:00, when traffic is worst. Lunch needs volume cover but not extra time allowance                                                            | Slow hours all run at traffic level 2; day of week varies under 4%                          |
| 3   | Joint: CBD plan              | Give CBD pickups their own plan: dispatch earlier, set longer arrival times or dedicate riders, and review the CBD restaurants that produce most of the 3,561 slow orders                   | CBD averages 93.8 min (about 10 above the next zone) and has a 32.4% slow-order rate        |
| 4   | Road: weather rule           | Storm and rain trigger a staffing uplift and a longer arrival-time promise, with radius reductions piloted in storms                                                                        | Storms +37 min (48%), rain +21.6 min; affects 5% and 18% of orders                          |
| 5   | Road: high-signal routes     | Where a route with 14 or more signals has an alternative, test it, after first checking how much of the effect is really CBD                                                                | Delivery rises from 79 to 84 min up to 13 signals to 88.8 to about 94.5 min from 14 signals |
| 6   | Kitchen: cuisine targets     | Set preparation targets by cuisine rather than one standard. Start with biryani and North Indian partners, with earlier order alerts or pre-preparation as pilots                           | Biryani 34 min and North Indian 30 min prep; together about 35% of orders                   |
| 7   | Kitchen: basket size         | Add about 2 minutes to the quoted preparation time for each extra item, so large orders are not flagged late when they are simply large                                                     | Preparation rises from 21 to 33 min from one item to seven                                  |
| 8   | Kitchen: load and rating     | Do not build speed rules around restaurant load or rating                                                                                                                                   | High load adds about 4 min; rating shows no steady link                                     |
| 9   | Joint: close the open checks | Complete Supply Q8 (load by hour) and the cross-checks listed in section 7 before changing partner staffing or opening hours                                                                | Q8 results pending; signal effect and CBD overlap not yet separated                         |

---

## 7. Data Notes and Limitations

- **Question numbering:** "Ops Q" and "Supply Q" refer to the Operations Manager's and Supply Manager's question lists in section 3. The two lists were written separately and overlap on hour and pickup zone only.
- **Correlations:** delivery time vs hourly order volume is +0.48; delivery speed vs hourly order volume is -0.49. Both are calculated across the 24 hours of the day, so each point is one hour, not one order. They are moderate, and Ops Q2 shows traffic explains the pattern better than volume does.
- **Weather averages:** the chart's dashed "Average: 90.8 min" line is the simple mean of the five weather categories, not the order-weighted average, so it is not comparable to the overall average of about 84 minutes.
- **Speed units:** the speed measure is reported as it appears in the dataset; the unit is not shown on the charts.
- **Slow-order definition:** the Supply Q7 output does not state what counts as a "slow" order (for example, preparation time above a threshold).Top 25%: Orders with the longest preparation times, which are classified as slow.
- **Load effect:** the load difference is small and consistent (about 4 minutes). A significance test would settle whether to call it significant.
- **Open cross-checks:** signal count and pickup zone may be linked (CBD routes may have more signals), so signal effects should be compared within a single zone. Cuisine mix by hour and by pickup zone has also not been checked, so the kitchen and road findings are reported side by side rather than separated statistically.
- **Association only:** these results show relationships, not causes.

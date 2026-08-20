# Profitability Performance Dashboard: FY23 to FY24

Electronics generates 40% of total revenue yet produces profit at less than half the efficiency of other categories - a 14% margin compared to 23%–34%. And it’s not a weak region or a slow month dragging the number down. The gap is consistent across every region and every month, which points to a structural issue, likely tied to pricing or cost, not something a regional or seasonal tweak will fix.

![Full dashboard](images/dashboard-full.png)


If you'd like to interact with the dashboard, the file can be found in `/power-bi`.

---

## 1. Background and Overview

This dashboard was built for a VP who needs to allocate next year’s merchandising and marketing budget based on profit contribution, not just sales volume.

In my analysis, revenue alone proved misleading; a category can post strong top‑line numbers while adding very little to the bottom line. That disconnect only becomes visible when the data is broken out by category, region, and time period; which is exactly the gap I designed this dashboard to close.

The questions guiding the work didn’t come first. They emerged after my initial pass through the data, once I understood the underlying story. From there, they became my framework for chasing the “why” behind the numbers and ensuring the final insights answered the stakeholder’s actual decision.

Questions we wanted answered:

* Which categories are truly generating profit, and which ones are only generating volume without meaningful contribution to the bottom line?
* Is the Electronics profit gap concentrated in a specific region, or is it consistent across the entire footprint?
* Does profit margin hold during the holiday surge, or does it erode when volume spikes?
* Based on the answers, where should next year’s merchandising and marketing investment actually go to maximize profit contribution?

## 2. Data Structure Overview

The dataset includes 200,000 transactions from January 2023 through December 2024. Each row represents a single sale with category, region, quantity, revenue, and profit captured at the transaction level.

Before analysis, I standardized and structured the data in Power Query to make sure every metric rolled up cleanly:

* Date standardization - Ensured all transactions aggregate correctly by year, month, and quarter.
* Proper margin calculation - Built profit margin as total profit divided by total revenue, avoiding skew from averaging row‑level percentages.
* Time fields - Added year, month, and quarter to support trend and seasonal analysis.

**Limits that shape the analysis**

* Customer tracking constraint - Customer names repeat across different individuals, so there’s no reliable way to identify repeat customers or measure loyalty. I intentionally excluded any customer‑level claims from this report.
* Electronics margin consistency - Profit margin in Electronics is nearly identical across laptops, phones, tablets, and other subcategories. That consistency signals the margin is set at the category level, not driven by product mix. All findings are scoped to the category level, where the signal is stable.

## 3. Executive Summary

Over FY23 and FY24, the business generated $142.4M in revenue and $31.5M in profit, a 22.2% overall margin, with revenue up 1.27% year over year.

The core finding is straightforward: Electronics is both our largest revenue driver and our weakest margin performer. It contributes 40% of total revenue ($57M) but converts at just 14%, less than half the margin of the other three categories, which range from 23% to 34%. At the opposite end, Accessories is the smallest revenue category at $10M, but the strongest converter at 34%.

This pattern holds everywhere. Electronics’ margin stays within a tenth of a percentage point across all four regions (14.0% to 14.1%), and overall company margin barely shifts across the year - 22.15% during the October–December peak versus 22.17% the rest of the year, even as revenue triples in that window. The business isn’t discounting its way through the holidays; margin stability confirms that.

**Suggestion for the VP:** 
Growing Electronics revenue won’t solve this. The issue isn’t volume - it’s conversion, how much of each sale actually turns into profit. The higher‑leverage move is understanding why Electronics converts so poorly (pricing, supplier cost, category‑level cost structure) and reconsidering how much of next year’s budget goes to Accessories, which already converts at the highest rate in the portfolio and currently receives the smallest share of investment.

## 4. Insight Deep Dive

### 4.1 Our biggest seller isn't our biggest earner

![Revenue and margin by category](images/category-revenue-margin.png)


Growing Electronics revenue won’t solve this. The issue isn’t volume - it’s conversion, how much of each sale actually turns into profit. The higher‑leverage move is understanding why Electronics converts so poorly (pricing, supplier cost, category‑level cost structure) and reconsidering how much of next year’s budget goes to Accessories, which already converts at the highest rate in the portfolio and currently receives the smallest share of investment.

### 4.2 It's not a regional problem

![Electronics margin by region](images/margin-by-region.png)


If Electronics’ weak margin were being driven by regional factors such as higher shipping costs in one market, more aggressive local pricing, or inconsistent execution, I would expect to see real variation across regions. I do not. East, West, South, and Centre all land within a tenth of a percentage point of each other, all at roughly fourteen percent. That consistency is the finding. Whatever is compressing Electronics’ margin is happening at the same rate everywhere, which rules out a regional promotion or a market specific pricing adjustment as the solution. This needs to be solved at the category level across the entire company.

### 4.3 The holiday rush doesn't cost us margin

![Revenue trend 2023 vs 2024](images/revenue-trend.png)


Revenue climbs sharply in the fourth quarter. November alone brought in $13.37M in 2023 and $13.90M in 2024, both roughly three times a typical month’s revenue of about $4.3M. In many retail businesses, that kind of spike comes with a margin cost because heavier discounting is used to move volume. That is not what is happening here. Margin holds in a tight band between 22.0% and 22.4% across the entire year, including the holiday period. The added volume is not being bought with lower prices, which means there is room to invest further into Q4 without that investment eating into profit once the underlying Electronics margin issue is addressed.

## 5. Recommendations

1. **Investigate why Electronics converts at such a low rate before investing further in selling more of it.** Electronics is already the top revenue category. The issue is not demand, it is conversion, and that points to pricing or supplier cost rather than a marketing problem.

2. **Shift a larger share of next year’s budget toward Accessories.** Accessories is the smallest category by revenue and the strongest by margin. It is also the most under‑invested category relative to its performance, which makes it a high leverage opportunity.

3. **Avoid treating the Electronics margin problem as a regional or seasonal issue.** The margin gap is consistent across every region and across the entire year. Any fix must be applied at the category level rather than through regional promotions or holiday pricing adjustments.

4. **Scale into Q4 once the Electronics margin issue is resolved.** The holiday spike is not being driven by lower margin, which means the seasonal volume itself is not the risk. The real risk is scaling the category that drives that spike before its underlying margin problem is fixed.

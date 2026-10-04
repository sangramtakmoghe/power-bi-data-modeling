# Power BI Data Modeling Project

Power BI data modeling project: turned 23 messy tables into a clean star schema with 6 facts, 6 dimensions, DAX measures and row-level security.

> This is my own solution, built by following the video step by step. Project idea and dataset credit: Baraa Khatib Salkini ([Data With Baraa](https://www.youtube.com/@datawithbaraa)).

## Why this project

Data modeling is the foundation of every dashboard and report. If the model is wrong, the numbers can't be trusted, however good the visuals look. This project taught me the basics and fundamentals of data modeling by taking a messy dataset and fixing it step by step.

## What I did

The project follows four phases, which I also wrote up in my handwritten notes (see the `notes` folder):

1. **Prepare & Explore**: understand the business, the entities, and which tables are dimensions and which are facts.
2. **Dimensions**: for each entity, collect its tables, combine them into one dimension, and clean it to the standards.
3. **Facts**: pick an event, read its grain, build the fact from the details, connect every dimension and test the numbers.
4. **Polish**: re-check the standards, add the date dimension, build measures, add row-level security and validate.

## The model

| Facts | Dimensions |
|---|---|
| `fact_sales` | `dim_customer` |
| `fact_order_process` | `dim_product` |
| `fact_inventory` | `dim_geo` |
| `fact_campaign_spend` | `dim_order_flags` (junk dimension) |
| `fact_promotion_coverage` (factless fact) | `dim_campaign` |
| `fact_sales_targets` | `dim_date` |

Supporting tables: `security` (user email to region) and `_measure` (measures).

- No two fact tables are connected directly; they share dimensions.
- `dim_geo` is used twice by `fact_sales` (ship-to city active, bill-to city inactive).

![Data model](images/model-view.png)

## Measures

| Measure | DAX |
|---|---|
| `total_sales` | `SUM(fact_sales[line_total])` |
| `total_orders` | `DISTINCTCOUNT(fact_sales[order_id])` |
| `total_active_customers` | `DISTINCTCOUNT(fact_sales[customer_id])` |
| `base_total_customers` | `COUNT(dim_customer[customer_id])` |
| `avg_order_to_pay` | `AVERAGE(fact_order_process[order_to_buy])` |

## Row-level security

Role **Regional Access** on `dim_customer`:

```
[region] = LOOKUPVALUE(security[region], security[user_email], USERPRINCIPALNAME())
```

Tested with *View as*: a regional user only sees their own region.

![Security test](images/security-test.png)

## Standards used

- English everywhere
- Lowercase names with underscores
- `fact_` and `dim_` prefixes for table names
- `_key` for keys created in the model, `_id` for keys from the source
- Readable names instead of cryptic codes
- Each word capitalized in text values

## Key lessons

- Understand the data and read the grain before changing anything.
- Every column must earn its place; if it doesn't help the report, drop it.
- Protect the numbers: know your totals and re-check after every change.
- Secure only what the requirements need.
- A star schema keeps a fact in the middle with dimensions around it.

## Next steps

- Connect `dim_date` to the facts and add time-based measures
- Add a report page on top of the model
- Keep practicing with more data modeling projects

## Files

```
powerbi/   Data_Modelling_with_baraa.pbix
notes/     handwritten notes (PDF)
images/    model view and security test screenshots
guide/     guide for the project (PDF)
```
## Connect

LinkedIn: https://www.linkedin.com/in/sangramtakmoghe/

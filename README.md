# Chile: CO2 Emissions (Production-based) vs Economic Growth (1990-2014)

Exploratory analysis of the relationship between Chile's CO2 emissions per 
capita and economic growth, using World Bank data.

## Background

Built as a self-directed learning project bridging my ESG/sustainability 
reporting background (CDP) with hands-on data analysis using pandas and 
matplotlib in Google Colab.

## Question

Does Chile's CO2 growth track economic growth? And does it matter whether 
"economic growth" means the *growth rate* or the *size* of the economy?

## Data sources

- **World Bank ESG Data** (Kaggle) — CO2 emissions and GDP growth rate by country/year
- **World Bank GDP Data** (Kaggle) — GDP level (current US$) by country/year

## Approach

```python
import pandas as pd

esg = pd.read_csv("ESGData.csv")
country_info = pd.read_csv("ESGCountry.csv")

real_countries = country_info[country_info["Region"].notna()]["Country Code"]
year_columns = [str(y) for y in range(1990, 2015)]

co2 = esg[esg["Indicator Name"] == "CO2 emissions (metric tons per capita)"]
co2_countries = co2[co2["Country Code"].isin(real_countries)]

chile = co2[co2["Country Name"] == "Chile"]
gdp_row = esg[(esg["Country Name"] == "Chile") &
               (esg["Indicator Name"] == "GDP growth (annual %)")]
```

Both CO2 and GDP data were reshaped from wide format (years as columns) to 
long format (years as rows) using `.melt()`, then merged on Country Name + Year 
to compare them side by side.

## Finding 1: CO2 vs GDP growth (annual %)

**No clear relationship.** GDP growth fluctuates year to year, as growth rates 
naturally do (recessions, rebounds), while CO2 emissions rose fairly steadily. 
Comparing a level (emissions) against a rate of change (growth %) doesn't 
reveal a meaningful pattern.

<img width="2008" height="1086" alt="image" src="https://github.com/user-attachments/assets/59751623-3e29-45fe-8d45-16ab839cfa48" />


## Finding 2: CO2 vs GDP level (current US$)

**Clear relationship.** Both metrics trend upward together across the full 
period, including a shared dip around 2008-2009 that coincides with the 
global financial crisis.

<img width="989" height="590" alt="image" src="https://github.com/user-attachments/assets/d579ca16-a5a4-4524-ba33-c28d47db0a36" />


## Conclusion

Chile's CO2 emissions per capita track much more closely with the *size* of 
its economy (GDP level) than with its year-to-year *growth rate*. This 
suggests emissions are tied to the overall scale of economic activity rather 
than short-term growth fluctuations, meaning decoupling emissions from 
growth (a common climate policy goal) would likely require structural 
changes to the energy/industrial base, not just short-term economic 
management.

This also reinforced a broader analytical lesson: the first comparison 
(growth rate) looked uncorrelated, while the second (GDP level) revealed a 
clear relationship hiding behind the same underlying question. Choosing the 
right variable to compare matters as much as finding data that's technically 
related.

Methodology note: Production-based vs. Consumption-based emissions

This analysis uses production-based (territorial) CO2 emissions, meaning emissions physically produced within Chile's borders. This is the standard measure used in most national inventories and by the World Bank.

It's important to note this is not the same as Chile's carbon footprint. Production-based emissions don't account for trade. If a country exports resource-intensive goods (e.g., copper, in Chile's case), emissions from producing those goods count against Chile, even if the demand driving that production comes from consumers elsewhere.

Consumption-based emissions would instead reallocate emissions to the country where goods are ultimately consumed, offering a different picture of responsibility.

Limitation: This analysis does not adjust for trade, so conclusions about Chile's "environmental performance" relative to GDP should be read with that caveat. A natural extension of this project would be comparing production-based vs. consumption-based emissions to see how much of Chile's footprint is externally driven demand vs. domestic consumption.

## Tools used

Python, pandas, matplotlib, Google Colab

## Files in this repo

- `co2_gdp_analysis.ipynb` - full notebook with code and outputs
- `chile_co2_vs_gdp.png` - CO2 vs GDP growth rate chart
- `chile_co2_vs_gdp_level.png` - CO2 vs GDP level chart

# Chile: CO2 Emissions vs. GDP (Production & Consumption-Based), 1990–2014

Exploratory analysis of the relationship between Chile's CO2 emissions per capita and economic growth, using World Bank data, extended to compare production-based and consumption-based emissions accounting.

## Background

Built as a self-directed learning project bridging my ESG/sustainability reporting background (CDP) with hands-on data analysis using pandas and matplotlib in Google Colab.

## Question

Does Chile's CO2 growth track economic growth? Does it matter whether "economic growth" means the *growth rate* or the *size* of the economy? And does it matter whether "emissions" means what Chile *produces* domestically or what it *consumes* as a country, accounting for trade?

## Data sources

- **World Bank ESG Data** (Kaggle): CO2 emissions (production-based) and GDP growth rate by country/year
- **World Bank GDP Data** (Kaggle): GDP level (current US$) by country/year
- **Global Carbon Project**, via [Our World in Data](https://ourworldindata.org/grapher/consumption-co2-per-capita): CO2 emissions (consumption-based) by country/year

## Approach

```python
import pandas as pd

esg = pd.read_csv("ESGData.csv")
country_info = pd.read_csv("ESGCountry.csv")
consumption_df = pd.read_csv("consumption-co2-per-capita.csv")

real_countries = country_info[country_info["Region"].notna()]["Country Code"]
year_columns = [str(y) for y in range(1990, 2015)]

co2 = esg[esg["Indicator Name"] == "CO2 emissions (metric tons per capita)"]
co2_countries = co2[co2["Country Code"].isin(real_countries)]

chile = co2[co2["Country Name"] == "Chile"]
gdp_row = esg[(esg["Country Name"] == "Chile") &
               (esg["Indicator Name"] == "GDP growth (annual %)")]

chile_consumption = consumption_df[consumption_df["entity"] == "Chile"]
```

CO2, GDP, and consumption-based emissions data were reshaped from wide format (years as columns) to long format (years as rows) using `.melt()`, then merged on Year to compare all three side by side.

## Finding 1: Production-based vs. Consumption-based emissions

This analysis uses two different ways of measuring CO2 emissions. **Production-based** (territorial) emissions count what Chile physically emits within its borders. This is the standard measure used in most national inventories. It doesn't account for trade: if Chile exports resource-intensive goods (e.g. copper), those production emissions count against Chile even when demand comes from consumers elsewhere. **Consumption-based** emissions instead reallocate emissions to the country where goods are ultimately consumed, giving a different picture of environmental responsibility. This data is sourced here from the Global Carbon Project.

Comparing the two: production and consumption-based emissions track closely from 1990 to 2007, with the gap oscillating within roughly ±0.15 tons per capita and no sustained direction. From 2008 onward, a persistent positive gap emerges. Chile consistently consumes more embedded carbon than it produces domestically, peaking around 2011-2012. Both metrics also dip sharply in 2008-2009, consistent with the financial crisis, with consumption-based emissions swinging more sharply than production (plausible, since consumption is more exposed to trade volume, which contracts faster than domestic production during a recession).

<img width="900" alt="Production vs Consumption CO2" src="https://github.com/agataczerwinska-cmd/chile-co2-vs-gdp-1990-2014/blob/main/production_vs_consumption_co2.png?raw=true">

Chile is a heavy exporter of upstream, energy-intensive commodities and an importer of finished manufactured goods. Those two flows roughly cancelled through the 1990s and early 2000s, which is why the lines sit on top of each other.

## Finding 2: CO2 vs GDP growth (annual %)

No clear relationship. GDP growth fluctuates year to year, as growth rates naturally do (recessions, rebounds), while CO2 emissions rose fairly steadily. Comparing a level (emissions) against a rate of change (growth %) doesn't reveal a meaningful pattern. Confirmed statistically: **r = -0.42** (weak, inverse).

<img width="900" alt="CO2 vs GDP growth" src="https://github.com/agataczerwinska-cmd/chile-co2-vs-gdp-1990-2014/blob/main/co2_vs_gdp_growth.png?raw=true">

## Finding 3: CO2 vs GDP level (current US$)

Clear relationship. Both metrics trend upward together across the full period, including a shared dip around 2008-2009 that coincides with the global financial crisis. Confirmed statistically: **r = 0.85** (strong positive).

<img width="900" alt="CO2 vs GDP size" src="https://github.com/agataczerwinska-cmd/chile-co2-vs-gdp-1990-2014/blob/main/co2_vs_gdp_size.png?raw=true">

## Statistical validation

To test whether the visual relationships above hold up statistically, and aren't just an artifact of chart scaling, correlation coefficients were calculated and plotted against a scatter view:

<img width="900" alt="CO2 vs GDP correlation scatter" src="https://github.com/agataczerwinska-cmd/chile-co2-vs-gdp-1990-2014/blob/main/co2_vs_gdp_scatter.png?raw=true">

## Conclusion

Chile's CO2 emissions per capita track much more closely with the *size* of its economy (GDP level, r=0.85) than with its year-to-year *growth rate* (r=-0.42). This suggests emissions are tied to the overall scale of economic activity rather than short-term growth fluctuations. Decoupling emissions from growth (a common climate policy goal) would likely require structural changes to the energy/industrial base, not just short-term economic management.

Separately, Chile's production and consumption-based emissions were closely matched for most of the study period, suggesting Chile wasn't meaningfully "offshoring" or "importing" emissions responsibility through trade, unlike economies with heavy manufacturing offshoring. That changed after 2008, when Chile shifted into a sustained pattern of consuming more embedded carbon than it produces. That shift is worth investigating further, potentially tied to changing trade patterns or import composition post-crisis.

This also reinforced a broader analytical lesson: the first comparison (growth rate) looked uncorrelated, while the second (GDP level) revealed a clear relationship hiding behind the same underlying question. Choosing the right variable to compare matters as much as finding data that's technically related, and the same is true of choosing which *definition* of emissions to compare, not just which economic variable.

**Limitation**: The growth-rate correlation (r=-0.42) is based on 25 annual data points and is sensitive to extreme single-year swings (e.g. 2009's sharp GDP contraction alongside a CO2 dip), so it's worth treating as directional rather than definitive. Consumption-based emissions data also isn't available for all countries globally, since it requires detailed international trade data. Chile's coverage happens to be complete for 1990-2014, but this shouldn't be assumed for other countries without checking.

## Tools used

Python, pandas, matplotlib, Google Colab

## Files in this repo

- `Chile_analysis_co2_gdp.ipynb`: full notebook with code and outputs
- `ESGData.csv`, `ESGCountry.csv`, `GDP by Country.csv`, `co2_clean.csv`: source data files
- `production_vs_consumption_co2.png`: production vs. consumption-based CO2 chart
- `co2_vs_gdp_growth.png`: CO2 vs GDP growth rate chart
- `co2_vs_gdp_size.png`: CO2 vs GDP level chart
- `co2_vs_gdp_scatter.png`: correlation scatter plots (GDP size vs. growth rate)

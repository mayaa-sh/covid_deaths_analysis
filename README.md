# covid_deaths_analysis (JHU CSSE)

Exploratory analysis and forecasting of global COVID-19 deaths using the Johns Hopkins CSSE time-series data (22 Jan 2020 – 9 Mar 2023, 201 countries).

The notebook answers six questions:

1. Which countries had the most total deaths?
2. How does the ranking change after adjusting for population (deaths per 100k)?
3. How did daily deaths evolve, and when did each country peak?
4. Which months were the worst worldwide?
5. Which countries were rising or falling at the end of the data?
6. Can daily deaths be forecasted better than a naive baseline?

## Key findings

- **Total deaths:** US (1.12M), Brazil (699k) and India (531k) lead. The top 10 countries account for 58.7% of reported deaths.
- **Per capita:** the ranking reverses. Peru (~666 deaths per 100k) and Eastern European countries (Bulgaria, Hungary, Bosnia and Herzegovina, North Macedonia) were hit hardest; the US is not in the top 10.
- **Timing:** peaks were not synchronized. Italy and France peaked in spring 2020, most others in winter 2020–21 or spring 2021, Russia in November 2021.
- **Worst months worldwide:** January 2021 (~422k deaths), then May and April 2021 (~379k each).
- **Forecasting:** for the US with a 28-day horizon, a naive "last value" baseline (MAE 96.9) beat a Ridge model on 14 lag features (MAE 122.8). A more complex model did not help on this short, smooth, declining window.

## Methods

- **Cleaning:** provinces summed to country level, wide-to-datetime index, daily deaths via `diff()` of cumulative counts, negative values (data corrections) clipped to 0, 7-day moving average.
- **Normalization:** deaths per 100k using JHU's own population lookup table; countries under 1M people excluded from the per-capita ranking.
- **Forecasting:** strict time-based split (last 28 days as test), recursive multi-step forecast, compared against two naive baselines using MAE and RMSE.

## How to run

1. Clone the repository and install dependencies:

   ```bash
   git clone https://github.com/<mayaa-sh>/covid-deaths-analysis.git
   cd covid-deaths-analysis
   pip install -r requirements.txt
   ```

2. Download these two files from the [JHU CSSE COVID-19 repository](https://github.com/CSSEGISandData/COVID-19) and place them in `data/`:
   - `csse_covid_19_data/csse_covid_19_time_series/time_series_covid19_deaths_global.csv`
   - `csse_covid_19_data/UID_ISO_FIPS_LookUp_Table.csv` (only needed for the per-capita analysis)

3. Open and run the notebook:

   ```bash
   jupyter notebook notebooks/covid_deaths_analysis.ipynb
   ```

If your files are elsewhere, change `DATA_DIR` in the first code cell.

## Limitations

- Reported deaths depend on each country's testing and reporting practices (under-reporting, delays, definition changes), so cross-country comparisons are approximate.
- Five countries could not be matched to a population and are missing from the per-capita analysis.
- Reporting changes (e.g. UK, Taiwan) create artificial zeros in the final weeks of the data.
- The forecast was evaluated on one country and one 28-day window, so the result may not generalize.

## Data source

Dong E, Du H, Gardner L. *An interactive web-based dashboard to track COVID-19 in real time.* Lancet Infect Dis, 2020. Data: [CSSEGISandData/COVID-19](https://github.com/CSSEGISandData/COVID-19) (Johns Hopkins University CSSE). Please check the source repository for its terms of use.

## License

Code is released under the MIT License (see `LICENSE`). The data belongs to its original authors.

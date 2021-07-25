# PyBer_Analysis

## Overview of Project
Using the pandas and matplotlib libraries within python, visualizing differences in fare and driver data between urban, suburban, and rural cities.  Following this, the data is reviewed to determine the weekly fares as a function of city type.

To dervive the summary data, pandas is used to merge the two datasets provided and determine for each city type the total rides, number of drivers, sum of all fares, average fare per ride, and average fare per driver:

    pyber_data_df = pd.merge(ride_data_df, city_data_df, how="left", on=["city", "city"])
    ride_counts = pyber_data_df.groupby(["type"]).count()["ride_id"]
    driver_counts = city_data_df.groupby(["type"]).sum()["driver_count"]
    fares = pyber_data_df.groupby(["type"]).sum()["fare"]
    avg_ride_fares = fares / ride_counts
    avg_driver_fares = fares/driver_counts

Then, to find the weekly breakdown of fares by city type, we first break out the fare data as a function of date and city type; rearrange the data setting the date as the rows and the columns as the city type, with fares being displayed in the cells; incomplete data from May is exclused from the analysis; finally the data is restructured from hourly data into weekly data:

    fares_by_date_df = pyber_data_df.groupby(['type','date']).sum()['fare']
    fares_by_date_df= fares_by_date_df.reset_index()
    fares_by_date_df_pivot = fares_by_date_df.pivot(index="date",columns="type",values="fare")
    jan_apr_only_df = fares_by_date_df_pivot.loc[(fares_by_date_df_pivot.index >= "2019-01-01") & (fares_by_date_df_pivot.index <= "2019-04-29")]
    jan_apr_only_df.index = pd.to_datetime(jan_apr_only_df.index)
    jan_apr_byweek_df = jan_apr_only_df.resample("W").sum()

## Results
Running the summary data through the code reveals the following summary:

![PyBer City Type Summary](https://github.com/rscalise88/PyBer_Analysis/blob/main/analysis/PyBer_summary.PNG)

The data show that, as we might expect, the total number of drivers, rides, and fares increases as the population increases.  However, the average fare per ride seems to decrease as population increases.  The effect is even more prnounced when reviewing the average fare per driver data, where the difference in fares per driver between rural and urban areas is dramatic.

One datapoint of interest is that the number of drivers in urban areas exceeds the number of drivers, implying that some drivers are on the clock, but may never complete a ride.


Following this, when we determine the weekly fares as a function of city type, we see the following:
![Weekly Fares by City Type](https://github.com/rscalise88/PyBer_Analysis/blob/main/analysis/PyBer_fare_summary.png)

The data seem to indicate that regardless of city type, fares do not vary significantly as a function of time.  However, as expected, the weekly fares taken in in urban areas exceed those in rural and suburban areas; sometimes more than 9x as much as the rural areas.

## Summary 
The very high rates seen in rural areas could be responsible for the significantly lower ridership.  It might be worth considering lower the rates in rural communities to attempt to increase ridership or conduct additional analysis to determine what other factors might be influencing this such as overall driver availability and distance traveled per ride.
The number of drivers exceeds the number rides in urban areas.  Consider determining either what more could be done to increase ridership in urban areas to have demand meet the supply, or consider capping the number of drivers that could be active in a given urban area at a certain level to increase fare per driver metrics.
Despite suburban areas being home to less than 1/5 of of the total drivers in the analysis area, they make up a much larger portion of the total fares.  Further investment into suburban areas could be advised.  In addition, research into what the drives the relative success of suburban areas could be conducted and applied to other other two types of cities to improve performance.

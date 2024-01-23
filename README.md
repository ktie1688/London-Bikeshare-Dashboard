# London Bikeshare Trend Analysis

## Context
Cycling is a popular mode of transportation in London ([TSGB - Transport Statistics](https://www.gov.uk/government/statistics/transport-statistics-great-britain-2021/transport-statistics-great-britain-2021#overview-of-transport-statistics)) that serves as an alternative to highly congested automobile traffic and crowded subway lines. In light of the COVID pandemic and push for environmental sustainability, bike sharing offers a private form of transportation while reducing carbon emission levels, leading to its growing usage for daily commutes.

## Objective
Clean and analyze a dataset on bike shares in London. Then create visualizations in an interactive dashboard that shows bike sharing trends between 2015 - 2017 to predict future bike shares.

## Tools/Resources 
* Bike Share Dataset by [HRISTO MAVRODIEV](https://www.kaggle.com/datasets/hmavrodiev/london-bike-sharing-dataset), powered by TFL Open Data. Contains OS data © Crown copyright and database rights 2016 and Geomni UK Map data © and database rights [2019]
* Kaggle API, Pandas
* Tableau

## Methodology
First off, the dataset was loaded into a [Jupyter Notebook](file) to begin the data cleaning and mapping process.
### Data Cleaning and Mapping
* Renamed columns to improve clarity
* Percentage values transformed to actual percent values (i.e. between 0 and 1) for later measures in Tableau.
* Mapped weather and season codes to the dataset

Exported the resulting Dataframe to a CSV file for visualization in Tableau.
## Tableau - Data Visualization
### Moving Average
In order for users to identify trends, I opted to create a moving average line chart with interactive viewing features.
* Created 2 user parameters: ```Moving Average Period``` and ```Moving Average Unit```
* To use these parameters, created a measure that applies a DATETRUNC() function to the ```Time``` field
```
DATETRUNC([Moving Average Period], [Time])
```
* Next, made a line chart based on the ```Moving Average Period``` and the ```Count``` of bike shares
* Created a set for ```Moving Average Period``` to be filled with an IF() statement:
```
{MIN(IF[Moving Average Period Set] THEN [Moving Average Period] END)}
{MAX(IF[Moving Average Period Set] THEN [Moving Average Period] END)}
```
This creates an empty set that populates itself once the user applies an input that specifies the start and end month. 
* Addded visual feedback with a calculated field ```MAP In Range``` to highlight selected data points a different color based on user inputs:
```
[Moving Average Period] >= [Min Month]
AND
[Moving Average Period] <= [Max Month]
```
* Made another calculated field ```Moving Average Calculation``` that converts the ```Count``` of bike shares to a moving average:
```
WINDOW_AVG(SUM([Share Count]), -[Moving Average Unit] + 1, 0)
```
Resulting line chart is dynamically loads the moving average of bike shares based on user specified time frames.
### Total Bike Rides Card
* Created another calculated field ```In Range Shares``` that applies our ```MAP In Range``` to output the sum total of bike shares in that time frame:
```
{sum(INT([MAP In Range]) * [Share Count])}
```
Now, we have a card that will show the total number of bike shares based on a range of highlighted data points.
### Wind Speed & Temperature Heatmap
To further inform our bike sharing predictions, I chose to make a heatmap that will show number of bike rides based on differing temperatures and wind conditions.
* Due to the large number of data in ```real_temp_c``` and ```wind_speed_kph```, it's necessary to bin them
* Added ```Count``` of bike shares and data labeling
Finished heatmap updates based on user inputs.
### Tooltips
As an additional layer of detail, I created tooltip charts that show bike usage based on weather categories and time of day when a user hovers over a data point.
#### Weather Split
Bar chart indicating weather conditions and number of bike shares.
#### Hour Split
Bar chart indicating time of day and number of of bike shares.
## Data Insights
Bike shares spike between 7-9 AM and spike again between 5-7 PM, suggesting it is a popular mode of commuting to work. The service peaks during the summer, gradually decreasing to a low in the winter and climbing back up during the spring. Ideal weather appears to be 15 degrees Celsius with gentle to moderate winds with significantly less usage in the rain. Potential actions would be to apply cost cutting measures during months of lower usage or to add rain poncho dispensaries by bike stations.

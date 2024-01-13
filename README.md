---
title: "Air_Pollution"
author: "Aravind"
date: "`r Sys.Date()`"
output:
  pdf_document: default
  word_document: default
  html_document: default
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

### Set up the environment.

```{r}
setwd('D:/Project/Air pollution')
```

### Install the Packages.
* Note: repos is used as a workaround to eliminate the error: "contrib.url()" while knitting.
* Advisable to use your country CRAN mirror repos for quicker knitting.
```{r Package} 
install.packages("tidyverse",repos = "https://cran.icts.res.in/")
install.packages("ggplot2", repos = "https://cran.icts.res.in/")
```

### ADD pacakages to the library.
```{r}
library(tidyverse)
library(ggplot2)
```

### Import Dataset.
* NOTE: set the right directory.
```{r}
Air_Pollution <- read.csv("air_pollution.csv")
```

### Check the structure and Summary of the Dataset.
```{r}
str(Air_Pollution)
```

Summary statistics for air pollution levels in each year.

```{r}
summary(Air_Pollution)
```

### Rename the column name of a dataset.
#### Pipe Operator.
```{r}
Air_Pollution <- Air_Pollution %>%
  rename(Year_2017 = X2017, Year_2018 = X2018, Year_2019 = X2019, Year_2020 = X2020, Year_2021 = X2021, Year_2022 = X2022, Year_2023 = X2023)
```
### Highest polluted City from 2017 - 2023.
* Similarly create data frame for remaining years.
```{r}
Highest_polluted_City_2017 <- Air_Pollution %>%
  select(country, city, Year_2017) %>%
  slice(which.max(Year_2017))

Highest_polluted_City_2018 <- Air_Pollution %>%
  select(country, city, Year_2018) %>%
  slice(which.max(Year_2018))

Highest_polluted_City_2019 <- Air_Pollution %>%
  select(country, city, Year_2019) %>%
  slice(which.max(Year_2019))

Highest_polluted_City_2020 <- Air_Pollution %>%
  select(country, city, Year_2020) %>%
  slice(which.max(Year_2020))

Highest_polluted_City_2021 <- Air_Pollution %>%
  select(country, city, Year_2021) %>%
  slice(which.max(Year_2021))

Highest_polluted_City_2022 <- Air_Pollution %>%
  select(country, city, Year_2022) %>%
  slice(which.max(Year_2022))

Highest_polluted_City_2023 <- Air_Pollution %>%
  select(country, city, Year_2023) %>%
  slice(which.max(Year_2023))
```

### Least polluted City from 2017 - 2023.
* Similarly create data frame for remaining years.
```{r}
Least_polluted_City_2023 <- Air_Pollution %>%
  select(country, city, Year_2023) %>%
  slice(which.min(Year_2023))
```

### Top 10 Highest polluted Country from 2017 - 2023.
* Similarly create data frame for remaining years.
```{r}
Highest_polluted_Country_2023 <- Air_Pollution %>%
  group_by(country) %>%
  summarise(Pollution_level_2023 = sum(Year_2023, na.rm = TRUE)) %>%
  arrange(desc(Pollution_level_2023)) %>%
  head(10)

Highest_polluted_Country_2022 <- Air_Pollution %>%
  group_by(country) %>%
  summarise(Pollution_level_2022 = sum(Year_2022, na.rm = TRUE)) %>%
  arrange(desc(Pollution_level_2022)) %>%
  head(10)

Highest_polluted_Country_2021 <- Air_Pollution %>%
  group_by(country) %>%
  summarise(Pollution_level_2021 = sum(Year_2021, na.rm = TRUE)) %>%
  arrange(desc(Pollution_level_2021)) %>%
  head(10)

Highest_polluted_Country_2020 <- Air_Pollution %>%
  group_by(country) %>%
  summarise(Pollution_level_2020 = sum(Year_2020, na.rm = TRUE)) %>%
  arrange(desc(Pollution_level_2020)) %>%
  head(10)

Highest_polluted_Country_2019 <- Air_Pollution %>%
  group_by(country) %>%
  summarise(Pollution_level_2019 = sum(Year_2019, na.rm = TRUE)) %>%
  arrange(desc(Pollution_level_2019)) %>%
  head(10)

Highest_polluted_Country_2018 <- Air_Pollution %>%
  group_by(country) %>%
  summarise(Pollution_level_2018 = sum(Year_2018, na.rm = TRUE)) %>%
  arrange(desc(Pollution_level_2018)) %>%
  head(10)

Highest_polluted_Country_2017 <- Air_Pollution %>%
  group_by(country) %>%
  summarise(Pollution_level_2017 = sum(Year_2017, na.rm = TRUE)) %>%
  arrange(desc(Pollution_level_2017)) %>%
  head(10)
```

### Combining all the Highest polluted cities into a single data frame.
Note: "Highest_polluted_City_" is a constant string that serves as a prefix for each data frame name.

* Step 1 - Combine all the data frame names to a string using paste0.

```{r}
Combine_data_frame <- paste0("Highest_polluted_City_", 2017:2023)
```

From the previous step we have all the list of data frame names stored in a vector "Combine_data_frame" as a character datatype and not as a dataframe with rows and columns.

Now to create multiple data frames as a list stored in "Combine_data_frame" into a single dataframe.

* Step 2 - Use mget
```{r}
Retrieve_data_frame <- mget(Combine_data_frame)
```

* Step 3- Use Bind_rows() to retrieve all objects and variables from the list of data frames in "Retrieve_data_frame".

```{r}
Highest_polluted_Cities <- bind_rows(Retrieve_data_frame)
glimpse(Highest_polluted_Cities)
```
### Combining all the Highest polluted countries into a single data frame.
Follow the similar procedures as above.
```{r}
Country_df_list <- paste0("Highest_polluted_Country_", 2017:2023)
Retrieve_df_country <- mget(Country_df_list)
Top_10_Highest_polluted_countries <- bind_rows(Retrieve_df_country)
```

To View how many countries were in the Top 10

```{r}
print(Top_10_Highest_polluted_countries$country)
```
As you can see there are countries that is listed more than once, which is due to concatenation of all the 
data frames into single Top 10 df.

To View Unique/Distinct country.
```{r}
unique(Top_10_Highest_polluted_countries$country)
```
so there are 15 countries in the TOP 10 highest polluted countries df.

### Air Quality Correaltion Matrix Year 2017-2023.

The "use = complete.obs" argument is used to exclude missing values while calculating correlations.
```{r}
selected_columns <- Air_Pollution[, c("Year_2017", "Year_2018", "Year_2019", "Year_2020", "Year_2021", "Year_2022", "Year_2023")]
correlation_matrix <- cor(selected_columns, use = "complete.obs")
print(correlation_matrix)
```
### Tempearture correlation under heat map.
```{r}
heatmap(correlation_matrix)
```

### Adding colour palette to the HeatMap.
* To know maxColorValue

The below code will give 255 shades of color pallette based on Grey and Purple.
.
```{r}
my_colors <- colorRampPalette(c("grey", "Purple"))
```

I have choose max color to 49 as we have 49 objection in correlation matrix = 7X7.
```{r}
correlation_matrix %>%
  heatmap(correlation_matrix,col = my_colors(49))
```
To Remove the dendogram.

In this correlation matrix we have 2 most correlated years - 2022 and 2023 which means they have strong similarity in air quality measurements.
```{r}
correlation_matrix %>%
  heatmap(correlation_matrix, col = my_colors(49), Rowv = NA, Colv = NA)
```
### Presenting Air Quality Correlation using Pairs.

* Step 1: Choose your colour pallette.
```{r}
my_colors <- colorRampPalette(c("Red","Purple","Grey"))
```

* Step 2: use the function pairs.
** [3:9] - is the column index of the air_pollution dataset i.e from 3,4,5,6,7,8 & 9.

```{r}
pairs(Air_Pollution[3:9],
      main = "Air Quality Correlation",
      pch = 21,
      bg = my_colors(7),
      col = my_colors(7))
```

As you can see years - 2022 and 2023 have similar pattern which means they have strong similarity in air quality measurements.

### Air Quality Distribution of Top 15 Higest Polluted countries using GGPLOT.

Step 1: To rename the column names to have an aesthetic data viz.
```{r}
Top_10_Highest_polluted_countries <- Top_10_Highest_polluted_countries %>%
  rename(
    Year_2017 = Pollution_level_2017,
    Year_2018 = Pollution_level_2018,
    Year_2019 = Pollution_level_2019,
    Year_2020 = Pollution_level_2020,
    Year_2021 = Pollution_level_2021,
    Year_2022 = Pollution_level_2022,
    Year_2023 = Pollution_level_2023
  )
```

To analyse the air quality distribution of Top 15 countries using geom_bar function we need to reshape the df to 3 variables, by grouping all the Year_2017 to Year_2023 under one column "Year" and values to "pollution Level".
```{r}
Top_10_Highest_polluted_countries <- Top_10_Highest_polluted_countries %>%
  pivot_longer(cols = starts_with("Year_"), names_to = "Year", values_to = "Pollution_level")
```

### Ggplot using geom_bar
* In general geom_bar using only one axis for it visualization.
* Here I have used x and y axis, so the "stat = "identity" is used to set heights of the bars.
* theme_minimal is used to remove gridlines.
* hjust is Horizontal justification changing its number will make the element text appear away from the axis.
```{r}
ggplot(Top_10_Highest_polluted_countries, aes(x = Year, y = Pollution_level, fill = country)) +
  geom_bar(stat = "identity", color = "black", linewidth = 0.2, na.rm = TRUE) + facet_wrap(~country) +
  labs(title = "Air Quality Distribution of Top 15 Countries (2017-2023)") +
  theme_minimal() + theme(axis.text.x = element_text(size = 7, angle = 45, hjust = 1))
```



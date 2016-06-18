Twin Cities Minnesota Demographics
========================================================
author: Bill Rowe
date: 6/17/2016
autosize: true

Twin Cities, MN Metropolitan Statistical Area
========================================================

This Shiny app uses choropleth maps to explore the demographics of the Twin Cities Metropolitan Statistical Area.  The Twin Cities MSA that includes 10 counties in Minnesota and 2 counties in western Wisconsin (separated by the St. Croix River). A choropleth map (from Greek <U+03C7><U+03CE><U+03C1><U+03BF> ("area/region") + p<U+03BB><U+03AE><U+03B8><U+03BF><U+03C2> ("multitude")) is a thematic map.  Areas are colored in proportion to the value of a statistic like per-capita income or percent Hispanic as examples.

Metropolitan Statistical Areas [Metropolitan Statistical Area (MSA)](https://en.wikipedia.org/wiki/Metropolitan_statistical_area) are defined by the Census Bureau. The regions in the images are the Census Bureau's approximation ([Zip Code Tabulated Areas (ZCTAS)](https://www.census.gov/geo/reference/zctas.html)) of postal zip codes.  All the data is from the 2013 [American Community Survey (ACS)](http://www.census.gov/acs/www/) accessed via the [choropleth](https://cran.r-project.org/web/packages/choroplethr/choroplethr.pdf) package created by [Ari Lamestein](http://www.arilamstein.com/).

The R code (server.R, ui.R and Top.md) for this app can be found on GitHub at https://github.com/wer61537/shiny_app.  The Shiny app can be found at https://wer61537.shinyapps.io/shiny_app/.

<div align="center">
<img src="app.png" width=1067 height=800>
</div>

How to Use the Maps Tab
========================================================

The ***Maps*** tab shows the distribution of a demographic statistic across the statistical area. The drop down can be used to choose the demographic that will be presented on a map of the Twin Cities MSA.  The following map shows the distribution of different ethnic classes across the Twin Cities by Zip Code.  Code to generate this is shown on the last slide.

The map imply geographical segregation of ethnic groups within the Twin Cities MSA.  The African American ethnic group clusters in the ZCTAs at the center of the Minneapolis and St. Paul.  This is also true for the hispanic and asian populationns (mostly Hmong and Vietnamese).  The "white"" population clusters in ZCTAs outside the center of the cities.

As the page loads or when the selected demographic changes, the map of the MSA for that demographic changes.


<div align="center">
<img src="ethnic_distribution.png" width=1067 height=800>
</div>

How to Use the Plots Tab
========================================================

The ***Plots*** tab creates scatter plots of the demographics versus other other selected demographics.  As the page loads or as different demographics are selected in the two drop down selectors, the graph is updated. 

The following is a plot of the fraction of white residents vs the fraction of black residents.  This seems to mirror the geographic separation of the "white" and other ethnic groups.

<div align="center">
<img src="white_vs_black.png" width=1067 height=800>
</div>


Code to Generate Racial Demographics Map
========================================================
```r
library(gridExtra)
library(ggplot2)
library(mapproj)
library(choroplethr)
library(choroplethrZip)

data(df_zip_demographics)
demographics = colnames(df_zip_demographics)[2:9]

tc_zips<-zip.regions[zip.regions$cbsa == "33460", ]
tc_data<-df_zip_demographics[df_zip_demographics$region %in% tc_zips$region,]


# highlight a zcta
highlight_zip = function(zip)
{
  library(choroplethrZip)
  data(zip.map)
  df = zip.map[zip.map$region %in% zip, ]
  geom_polygon(data=df, aes(long, lat, group=group), color="yellow", fill=NA, size=0.5)
}

df_zip_demographics$value = df_zip_demographics$percent_white
choro_white = zip_choropleth(df_zip_demographics, num_colors=1, msa_zoom="Minneapolis-St. Paul-Bloomington, MN-WI") +
  highlight_zip("55422") +   ggtitle("Twin Cities MSA ZCTAs\n Percent White") +   coord_map()


df_zip_demographics$value = df_zip_demographics$percent_black
choro_black = zip_choropleth(df_zip_demographics, num_colors=1, msa_zoom="Minneapolis-St. Paul-Bloomington, MN-WI") +
  highlight_zip("55422") +   ggtitle("Twin Cities MSA ZCTAs\n Percent Black") +   coord_map()



df_zip_demographics$value = df_zip_demographics$percent_asian
choro_asian = zip_choropleth(df_zip_demographics, num_colors=1, msa_zoom="Minneapolis-St. Paul-Bloomington, MN-WI") +
  highlight_zip("55422") +   ggtitle("Twin Cities MSA ZCTAs\n Percent Asian") +   coord_map()


df_zip_demographics$value = df_zip_demographics$percent_hispanic
choro_hispanic = zip_choropleth(df_zip_demographics, num_colors=1, msa_zoom="Minneapolis-St. Paul-Bloomington, MN-WI") +
  highlight_zip("55422") +   ggtitle("Twin Cities MSA ZCTAs\n Percent Hispanic") +   coord_map()


grid.arrange(choro_white, choro_black,choro_asian,choro_hispanic,nrow=2,ncol=2)
```
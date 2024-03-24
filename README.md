# Carbon Emission Analysis

A project to look into carbon footprints of different products

## An overview of the data

Select first 5 rows from each table to take a quick look at the data

*product_emissions* table
```
SELECT * FROM product_emissions
LIMIT 5;
```
|id|company_id|country_id|industry_group_id|year|product_name|weight_kg|carbon_footprint_pcf|upstream_percent_total_pcf|operations_percent_total_pcf|downstream_percent_total_pcf| 
| ------------: | ----------: | ----------: | -----------------: | ----: | ---------------------------------------------------------------: | ---------: | --------------------: | --------------------------: | ----------------------------: | ----------------------------: | 
|10056-1-2014|82|28|2|2014|Frosted Flakes(R) Cereal|0.7485|2|57.50|30.00|12.50| 
|10056-1-2015|82|28|15|2015|"Frosted Flakes, 23 oz, produced in Lancaster, PA (one carton)"|0.7485|2|57.50|30.00|12.50| 
|10222-1-2013|83|28|8|2013|Office Chair|20.68|73|80.63|17.36|2.01| 
|10261-1-2017|14|16|25|2017|Multifunction Printers|110|1488|30.65|5.51|63.84|
|10261-2-2017|14|16|25|2017|Multifunction Printers|110|1818|25.08|4.51|70.41|    

*industry_groups* table
```
SELECT * FROM industry_groups
LIMIT 5;
```
|id|industry_group| 
| --: | ----------------------------------------------------------------------: | 
|1|"Consumer Durables, Household and Personal Products"| 
|2|"Food, Beverage & Tobacco"| 
|3|"Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber"| 
|4|"Mining - Iron, Aluminum, Other Metals"| 
|5|"Pharmaceuticals, Biotechnology & Life Sciences"| 

*companies* table
```
SELECT * FROM companies
LIMIT 5;
```
|id|company_name| 
| --: | -----------------------------: | 
|1"Autodesk, Inc."| 
|2|"Casio Computer Co., Ltd."| 
|3|"Cisco Systems, Inc."| 
|4|"CNX Coal Resources, LP"| 
|5|"Coca-Cola Enterprises, Inc."| 

*countries* table
```
SELECT * FROM countries
LIMIT 5;
```
|id|country_name| 
| --: | ------------: | 
|1|Australia| 
|2|Belgium| 
|3|Brazil| 
|4|Canada| 
|5|Chile|

## Research

#### What product contributes the most to carbon emissions?
Get the names of 5 products that produce the highest amount of carbon emissions and their respective carbon footprint
```
SELECT product_name, carbon_footprint_pcf
FROM product_emissionsORDER BY carbon_footprint_pcf DESC
LIMIT 5;
```
|product_name|carbon_footprint_pcf| 
| ------------------------------------------------------------------: | --------------------: | 
|Wind Turbine G128 5 Megawats|3718044| 
|Wind Turbine G132 5 Megawats|3276187| 
|Wind Turbine G114 2 Megawats|1532608| 
|Wind Turbine G90 2 Megawats|1251625| 
|Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.|191687|

The product that contributes the most to carbon emissions is the Wind Turbine G128 5 Megawats. However, it is also interesting to note that the 4 products with the largest carbon footprints in the dataset are all wind turbines.

#### Which are the industry groups with the highest contribution to carbon emissions?
Join product_emissions with industry_groups, then get the names along with number of products, carbon footprints and percentage contribution of each production stage of the 5 groups with the largest carbon footprint
```
SELECT industry_groups.industry_group,
	   COUNT(*) AS number_of_products,
	   product_emissions.carbon_footprint_pcf, 
	   product_emissions.upstream_percent_total_pcf, 
	   product_emissions.operations_percent_total_pcf, 
	   product_emissions.downstream_percent_total_pcf
FROM product_emissions JOIN industry_groups
ON product_emissions.industry_group_id = industry_groups.id
GROUP BY industry_groups.industry_group
ORDER BY product_emissions.carbon_footprint_pcf DESC
LIMIT 5;
```
|industry_group|number_of_products|carbon_footprint_pcf|upstream_percent_total_pcf|operations_percent_total_pcf|downstream_percent_total_pcf| 
| ------------------------------------------------: | --------------------: |  --------------------: | ------------------------------------------------: | ------------------------------------------------: | ------------------------------------------------: | 
|Automobiles & Components|73|21725|18.63|5.75|75.62| 
| "Pharmaceuticals, Biotechnology & Life Sciences"|3| 15197| 2.95| 0.64| 96.41| 
| Chemicals|32| 10245| N/a (product with insufficient stage-level data)| N/a (product with insufficient stage-level data)| N/a (product with insufficient stage-level data)| 
| Materials|100| 10000| N/a (product with insufficient stage-level data)| N/a (product with insufficient stage-level data)| N/a (product with insufficient stage-level data)|  
| Media|15| 9438| 64.62| 0.57| 34.81| 

The industry with the largest carbon footprint is Automobiles & Components with 21725 CO2e, followed surprisingly by Pharmaceuticals with 15197 CO2e. The following 3 industry groups, namely CHemicals, Materials and Media are very close in carbon emissions, all having carbon footprints of about 10000 CO2e.
It is worth noting that the carbon footprints recorded for Automobiles & Components and Materials are from over 70 products, significantly more than Chemicals (32 products), Media(15 products) or Pharmaceuticals, Biotechnology & Life Sciences (3 products).
In Automobiles & Components and Pharmaceuticals, Biotechnology & Life Sciences, most of the carbon emissions are from downstream processing, whereas in Media, it is upstream processing that is responsible for most of the carbon emissions.

#### Which countries have the highest contribution to carbon emissions?
Join product_emissions with countries, then get the names along with carbon footprints and number of products of the 5 countries with the largest carbon footprint
```
SELECT countries.country_name,
	   COUNT(*) AS number_of_products,
	   product_emissions.carbon_footprint_pcf
FROM product_emissions JOIN countries
ON product_emissions.country_id = countries.id
GROUP BY countries.country_name
ORDER BY product_emissions.carbon_footprint_pcf DESC
LIMIT 5;
```
| country_name| number_of_products| carbon_footprint_pcf| 
| ------------: | ------------------: | --------------------: | 
| Germany| 67| 21725| 
| South Korea| 33| 5846| 
| Brazil| 18| 1750| 
| Japan| 142| 1488| 
| India| 16| 1282| 

The country with the highest carbon emissions is Germany with 21725 CO2e, follwed by South Korea with 5846 CO2e. The next 3 countries (Brazil, Japan, India) do not differ much in carbon footprint, all accounting for approximately 1500 CO2e.
Interesting, Japan has the highest number of products within these 5 countries, followed by Germany. The large carbon footprint of South Korea comes from only 33 products. Brazil and India, despite having similar carbon emissions as Japan, only have less 20 products recorded.

#### What are some trends in carbon emissions over year?
Get the total carbon footprint produced and the percentage contribution of each production stage of each year
```
SELECT year, 
	   SUM(carbon_footprint_pcf) AS total_carbon_footprint_pcf,
     ROUND(AVG(upstream_percent_total_pcf), 2) AS upstream_percent_total_pcf, 
	   ROUND(AVG(operations_percent_total_pcf), 2) AS operations_percent_total_pcf, 
	   ROUND(AVG(downstream_percent_total_pcf), 2) AS downstream_percent_total_pcf
FROM product_emissions
WHERE upstream_percent_total_pcf != 'N/a (product with insufficient stage-level data)'
GROUP BY year;
```
| year| total_carbon_footprint_pcf| upstream_percent_total_pcf| operations_percent_total_pcf| downstream_percent_total_pcf| 
| ----: | --------------------------: | --------------------------: | ----------------------------: | ----------------------------: | 
| 2013| 241733| 46.28| 21.11| 32.61| 
| 2014| 236829| 51.99| 20.16| 27.85| 
| 2015| 165491| 44.8| 21.79| 33.4| 
| 2016| 393360| 47.41| 23.86| 28.73| 
| 2017| 324430| 33.93| 24.53| 41.55| 

From 2013 to 2015, the total carbon footprints have increased from 241733 to 165491 CO2e. However, there was a large increase in carbon footprint from 2015 to 2016 to 393360 CO2e. This then decreased to 324430 CO2e in 2017, but this was still higher than the emissions in 2013.
From 2013 to 2016, upstream processing was responsible for most of the carbon emissions produced. In 2017, however, most of the carbon footprint came from downstream processing.

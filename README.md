# ETL_Project

COVID-19 ETL PROJECT
 The Pretentious Pugs Group
MEMBERS: Ellis Mok | Katherine Matovic | Ricky Kong | Silva MK


SUBJECT OF DATA EXPLORATION
COVID-19 vaccinations, test cases, hospitalizations, mortality and unemployment in California Counties
MAIN OBJECTIVES
To collect COVID-19 data from two different sources that are maintained periodically, preferably managed by government or API sources that collect government data
To perform cleaning, merging, filtering, normalization and aggregations of all the data being used
To upload the files in a database in preparation for more thorough analysis
To formulate ideas on what types of questions or analyses can be drawn from the datasets
DATA SOURCES
California Health and Human Services Open Data Portal - For COVID-19 data
website: https://data.chhs.ca.gov/
covid19vaccinesbycounty.csv
covid19hospitalbycounty.csv
covid19cases_test.csv
Socrata Open Data API (SODA) - For Employment/Unemployment data
website: https://data.edd.ca.gov/Labor-Force-and-Unemployment-Rates/Local-Area-Unemployment-Statistics-LAUS-/e6gw-gvii
Only public information can be extracted without an API key
employment_dataset.csv
DATA DESCRIPTIONS
COVID-19 Vaccine Data - covid19vaccinesbycounty.csv
There were 5,212 total records collected from December 15, 2020 through March 16, 2021. The total number of doses administered per county in CA has been recorded daily.
In the vaccine raw data, there were county records named “Outside California” and “Unknown”. Based on CHHS Open Data Portal, these records are non-California residents who received vaccinations in a specific CA county. There were also 23 other counties reported that do not belong to California County like Tarrant, Windham, Walla Walla & etc.
COVID-19 Hospitalization Data - covid19hospitalbycounty.csv
There were 19,709 total records collected from March 29, 2020 through March 15, 2021. The following data were collected for each county on a daily basis:
the number of hospitalized confirmed & suspected COVID-19 patients
the number of ICU confirmed & suspected COVID-19 patients
the number of ICU available beds
the number of hospitalized COVID-19 patients (missing data from March 29, 2020 through April 20, 2020)
the number of all hospital beds per county (missing data from March 29, 2020 through April 20, 2020)
COVID-19 Test Cases Data - covid19cases_test.csv
There were 26,901 total records collected from January 1, 2020 through March 16, 2021. The number of test cases are recorded for each county on a daily basis with the most recent dates added at the top of the table. The following data were collected for each county per day:
area or county name
area type
the number of population by county
total number of new & cumulative
laboratory-confirmed COVID-19 cases
confirmed COVID-19-related deaths
PCR tests performed by laboratories
PCR tests with positive results
reported cases to the CA Department of Public Health
reported deaths to the CA Department of Public Health
reported PCR tests
There were some negative numbers in the column “reported_deaths”. Based on the CHHS Open Data Portal field documentation, these were corrections from the previous day’s report (i.e. if the cause of death was determined to be non-COVID related)
Employment/Unemployment Data - employment_dataset.csv
There were 26,000 data extracted from Socrata API from April 2004 through January 2021. The following data were collected for each county per month:
number of labor force
number of employment and unemployment
unemployment rate
The data in columns record count, area_type, year, month, and status_preliminary_final were were not used. Therefore, these columns were deleted.
ASSUMPTIONS & LIMITATIONS
All COVID data include only 2020 to 2021. Employment and unemployment data were narrowed down to only include these years.
The datasets are limited to the time fram they were reported as indicated in the data descriptions.
ETL Methodologies


A. DATA EXTRACTION
Downloaded three CSV datasets related to vaccines, hospitalizations and test cases by county level from the California Health and Human Services Open Data Portal
Collected EDD employment/unemployment data using API extraction via Socrata Open Data API
B. DATA TRANSFORMATION (Python & Pandas)
Data Cleaning & Filtering
Dataset1: covid19vaccinesbycounty.csv
The column “california_flag” contains “California” or “Not in California”. This column is not necessary to keep because all counties are in CA and the records with “Not in California” were already categorized in the county column as “Outside California” and “Unknown”. Therefore, this column has been deleted.
Removed records that were "Outside California", "Unknown", "California", "Oxford", "Cottonwood" and the rest of this list are found in the Transform directory, data_notes.xls file
Re-formatted dates to a yyyy-mm-dd to sort the data correctly
Replaced all NaN's with 0
Renamed the columns to match the column names to be used in SQL
Dataset2: covid19hospitalbycounty.csv
There are 1,285 records (approximately 6.5% of the entire dataset) with the missing data pertaining to the number of all hospital beds and the number of hospitalized COVID-19 patients from March 29,2020 to April 20,2020. The number of all hospital beds were left as 0 on these dates. The records in the column “hospitalized_covid_patients” for the dates after April 20, 2020 are equal to the columns “hospitalized_covid_confirmed” and “hospitalized_covid_suspected” patients. Therefore, column “hospitalized_covid_patients” has been re-calculated in Pandas so that the dates with missing data can be filled in. Then, deleted the original "hospitalized_covid_patients" with the missing data.
Re-formatted dates to a yyyy-mm-dd to sort the data correctly
Replaced all NaN's with 0
Renamed the columns to match the column names to be used in SQL
Dataset3: covid19cases_test.csv
The column “area” are generally county names but there were records of California, Out of State and Unknown. Since we are focusing on counties, these records have been deleted.
The column “area_type” indicates either “County” or “State”. In reference to the first bullet above, California has been deleted so there will be no records with "State" and only "County" were left in this columns. Therefore, there's no need to keep a column that just contains one information. This column has been deleted.
Removed all cumulative sums columns because these can be easily calculated in SQL, if necessary
Re-formatted dates to a yyyy-mm-dd to sort the data correctly
Replaced all NaN's with 0
Renamed the columns to match the column names to be used in SQL
Dataset4: employment_dataset.csv
Since the time span of the data is too broad, including non-COVID19 era, records before 2020 were excluded.
There were records of “State”, “Sub-County Place”, “MSA”, “Metropolitan Area” and “Metro Division” in column area_type. These records have been deleted because the data's main focus is on counties.
There were 2 records for Los Angeles county, one record reflects the number of employment/unemployment that are seasonally adjusted and the other record was not seasonally adjusted. We will assume the record with no seasonal adjustment (seasonally_adjusted_y_n)=N because the majority of the seasonally adjusted records (seasonally_adjusted_y_n=Y) were before January 2020. The records with seasonally_adjusted_y_n=Y has been deleted.
Re-formatted dates to a yyyy-mm-dd to sort the data correctly
Renamed the columns to match the column names to be used in SQL
Data Normalization


Final normalized datasets
employment/unemployment and vaccine data - normalizing not needed because data is small
hospital data - produced 3 smaller datasets, hospitalized covid patients, hospital beds & icu covid patients
test cases data - produced 4 smaller datasets, counties, cases, deaths and total tests
C. DATA LOADING (PGAdmin - SQL)
Created ERD Diagram via QuickDBD that shows how all the different normalized has been related
Used county and date as primary & foreign composite keys
Set the primary composite key from the Counties data

Created a new database called covid_db
Wrote a Schema to create the tables in the database and setting primary/foreign composite keys for each dataset
Imported all nine normalized datasets successfully in SQL server
Generated some queries to show how the data can be useful to analysts in the future

SAMPLE OF LOADED DATA

D. DATA USE CASES
From these datasets, the following are some useful queries that can be generated by the future analyst(s):
The counties with the highest total number of administered vaccine
The number of total COVID deaths in each county or a spevific county of interest
The average number of hospitalized COVID patients in Los Angeles when COVID just started
The county that has the most positive tests

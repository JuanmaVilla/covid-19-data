# Contribute
We welcome contributions to our vaccination dataset! Note that due to the nature of our pipeline, **we cannot accept pull requests
for manually imported country data**. To see which countries are automated and which reaquire manual import, check
[this file](automation_state.csv).

### Content
- [About our vaccination data](#about-our-vaccination-data)
  - [Manufacturer data](#Manufacturer-data)
  - [Age group data](#Age-group-data)
- [Report new data values](#report-new-data-values)
- [Add new country automations](#Add-new-country-automations)
  - [Steps](#Steps)
- [Criteria to accept pull requests](#criteria-to-accept-pull-requests)

## About our vaccination data
For details about the development environment, check the details in [README](README.md#2-development-environment).

We are currently collecting vaccination data at country level in the following format:

|location   |date      |vaccine                                               |source_url                                                                                                  |total_vaccinations|people_vaccinated|people_fully_vaccinated|
|-----------|----------|------------------------------------------------------|------------------------------------------------------------------------------------------------------------|------------------|-----------------|-----------------------|
|Afghanistan|2021-05-30|Oxford/AstraZeneca, Pfizer/BioNTech, Sinopharm/Beijing|https://covid19.who.int/                                                                                    |600152            |480226           |119926                 |

Where the metrics `total_vaccinations`, `people_vaccinated` and `people_fully_vaccinated` are defined as read
[here](https://github.com/owid/covid-19-data/tree/master/public/data/vaccinations#vaccination-data).
 
Note that for some countries, some metrics can't be reported as these are not be available. This is not ideal but it is OK.

### Manufacturer data
Along with the main data, we include vaccine data break by manufacturer for some countries where this data is available.

Each row in the data gives the cumulative number of doses administered for a given date and vaccine manufacturer.

#### Fields
- `date`: Date in format YYYY-MM-DD
- `vaccine`: Vaccine manufacturer name. Our convention for vaccine names can be found
  [here](https://github.com/owid/covid-19-data/blob/c4208f353449d750515b8e14015bde2c349371ee/scripts/scripts/vaccinations/src/vax/utils/checks.py#L7).
  As new vaccines emerge, new conventions will be defined.
- `location`: Region name.
- `total_vaccinations`: Cumulative number of administered doses up to `date` for given `vaccine`.


#### Example
|date      |vaccine           |location|total_vaccinations |
|----------|------------------|------------------|---------|
|...|...           |...            |...|
|2021-06-01|Moderna           |Lithuania            |151261|
|2021-06-01|Oxford/AstraZeneca|Lithuania            |333733|
|2021-06-01|Johnson&Johnson   |Lithuania             |34974|
|2021-06-01|Pfizer/BioNTech   |Lithuania           |1133371|
|...|...           |...            |...|


### Age group data

Along with the main data, we include vaccine data break by age groups for some countries where the data is available.

Each row in the data gives the cumulative number of doses administered for a given date and an age group. Note that
currently there is no standard for which age groups are accepted, as each country may define different ones. As a
general rule, we try to have groups as granular as possible (10 years chunks).

#### Fields
- `date`: Date in format YYYY-MM-DD
- `age_group_min`: Lower bound of the age group.
- `age_group_max`: Upper bound of the age group (included).
- `location`: Region name.
- `total_vaccinations`: Cumulative number of administered doses up to `date` for given `vaccine`.


#### Example
|date      |age_group_min     |age_group_max|location|total_vaccinations|
|----------|------------------|-------------|------------------|--------|
|...|...           |...            |...|...|
|2020-12-27|20                |29           |Italy               |686   |
|2020-12-27|30                |39           |Italy              |1069   |
|2020-12-27|40                |49           |Italy              |1476   |
|2020-12-27|50                |59           |Italy              |2168   |
|2020-12-27|60                |69           |Italy              |1461   |
|2020-12-27|70                |79           |Italy               |129   |
|2020-12-27|80                |89           |Italy               |118   |
|2020-12-27|90                |             |Italy                |77   |
|...|...           |...            |...|...|


## Report new data values
To report new values for a country/location, first check if the imports for that country/territory are automated. You
can check column `automated` in [this file](automation_state.csv).

- If the country imports are automated (`TRUE` value in file above), note that the new value might be added in next
  update. **Only report new values if the data is missing for more than 48 hours!** Report the new data as a [pull request](https://github.com/owid/covid-19-data/compare).
- If the country imports are not automated, i.e. data is manually added, (`FALSE` value in file above) you can report
  new data in any of the following ways:
  - Open a [new issue](https://github.com/owid/covid-19-data/issues/new), reporting the data and the corresponding
    source.
  - If you plan to contribute regularly to a specific country/location, consider opening a dedicated issue. This way,
    we can easily back-track the data addded for that country/location.
  - If this seems too complicated, alternatively, you may simply add a comment to thread
[#230](https://github.com/owid/covid-19-data/issues/230). 

*Note*: We only accept official sources or news correctly citing official sources.

## Add new country automations
To automate the data import for a country, make sure that:
- The source is reliable.
- The source provides data in a format that can be easily read:
    - As a file (e.g. csv, json, xls, etc.)
    - As plain text in source HTML, which can be easily scraped.

### Steps
Next, follow the steps below:

1. Decide if the import is batch (i.e. all the timeseries) or incremental (last value). See the scripts in
   [`src/vax/batch`](src/vax/batch) and [`src/vax/incremental`](src/vax/incremental) for more details. **Note: Batch is
   prefered over Incremental**.
2. Create a script and place it based on decision in step 1 either in [`src/vax/batch`](src/vax/batch) or
   [`src/vax/incremental`](src/vax/incremental). Note that each source is different and there is no single pattern that
   works for all sources, however you can take some inspiration from the scripts below:
    - Batch imports:
        - CSV: [Peru](src/vax/batch/peru.py)
        - JSON: [Hong Kong](src/vax/batch/hong_kong.py)
        - API/JSON (with manufacturer data): [Lithuania](src/vax/batch/lithuania.py)
        - CSV (with manufacturer data): [Romania](src/vax/batch/romania.py)
        - Excel (with manufacturer data): [Latvia](src/vax/batch/latvia.py)
        - Link to excel (with age data): [New Zealand](src/vax/batch/new_zealand.py)
    - Incremental imports:
        - HTML: [Bulgaria](src/vax/incremental/bulgaria.py), [Equatorial Guinea](src/vax/incremental/equatorial_guinea.py)
        - HTML, from news feed: [Macao](src/vax/incremental/macao.py), [Albania](src/vax/incremental/albania.py), [Monaco](src/vax/incremental/monaco.py) 
        - PDF: [Taiwan](src/vax/incremental/taiwan.py), [Nepal](src/vax/incremental/nepal.py)
        - CSV: [Argentina](src/vax/incremental/argentina.py)
        - API/JSON: [Poland](src/vax/incremental/poland.py)
    - Others:
        - From WHO: See [WHO](src/vax/incremental/who.py)
        - From SPC: See [SPC](src/vax/batch/spc.py)
3. Feel free to add [manufacturer](#Manufacturer-data)/[age data](#Age-data) if you are automating a batch script and
   the data is available.
4. Test that it is working and that it is stable. For this you need to have the [library
   installed](README.md#2-development-environment). Run
```
cowid-vax get -c [country-name]
``` 
   
5. Issue a pull request and wait for a review.



More details: [#230](https://github.com/owid/covid-19-data/issues/230),
[#250](https://github.com/owid/covid-19-data/issues/250)

## Criteria to accept pull requests
Due to how our pipeline operates at the moment, pull requests are only accepted under certain conditions. These include,
but are not limited to, the following:

- Code improvements / bug fixes. As an example, you can take [#465](https://github.com/owid/covid-19-data/pull/465).
- Updates on the data for countries with automated data imports and incremental processes (this countries are found
  [here](src/vax/incremental)). For this case, you can create a PR modifying the corresponding file in [output
  folder](https://github.com/owid/covid-19-data/tree/master/scripts/scripts/vaccinations/output). Create the pull
  request only if the daily update already ran but did not update the corresponding country.

You can of course, and we appreciate it very much, create pull requests for other cases.

Note that files in [public folder](https://github.com/owid/covid-19-data/tree/master/public) are not to be manually modified.
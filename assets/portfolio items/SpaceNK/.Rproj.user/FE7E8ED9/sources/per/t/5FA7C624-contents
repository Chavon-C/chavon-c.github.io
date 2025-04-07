---
editor_options: 
  markdown: 
    wrap: 72
---

## Report on how foundation labels reinforce racial and societal bias

This report aims to discuss the implications of naming foundation
shades, linking it racial and societal bias. This was inspired by the
publication ['The Naked
Truth'](https://pudding.cool/2021/03/foundation-names/) by Ofunne Amaka
and Amber Thomas (2021), which motivated me to further explore the
implications of naming shades, as well as discussing how lighter shades
are named and the consequences of doing so, linking it to wider
discussion in the social sciences.

### R files

| Name | Description |
|----|----|
| 01-web-scraping.R | Code for automatic data collection from the SpaceNK website |
| 02-data-wrangling.R | Code for cleaning up the data collected |
| app.R | Code for the R Shiny dashboard |

### CSV files

#### foundation_data.csv

This was created in the 01-web-scraping.R file, which includes the final
data frame of the scraped website.

-   1555 observations

| Variables   | Description         |
|-------------|---------------------|
| Brand       | Name of brand       |
| ProductName | Name of product     |
| ShadeName   | Name of shade       |
| ShadeColour | Hex colour of shade |

#### foundation_all_clean.csv

This was created in the 02-data-wrangling.R file, which includes
information of all products. Products that were deemed to be duplicates,
i.e travel sized products and refills were removed.

-   1481 observations

| Variables   | Description             |
|-------------|-------------------------|
| Brand       | Name of brand           |
| ProductName | Name of product         |
| ShadeName   | Name of shade           |
| ShadeColour | Hex colour of shade     |
| L           | HSL L value (lightness) |

#### foundation_words.csv

This was created in the 02-data-wrangling.R file, which includes
information of products only used a naming system for foundation shades.

-   644 observations

| Variables   | Description             |
|-------------|-------------------------|
| Brand       | Name of brand           |
| ProductName | Name of product         |
| ShadeName   | Name of shade           |
| ShadeColour | Hex colour of shade     |
| L           | HSL L value (lightness) |

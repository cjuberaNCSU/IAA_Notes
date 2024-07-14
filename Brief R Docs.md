# Common Processes 
## Change Column Types
Col1, Col2, Col3 are columns that we want to change to factors
```r
data <- data %>% mutate(across(c(Col1, Col2, Col3), as.factor))
```
`c(Col1, Col2, Col3)` selects Col1, Col2, and Col3

If we want to change all but Col4 and Col 5 to factor
```r
data <- data %>% mutate(across(-c(Col4, Col5), as.factor))
```
`-c(Col4, Col5)` represents not Col4 or Col5

## Split a String Column into Multiple Columns
```r
data %>% 
  separate_wider_delim(Combined_Col, delim=",", names = c("Col1", "Col2")) 
```

## Drop a Column
```r
data <- data %>% select(-Col_To_Drop)
```
`select(-Col_To_Drop)` selects all Columns except for Col_To_Drop

## Change a Column by a Condition
```r
data <- data %>%
	mutate(Col1 = ifelse(Col1 > 0, Col1 + Col2, Col1))
```
`Col1 > 0` is a conditional that is being tested
`Col1 + Col2` is what happens when the conditional (`Col1 > 0` in this example) is True
`Col1` is what happens when the conditional is False

## Change a Column by Multiple Conditions
Can use multiple mutate + if_else calls, or can use `case_when`
```r
df <- df %>% mutate(Country =  case_when(
  Country == "US" ~ "United States",
  Country == "U.S.A" ~ "United States",
  Country == "U.S." ~ "United States",
  Country == "USA" ~ "United States",
  Country == "United States of America" ~ "United States",
  Country == "The United States" ~ "United States",
  Country == "united states" ~ "United States",
  Country == "Durham" ~ "United States",
  State == "North Carolina" & is.na(Country) ~ "United States",
  .default = Country 
))
```
A more efficient approach here would be to use `Country %in% c("US", "U.S.A", ... "Durham"`, but they produce the same results.
`Country == "US"` is the conditional tested.
`~ "United States"` is the result returned when the condition is True.
`.default = Country` returns `Country` when none of the cases are used.

## Split a Dataset into Training and Testing
```r
set.seed(123) 
data <- data %>% mutate(id = row_number()) # if there is not a index, create one
train <- data %>% sample_frac(0.7) 
test <- anti_join(data, train, by = 'id')
```
`0.7` is the percent of the data to include in the training.

# Functions
## Data Manipulation Functions
### `across()`
Used to mutate across multiple columns.
Example:
```r
data <- data %>% mutate(across(c(Col1, Col2), as.factor))
```
**Look at full documentation for more advanced applications**
[Full Documentation](https://dplyr.tidyverse.org/reference/across.html)

### `mutate()`
Used to change or create new columns.
Example:
```r
df %>% mutate(z = x + y) # create a z column that equal to x + y
```
[Full Documentation](https://dplyr.tidyverse.org/reference/mutate.html)

### `if_else()`
Used to return values based on a conditional statement.
Example:
```r
if_else(x < 0, "negative", "positive", missing = "missing")
```
[Full Documentation](https://dplyr.tidyverse.org/reference/if_else.html)

### `case_when()`
Used to return values based on multiple conditional statements.
Example:
```r
case_when(
  x %%  5 == 0 ~ "fizz",
  x %%  7 == 0 ~ "buzz",
  x %% 35 == 0 ~ "fizz buzz",
  .default = as.character(x)
)
```
[Full Documentation](https://dplyr.tidyverse.org/reference/case_when.html?q=case_#null)

### `seperate_longer_delim()`
Used to split a string column into multiple rows.
Example:
```r
df %>% 
  separate_longer_delim(c(Languages), delim = ",") # Separate Languages column by "," separator
```
[Full Documentation](https://tidyr.tidyverse.org/reference/separate_longer_delim.html)

### `seperate_wider_delim()`
Used to split a string column into multiple columns.
Example:
```r
df %>% 
  separate_wider_delim(Birth_Month_Year, delim=", ", names = c("Month", "Year")) 
```
[Full Documentation](https://tidyr.tidyverse.org/reference/separate_wider_delim.html)

### `na_if()`
Sets value of a column equal to NA based on a condition
Example:
```r
na_if(y, "") # y is the column and "" is the value to set to NA
```
[Full Documentation](https://dplyr.tidyverse.org/reference/na_if.html)
## String Functions
### `str_split_fixed()`
Split string column into character matrix (table) with a column for each split.
Example:
```r
df %>% mutate(Birth_Month = str_split_fixed(df$Birth_Month_Year, ", ", 2)[,1],
              Birth_Year = str_split_fixed(df$Birth_Month_Year, ", ", 2)[,2])
```
Better to use `seperate_wider_delim()`
[Full Documentation](https://www.rdocumentation.org/packages/stringr/versions/0.6.1/topics/str_split_fixed)

### `grepl()`
A [regex](https://cheatography.com/davechild/cheat-sheets/regular-expressions/) search of a string value that returns True if a string contains a value and False otherwise.
Example:
```r
grepl("[0-9]", Birth_Month) # if there is a number in the string return True

```
[Full Documentation](https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/grep)

### `str_replace_all()` or `str_replace()`
Replace a pattern within a string with another string value. `str_replace()` for first match and `str_replace_all()` for all matches. 
Example:
```r
str_replace_all(String, ",", "")
```
[Full Documentation](https://stringr.tidyverse.org/reference/str_replace.html)

### `str_to_title()`, `str_to_upper()`, `str_to_lower()`, and `str_to_sentence()`
Ways to change capitalization of a string.
- Title Case
- UPPER CASE
- lower case
- Sentence case
[Full Documentation](https://stringr.tidyverse.org/reference/case.html?q=str_to_#ref-usage)

### `str_squish()` or `str_trim()`
Remove leading and trailing whitespace from a string
[Full Documentation](https://stringr.tidyverse.org/reference/str_trim.html)
									
## Time  
A lot of functions do the similar things for different time formats, use [lubridate cheatsheet](https://rawgit.com/rstudio/cheatsheets/main/lubridate.pdf)

### `mdy()`
Parse a date by Month, Year, and Day
```r
mdy(010210) # returns "2010-01-02"
```
[Full Documentation](https://lubridate.tidyverse.org/reference/ymd.html)

### `day()`, `month()`, or `year()`
Get specific time out of a date object 
```r
dates <- my("Apr 2020")
year(dates) # 2020
month(dates) # 4
```
[Full Documentation](https://lubridate.tidyverse.org/reference/day.html)

# Misc.
- `month.name` is a list in R that can be indexed with each month number. 
- `slice_max(col, n=5)` for top 5 and `slice_min(col, n=5)` for bottom 5 by values of col.
- `state.name` is a default list in R that contains US state names in title case. Useful for checking is a string is a US state. 
	- Example: `mutate(State = ifelse(State %in% state.name,State,NA))`
- `unique(Col)` is useful for checking work on qualitative columns.
- `head()` (for first couple of rows) or `sample()` (for random rows) can give you a quick subset of your dataframe to view.
- To get value not in list you can use `!(Value %in% List)` 
- Country code library can convert all types of country spellings to standard type: `countrycode(Country, origin = 'country.name', destination = 'iso3c')` 



# SCRAPING 


## Part A

Wikipedia tends to be an excellent source of static tables of information that make
a good (and easy) target to scrape. The site here holds information on life expectancies of men and women by country. For this part of the problem, you need
to scrape the CIA World Factbook table, which I chose mainly for its recent values
and broad global coverage. Scrape all of the information from this table into a data
frame for later use.

R code to scrape and create your dataframe
```R
#reading in webpage from internet 
html <- read_html("https://en.wikipedia.org/wiki/List_of_countries_by_life_expectancy")

#scraping CIA World Factbook Table 
tables <- html %>% html_table()
tables[[7]]
cia = tables[[7]]
```



## Part B
R code to define your API function and then apply it to your column of country names
```R
#testing paste and URLencode functions
country = "Republic of the Congo"
html <- read_html(paste("https://restcountries.com/v3.1/name/",country))
URLencode(paste("https://restcountries.com/v3.1/name/",country, sep = ""))


#testing meat of the function                  
data <- read_html(URLencode(paste("https://restcountries.com/v3.1/name/",country, sep = ""))) %>% 
  html_text %>% fromJSON %>% 
    pull(landlocked)
#testing if data holds the 
data

#var data has 2 booleans! Looks like my paste function with name endpoint is not adequate...it is grabbing multiple countries. I will use full name endpoint.

url <- URLencode(paste("https://restcountries.com/v3.1/name/",country,"?fullText=true", sep = ""))

#bingo. Now url is only for 1 country at a time. 

#function time: 
ll_checker <- function(country){
  data <- read_html(URLencode(paste("https://restcountries.com/v3.1/name/",country,"?fullText=true", sep = ""))) %>% 
    html_text %>% 
      fromJSON %>% 
        pull(landlocked)
  
  return(data)
}

#testing my function
ll_checker("Italy")
ll_checker("Chad")
ll_checker("Mongolia")
ll_checker("United States of America")
ll_checker("Democratic Republic of the Congo")
ll_checker("South Sudan")

#ii. Catching errors with tryCatch()

#adding a tryCatch block
ll_checker <- function(country){
  print(country)
tryCatch(
  {
    data <- read_html(URLencode(paste("https://restcountries.com/v3.1/name/",country,"?fullText=true", sep = ""))) %>% 
      html_text %>% 
      fromJSON %>% 
      pull(landlocked)
    
    return(data)
  },
  error = function(cond){
    return(NA)
  }
)
}

#testing tryCatch
ll_checker("France")
ll_checker("oatmeal")
ll_checker("coffee")
ll_checker("Somolia")
ll_checker("Somalia") 
ll_checker("Mali")



```



## Part C
R code to add and populate the new column to your dataframe and then export the entire thing as a CSV.

```R
map_lgl(cia$Country, ll_checker)
landlock_check = unlist(map(cia$Country, ll_checker))
cia$landlocked = landlock_check 

#checking that data frame now has landlock boolean column
cia

#writing data frame to csv 

write.csv(cia, "/Users/charleshanks/Desktop/MSDS/DATA_503/hw-07-chanks06-main/country_info.csv")
```

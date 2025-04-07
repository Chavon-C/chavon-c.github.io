rm(list=ls())

pacman::p_load(
  dplyr, 
  plotwidgets, 
  stringr)

foundation<- read.csv("foundation_data.csv")

#removing redundant column and products that had NA's for shade colour (only 2)
foundation <- foundation %>% select(-X) %>% na.omit()

#some shade names had the size of the product, so i removed that from the name
foundation$ShadeName <- trimws(gsub("30 ML|30ML|32 ML", "", foundation$ShadeName)) 

#there were refills and travel sized products which are the same as full sized ones so i removed them as they're duplicates
foundation <- foundation[!grepl("refill|mini", foundation$ProductName, ignore.case = TRUE), ]

#to order shades from light to dark, i need the HSL value, specifically L (lightness)

#had to convert to RGB first and then to HSL
rgb_values <- col2rgb(foundation$ShadeColour)
hsl_values <-rgb2hsl(rgb_values)

foundation$L <- hsl_values[3,]

write.csv(foundation, "foundation_all_clean.csv")


#creating csv for products that only has words in their name
foundation_words <- foundation %>%
  mutate(
    #removing all numbers in the shade name
    ShadeWords = str_remove_all(ShadeName, "\\d+\\.\\d+|\\d+"),
    #shade names with just numbers will be coded as NA and removed from dataframe
    ShadeWords = na_if(ShadeWords, "")  
  ) %>%
  na.omit() %>%
  select(-ShadeName) 

#cleaning the foundation_words dataframe

#these were brands that did not use words to describe their shades but still had words, e.g. 'SHADE 1'
foundation_words <- foundation_words[!grepl("Charlotte|Anastasia|Glossier|Terry|Lisa|KOSAS|ROSE|MONIKA|RARE|KEVYN|WESTMAN", 
                                foundation_words$Brand, ignore.case = TRUE), ]

#this product was named 'SHADE 1' , 'SHADE 2' and was therefore removed as it is a number based system
foundation_words <- foundation_words[!grepl("ReEvolve Natural Finish Foundation", 
                                            foundation_words$ProductName, ignore.case = TRUE), ]

#some shades have standalone letters like "N" , "C" or "N+" before the name, which stand for neutral or cool, which i decided to remove 
foundation_words$ShadeWords <- foundation_words$ShadeWords %>%
  str_remove_all("\\b[N|C|W|B|O|R]\\b") %>% 
  str_remove_all("\\bSF\\b|\\bNR\\b|\\bST\\b|\\bOB\\b|\\bOC\\b|\\bOR\\b|\\bFE\\b") %>%
  str_remove_all("[\\+\\.]") %>% 
  str_squish()  

#creating the top 15 lightest shades dataframe
lightest_shades <- foundation_words %>%
  arrange(desc(L)) %>%     
  head(15)             

##creating the top 15 darkest shades dataframe
darkest_shades <- foundation_words %>%
  arrange(L) %>%                
head(15)   

write.csv(foundation_words, "foundation_words.csv")


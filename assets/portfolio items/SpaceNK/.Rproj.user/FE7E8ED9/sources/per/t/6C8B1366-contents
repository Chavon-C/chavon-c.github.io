pacman::p_load(
  RSelenium,
  rvest,
  dplyr
)

#start Selenium server and remote driver
driver <- rsDriver(browser = "firefox")
remote_driver <- driver$client

#URL of the page for foundation products
category_url <- "https://www.spacenk.com/uk/makeup/complexion/foundation"
remote_driver$navigate(category_url)

#clicking accept cookies button
webElems <- remote_driver$findElements(using = "xpath", '//*[@id="ensAcceptAll"]')
webElems[[1]]$clickElement()
Sys.sleep(5) 


#getting the product links
get_product_links <- function(remote_driver){

  #clicking the load more button
  web_el <- remote_driver$findElement("css", ".btn-outline-primary")
  web_el$clickElement()
  Sys.sleep(5)
  
  #extracting all product links 
  category_page <- read_html(remote_driver$getPageSource()[[1]])
  product_links <- category_page %>%
    html_elements("a.swatch-container.show-more-swatch") %>%
    html_attr("href")
  
  #product links = relative URL, so this changes it to absolute URL, so it can navigate when scraping
  base_url <- "https://www.spacenk.com"
  product_links <- paste0(base_url, product_links)
  
  return(product_links)
}

#scraping elements of interest in individual product page
scrape_product_page <- function(url) {
  #reading the product page HTML
  webpage <- read_html(url)
  
  #extracting brand name from heading 1
  brand_name <- webpage %>%
    html_element("a.text-primary.h1") %>%
    html_text(trim = TRUE)
  
  #extracting product name from heading 4
  product_name <- webpage %>%
    html_element("p.product-name.h4") %>%
    html_text(trim = TRUE)
  
  #extracting shade names from assistive text
  shade_names <- webpage %>%
    html_elements("span.sr-only.selected-assistive-text") %>%
    html_text(trim = TRUE)
  
  #extracting shade colours from the style attribute
  shade_colours <- webpage %>%
    html_elements("span.simple-swatch") %>%
    html_attr("style") %>%
    #getting the hex codes by matching the css background colour
    gsub(".*background-color: (#[0-9A-Fa-f]{6});.*", "\\1", .)
  
  
  #there was a mismatch of length for shade names and colour, so i gave NA values to the shorter variable so they had equal lengths and could be put into a dataframe
  max_length <- max(length(shade_names), length(shade_colours))
  shade_names <- c(shade_names, rep(NA, max_length - length(shade_names)))
  shade_colours <- c(shade_colours, rep(NA, max_length - length(shade_colours)))
  
  
  #repeating brand and product name for each shade
  brand_name_repeated <- rep(brand_name, max_length)
  product_name_repeated <- rep(product_name, max_length)
  
  #combining all the data into a dataframe
  data <- data.frame(
    Brand = brand_name_repeated,
    ProductName = product_name_repeated,
    ShadeName = shade_names,
    ShadeColour = shade_colours,
    stringsAsFactors = FALSE
  )
  
  return(data)
}

#making an empty list to put data inside
foundation_data <- list()

#getting the product URLs to scrape
product_urls <- get_product_links(remote_driver)

#combining the scraped product URLS into the list
foundation_data <- lapply(product_urls, scrape_product_page) %>%
  bind_rows()

#writing a csv file for foundation data
write.csv(foundation_data, "foundation_data.csv")

remote_driver$close()  # Close the remote driver
driver$server$stop()  # Stop the Selenium server


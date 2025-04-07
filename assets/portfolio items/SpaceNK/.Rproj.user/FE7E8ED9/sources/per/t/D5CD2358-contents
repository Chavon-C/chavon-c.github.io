pacman::p_load(shiny,
               shinydashboard,
               ggplot2,
               dplyr,
               wordcloud2,
               plotly
               )


ui <- dashboardPage(
  dashboardHeader(title = "Foundation Shades Dashboard"),
  dashboardSidebar(
    sidebarMenu(
      menuItem("All Shades", tabName = "allshades", icon = icon("chart-bar")),
      menuItem("Shade Names", tabName = "shadenames", icon = icon("cloud")),
      menuItem("Name Plot", tabName = "nameplot", icon = icon("chart-area"))
    )
  ),
  dashboardBody(
    tabItems(
      #tab for all shades visualisation
      tabItem(tabName = "allshades",
              h2("All Shades"),
              sliderInput("lightness1", "Select Lightness Range:", 
                          min = 0,
                          max = 1,
                          value = c(0, 1),
                          step = 0.01),
              plotOutput("allshades_plot")
      ),
      
      #tab for shade names visualisation
      tabItem(tabName = "shadenames",
              h2("Shade Names"),
              sliderInput("lightness", "Select Lightness Range:", 
                          min = 0,
                          max = 1,
                          value = c(0, 1),
                          step = 0.01),
              wordcloud2Output("shadenames_cloud")
              
      ),
              tabItem(tabName = "nameplot",
                      h2("Name Chart"),
                      selectInput("shade_filter", "Filter by Shade Group:",
                                  choices = c("Darkest 15", "Lightest 15"),
                                  selected = "Darkest 15"),
                      plotlyOutput("nameplot")
              
      )
    )
  )
)

server <- function(input, output) {
  filtered_bar <- reactive({
    #using foundation dataset and filtering based on Lightness
    foundation %>%
      filter(L >= input$lightness1[1], L <= input$lightness1[2])
  })
  
  #rendering barplot
  output$allshades_plot <- renderPlot({
    foundation_filtered <- foundation %>%
      filter(L >= input$lightness1[1], L <= input$lightness1[2]) %>%
      #grouping L values by intervals of 0.01
      mutate(L_interval = cut(L, breaks = seq(0, 1, by = 0.01), right = FALSE))  
    
    #making the bar plot
    ggplot(foundation_filtered, aes(x = L_interval, fill = ShadeColour)) +
      geom_bar(stat = "count", color = "white", width = 1) + 
      scale_fill_identity() + 
      scale_x_discrete(name = "Lightness (L)", limits = levels(foundation_filtered$L_interval)) + 
      labs(title = "Barplot: Foundation Shades by Lightness",
           y = "Frequency",
           x = "Lightness (L)") +
      theme_minimal() +
      theme(axis.text.x = element_text(angle = 45, hjust = 1))
  })
  
  # Word cloud
  # Filters data based on L value range (0-1)
  filtered_data <- reactive({
    foundation_words %>%
      filter(L >= input$lightness[1] & L <= input$lightness[2])
  })
  
  # Rendering word cloud
  output$shadenames_cloud <- renderWordcloud2({
    # Using foundation_words dataset and filtering based on Lightness
    data <- foundation_words %>%
      filter(L >= input$lightness[1], L <= input$lightness[2])
    
    word_freq <- data %>%
      group_by(ShadeWords) %>%
      summarize(freq = n(), .groups = "drop")  # Getting count of each shade word
    
    # Finding the lightest shade color for each shade word
    lightest_colors <- data %>%
      group_by(ShadeWords) %>% # Rows with same shade word are grouped
      filter(L == max(L)) %>%  # Selects the row with the highest L
      slice(1) %>%  # Take the first occurrence
      select(ShadeWords, ShadeColour) #Keeps only shade words and corresponding shade colour
    
    # Merge frequency and color data
    final_data <- left_join(word_freq, lightest_colors, by = "ShadeWords")
    
    # Making the word cloud with the lightest shade color
    wordcloud2(data.frame(word = final_data$ShadeWords, 
                          freq = final_data$freq),  
               size = 0.5, 
               color = as.vector(final_data$ShadeColour),
               backgroundColor = "white")
  })
  
  #name plot
  selected_shades <- reactive({
    if (input$shade_filter == "Darkest 15") {
      return(darkest_shades) 
    } else {
      return(lightest_shades)  
    }
  })
  
  #rendering plot based on shade groups
  output$nameplot <- renderPlotly({
    shades_data <- selected_shades()
    
#creating a plotly scatter plot with hover text
    plot_ly(
      data = shades_data, 
      x = ~ShadeWords,
      y = ~L, 
      type = 'scatter', 
      mode = 'markers',
      marker = list(
        color = ~ShadeColour, 
        size = 30,    
        opacity = 0.8
      ),
      text = ~paste("Brand:", Brand, "<br>Product Name:", ProductName), 
      hoverinfo = "text" 
    ) %>%
      layout(
        title = paste("Top 15", input$shade_filter, "Shades"),
        xaxis = list(title = "Shade Name", tickangle = 45),
        yaxis = list(title = "L (Lightness)")
      )
  })
}
# Run the Shiny app
shinyApp(ui = ui, server = server)
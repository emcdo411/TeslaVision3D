---

### Suggested Repository Name
**TeslaVision3D**

This name captures the essence of the project: advanced 3D visualizations for Tesla-related data, with a sleek, futuristic vibe that aligns with Tesla’s brand.

---

### README.md

```markdown
# TeslaVision3D: Futuristic 3D Visualizations for Tesla Data Insights 🚗⚡️

Welcome to **TeslaVision3D**, a collection of mind-blowing, interactive 3D visualizations built in R using Plotly to explore Tesla-related datasets. These visualizations go beyond traditional plots, offering a futuristic, Tesla-inspired experience with glowing holographic effects, animated networks, and immersive 3D globes. Perfect for data enthusiasts, Tesla fans, and anyone who loves cutting-edge data visualization!

## Project Overview

This repository contains four interactive 3D visualizations analyzing Tesla data from 2021 to 2025, focusing on Cybertruck service complaints, customer satisfaction (CSI) scores, and public opinion post-DOGE (Department of Government Efficiency). Each visualization is designed to be Tesla-worthy, featuring:
- **Sleek Design**: Black backgrounds, electric blue accents, and glowing nodes for a futuristic aesthetic.
- **Interactivity**: Rotate, zoom, and hover for details in each visualization.
- **Animation**: Watch data evolve over time with smooth transitions.

The visualizations are:
1. **3D Sankey Diagram**: Tracks Cybertruck service complaints (e.g., Accelerator Pedal issues) over time.
2. **3D Globe**: Maps CSI scores across U.S. regions, comparing pre- and post-Cybertruck periods.
3. **Animated 3D Network**: Shows how public opinion (e.g., Brand Trust) evolves post-DOGE.
4. **Holographic 3D Force-Directed Bubble Network**: A jaw-dropping visualization of public opinion with dynamic, glowing bubbles and pulsing links.

## Datasets

Below are the three datasets used in this project, provided in CSV format for easy replication.

### 1. `tesla_cybertruck_service_complaints_2021_2025.csv`
Tracks Cybertruck service complaints from 2023 to 2025.

```csv
Year,Complaint_Type,Frequency,Corrective_Action,Action_Details,Recall_ID
2023,Battery,50,Software Update,Optimized thermal management,N/A
2024,Accelerator Pedal,300,Recall,Replaced sticking pedal,NHTSA-24V-051
2024,Exterior Panel,120,Part Replacement,Fixed loose cant rail adhesive,NHTSA-24V-052
2025,Brakes,80,Software Update,Adjusted regenerative braking,N/A
2025,Electrical,150,Recall,Repaired wiring harness,NHTSA-25V-010
```

### 2. `tesla_csi_pre_post_cybertruck_2021_2025.csv`
Compares CSI scores across U.S. regions before and after the Cybertruck launch.

```csv
Region,Period,CSI_Score,Change_from_Prev,Campaign_or_Recall,Sample_Size
Northeast,Pre-Cybertruck 2021-2022,85,N/A,Model Y Surge,600
Northeast,Post-Recall 2024-2025,78,-7,Accelerator Recall,550
Midwest,Pre-Cybertruck 2021-2022,82,N/A,Supercharger Expansion,500
Midwest,Post-Recall 2024-2025,80,-2,Cybertruck Delays,480
California,Pre-Cybertruck 2021-2022,88,N/A,Giga Factory Buzz,700
California,Post-Recall 2024-2025,83,-5,Panel Recall,650
South,Pre-Cybertruck 2021-2022,80,N/A,Model 3 Boom,450
South,Post-Recall 2024-2025,76,-4,Accelerator Recall,430
West,Pre-Cybertruck 2021-2022,86,N/A,FSD Beta Rollout,620
West,Post-Recall 2024-2025,81,-5,Cybertruck Launch,590
```

### 3. `tesla_public_opinion_post_doge_2025.csv`
Captures public opinion on Tesla post-DOGE in 2025.

```csv
Poll_Date,Region,Opinion_Category,Positive_Pct,Negative_Pct,Neutral_Pct,Sample_Size,Source
2025-03-15,U.S.,Brand Trust,40,35,25,1000,Gallup
2025-03-20,California,Musk Leadership,55,30,15,800,Tesla Owners Club
2025-04-01,Global,Product Quality,45,40,15,1500,X Survey
2025-04-10,U.S.,Brand Trust,38,38,24,1200,Pew Research
2025-05-01,California,Product Quality,50,25,25,900,Tesla Owners Club
```

## Visualizations

Below are the R scripts for all four visualizations. Each script generates an interactive HTML file that you can open in a browser (Chrome or Firefox recommended).

### 1. 3D Sankey Diagram for Cybertruck Service Complaints
**File**: `tesla_cybertruck_sankey.R`  
**Output**: `tesla_cybertruck_sankey.html`  
**Description**: A 3D Sankey diagram showing the flow of Cybertruck service complaints from `Year` to `Complaint_Type` to `Corrective_Action`, with link thickness reflecting `Frequency`.

```R
# Clear the environment to avoid conflicts
rm(list = ls())

# Load libraries
library(plotly)
library(dplyr)

# Read the data
setwd("C:/Users/Veteran")
data <- read.csv("tesla_cybertruck_service_complaints_2021_2025.csv")

# Debug: Check column names
print("Column names in data:")
print(colnames(data))

# Ensure column names are correct (in case of spaces or formatting issues)
colnames(data) <- gsub(" ", "_", colnames(data))
print("Adjusted column names:")
print(colnames(data))

# Debug: Print the data to verify contents
print("Data contents:")
print(data)

# Prepare data for Sankey
nodes <- data.frame(name = unique(c(as.character(data$Year), 
                                    as.character(data$Complaint_Type), 
                                    as.character(data$Corrective_Action))))
nodes$index <- 0:(nrow(nodes) - 1)

# Add node colors for styling
nodes$color <- ifelse(nodes$name %in% as.character(data$Year), "white", 
                      ifelse(nodes$name %in% as.character(data$Complaint_Type), "lightcoral", "lightblue"))

# Debug: Print nodes to verify
print("Nodes for Sankey diagram:")
print(nodes)

# Create links
links <- data %>%
  mutate(source = match(Year, nodes$name) - 1,
         target = match(Complaint_Type, nodes$name) - 1) %>%
  mutate(target2 = match(Corrective_Action, nodes$name) - 1)

# Print the links dataframe to inspect source, target, and target2
print("Links dataframe with source, target, and target2 indices:")
print(links)

# Combine links for Sankey (Year -> Complaint_Type -> Corrective_Action)
links1 <- data.frame(source = links$source, target = links$target, value = links$Frequency,
                     hover_text = paste("Frequency:", links$Frequency))
links2 <- data.frame(source = links$target, target = links$target2, value = links$Frequency,
                     hover_text = paste("Action:", links$Corrective_Action, "<br>Details:", links$Action_Details, "<br>Recall ID:", links$Recall_ID))
all_links <- rbind(links1, links2)

# Debug: Print all_links to verify
print("All links for Sankey diagram:")
print(all_links)

# Create 3D Sankey with Tesla-inspired styling
fig <- plot_ly(
  type = "sankey",
  node = list(
    label = nodes$name,
    color = nodes$color,
    pad = 15,
    thickness = 20,
    line = list(color = "white", width = 1)
  ),
  link = list(
    source = all_links$source,
    target = all_links$target,
    value = all_links$value,
    color = "rgba(0, 191, 255, 0.7)",  # Tesla electric blue
    hoverinfo = "text",
    hovertext = all_links$hover_text
  )
) %>%
  layout(
    title = list(
      text = "Cybertruck Service Complaints Flow (2023-2025)",
      font = list(size = 16, color = "white"),
      y = 0.95
    ),
    font = list(size = 12, color = "white"),
    paper_bgcolor = "black",
    plot_bgcolor = "black",
    scene = list(
      aspectmode = "manual",
      aspectratio = list(x = 1, y = 1, z = 0.5)
    )
  )

# Display the plot in RStudio to confirm it renders
print(fig)

# Save as HTML with self-contained option
htmlwidgets::saveWidget(fig, "tesla_cybertruck_sankey.html", selfcontained = TRUE)
```

### 2. 3D Globe for Tesla CSI Scores
**File**: `tesla_csi_globe.R`  
**Output**: `tesla_csi_globe.html`  
**Description**: A 3D globe showing CSI scores across U.S. regions, with markers sized by `CSI_Score` and colored by `Period` (Pre-Cybertruck vs. Post-Recall).

```R
# Clear the environment to avoid conflicts
rm(list = ls())

# Load libraries
library(plotly)
library(dplyr)

# Read the data
setwd("C:/Users/Veteran")
data <- read.csv("tesla_csi_pre_post_cybertruck_2021_2025.csv")

# Debug: Check column names
print("Column names in data:")
print(colnames(data))

# Ensure column names are correct (in case of spaces or formatting issues)
colnames(data) <- gsub(" ", "_", colnames(data))
print("Adjusted column names:")
print(colnames(data))

# Debug: Print the data to verify contents
print("Data contents:")
print(data)

# Add approximate lat/lon for regions (simplified for demo)
data <- data %>%
  mutate(Lat = case_when(
    Region == "Northeast" ~ 42.0,
    Region == "Midwest" ~ 41.0,
    Region == "California" ~ 36.0,
    Region == "South" ~ 33.0,
    Region == "West" ~ 39.0
  ),
  Lon = case_when(
    Region == "Northeast" ~ -73.0,
    Region == "Midwest" ~ -87.0,
    Region == "California" ~ -120.0,
    Region == "South" ~ -84.0,
    Region == "West" ~ -110.0
  ))

# Debug: Print data with lat/lon
print("Data with latitude and longitude:")
print(data)

# Create 3D globe with Tesla-inspired styling
fig <- plot_ly(data, type = "scattergeo", mode = "markers",
               lat = ~Lat, lon = ~Lon, 
               marker = list(
                 size = ~CSI_Score * 2,
                 color = ~ifelse(Period == "Pre-Cybertruck 2021-2022", "white", "lightblue"),
                 opacity = 0.8,
                 line = list(color = "white", width = 1)
               ),
               text = ~paste(Region, "<br>Period:", Period, "<br>CSI Score:", CSI_Score, 
                             "<br>Change:", Change_from_Prev, "<br>Event:", Campaign_or_Recall),
               hoverinfo = "text") %>%
  layout(
    title = list(
      text = "Tesla CSI Scores: Pre vs. Post Cybertruck (2021-2025)",
      font = list(size = 16, color = "white"),
      y = 0.95
    ),
    font = list(color = "white"),
    paper_bgcolor = "black",
    plot_bgcolor = "black",
    geo = list(
      scope = "usa",
      projection = list(type = "orthographic"),
      showland = TRUE,
      landcolor = "rgb(50, 50, 50)",
      countrycolor = "rgb(100, 100, 100)",
      showocean = TRUE,
      oceancolor = "rgb(0, 0, 50)"
    )
  )

# Display the plot in RStudio to confirm it renders
print(fig)

# Save as HTML with self-contained option
htmlwidgets::saveWidget(fig, "tesla_csi_globe.html", selfcontained = TRUE)
```

### 3. Animated 3D Network for Public Opinion Post-DOGE
**File**: `tesla_opinion_network.R`  
**Output**: `tesla_opinion_network.html`  
**Description**: An animated 3D network showing how public opinion (e.g., Brand Trust) evolves over `Poll_Date`, with link thickness reflecting `Positive_Pct`.

```R
# Clear the environment to avoid conflicts
rm(list = ls())

# Load libraries
library(plotly)
library(dplyr)

# Read the data
setwd("C:/Users/Veteran")
data <- read.csv("tesla_public_opinion_post_doge_2025.csv")

# Debug: Check column names
print("Column names in data:")
print(colnames(data))

# Ensure column names are correct (in case of spaces or formatting issues)
colnames(data) <- gsub(" ", "_", colnames(data))
print("Adjusted column names:")
print(colnames(data))

# Debug: Print the data to verify contents
print("Data contents:")
print(data)

# Prepare nodes and links
nodes <- data.frame(name = unique(c(as.character(data$Opinion_Category), 
                                    as.character(data$Region))))
nodes$index <- 0:(nrow(nodes) - 1)

# Add node colors for styling
nodes$color <- ifelse(nodes$name %in% as.character(data$Opinion_Category), "lightcoral", "white")

# Debug: Print nodes to verify
print("Nodes for network:")
print(nodes)

# Create links
links <- data %>%
  mutate(source = match(Opinion_Category, nodes$name) - 1,
         target = match(Region, nodes$name) - 1,
         frame = as.character(Poll_Date))

# Debug: Print links to verify
print("Links for network:")
print(links)

# Generate 3D coordinates for nodes (random for visualization)
set.seed(123)
nodes$x <- runif(nrow(nodes), 0, 10)
nodes$y <- runif(nrow(nodes), 0, 10)
nodes$z <- runif(nrow(nodes), 0, 10)

# Prepare link coordinates and expand frame and text to match
link_x <- unlist(lapply(1:nrow(links), function(i) c(
  nodes$x[links$source[i] + 1], 
  nodes$x[links$target[i] + 1], 
  NA)))
link_y <- unlist(lapply(1:nrow(links), function(i) c(
  nodes$y[links$source[i] + 1], 
  nodes$y[links$target[i] + 1], 
  NA)))
link_z <- unlist(lapply(1:nrow(links), function(i) c(
  nodes$z[links$source[i] + 1], 
  nodes$z[links$target[i] + 1], 
  NA)))

# Expand frame and text to match the length of x, y, z (repeat each value 3 times)
link_frame <- rep(links$frame, each = 3)
link_text <- rep(paste("Positive %:", links$Positive_Pct, "<br>Sample Size:", links$Sample_Size), each = 3)

# Debug: Print lengths to verify
print("Length of link_x, link_y, link_z:")
print(length(link_x))
print("Length of link_frame:")
print(length(link_frame))
print("Length of link_text:")
print(length(link_text))

# Create 3D network with animation
fig <- plot_ly() %>%
  add_trace(
    type = "scatter3d",
    mode = "markers",
    x = nodes$x,
    y = nodes$y,
    z = nodes$z,
    text = nodes$name,
    hoverinfo = "text",
    marker = list(
      size = 10,
      color = nodes$color,
      opacity = 0.8,
      line = list(color = "white", width = 1)
    )
  ) %>%
  add_trace(
    type = "scatter3d",
    mode = "lines",
    x = link_x,
    y = link_y,
    z = link_z,
    line = list(
      width = links$Positive_Pct / 5,
      color = "rgba(0, 191, 255, 0.7)"
    ),
    frame = link_frame,
    hoverinfo = "text",
    text = link_text
  ) %>%
  layout(
    title = list(
      text = "Tesla Public Opinion Network Post-DOGE (2025)",
      font = list(size = 16, color = "white"),
      y = 0.95
    ),
    font = list(color = "white"),
    paper_bgcolor = "black",
    plot_bgcolor = "black",
    scene = list(
      xaxis = list(title = "", showgrid = FALSE, zeroline = FALSE, showticklabels = FALSE),
      yaxis = list(title = "", showgrid = FALSE, zeroline = FALSE, showticklabels = FALSE),
      zaxis = list(title = "", showgrid = FALSE, zeroline = FALSE, showticklabels = FALSE)
    ),
    showlegend = FALSE,
    updatemenus = list(
      list(
        type = "buttons",
        buttons = list(
          list(method = "animate", args = list(NULL, NULL), label = "Play")
        )
      )
    )
  )

# Display the plot in RStudio to confirm it renders
print(fig)

# Save as HTML with self-contained option
htmlwidgets::saveWidget(fig, "tesla_opinion_network.html", selfcontained = TRUE)
```

### 4. Holographic 3D Force-Directed Bubble Network for Public Opinion
**File**: `tesla_opinion_holographic_network.R`  
**Output**: `tesla_opinion_holographic_network.html`  
**Description**: A holographic 3D force-directed bubble network with glowing nodes (sized by `Sample_Size`, colored by `Positive_Pct`) and pulsing links, animated over `Poll_Date`.

```R
# Clear the environment to avoid conflicts
rm(list = ls())

# Load libraries
library(plotly)
library(dplyr)

# Read the data
setwd("C:/Users/Veteran")
data <- read.csv("tesla_public_opinion_post_doge_2025.csv")

# Debug: Check column names
print("Column names in data:")
print(colnames(data))

# Ensure column names are correct (in case of spaces or formatting issues)
colnames(data) <- gsub(" ", "_", colnames(data))
print("Adjusted column names:")
print(colnames(data))

# Debug: Print the data to verify contents
print("Data contents:")
print(data)

# Prepare nodes and links
nodes <- data.frame(name = unique(c(as.character(data$Opinion_Category), 
                                    as.character(data$Region))))
nodes$index <- 0:(nrow(nodes) - 1)

# Simulate node types (for coloring)
nodes$type <- ifelse(nodes$name %in% as.character(data$Opinion_Category), "Opinion", "Region")

# Debug: Print nodes to verify
print("Nodes for network:")
print(nodes)

# Create links
links <- data %>%
  mutate(source = match(Opinion_Category, nodes$name) - 1,
         target = match(Region, nodes$name) - 1,
         frame = as.character(Poll_Date))

# Debug: Print links to verify
print("Links for network:")
print(links)

# Simulate a force-directed layout (simple physics simulation)
set.seed(123)
n_nodes <- nrow(nodes)
positions <- data.frame(
  x = runif(n_nodes, 0, 10),
  y = runif(n_nodes, 0, 10),
  z = runif(n_nodes, 0, 10)
)

# Simple force-directed simulation: repulsion between nodes, attraction along links
n_iterations <- 50
for (iter in 1:n_iterations) {
  # Repulsion: push nodes apart
  for (i in 1:(n_nodes - 1)) {
    for (j in (i + 1):n_nodes) {
      dx <- positions$x[i] - positions$x[j]
      dy <- positions$y[i] - positions$y[j]
      dz <- positions$z[i] - positions$z[j]
      dist <- sqrt(dx^2 + dy^2 + dz^2)
      if (dist < 1) dist <- 1
      force <- 1 / (dist^2)
      positions$x[i] <- positions$x[i] + dx / dist * force * 0.1
      positions$y[i] <- positions$y[i] + dy / dist * force * 0.1
      positions$z[i] <- positions$z[i] + dz / dist * force * 0.1
      positions$x[j] <- positions$x[j] - dx / dist * force * 0.1
      positions$y[j] <- positions$y[j] - dy / dist * force * 0.1
      positions$z[j] <- positions$z[j] - dz / dist * force * 0.1
    }
  }
  
  # Attraction: pull connected nodes closer
  for (k in 1:nrow(links)) {
    i <- links$source[k] + 1
    j <- links$target[k] + 1
    dx <- positions$x[i] - positions$x[j]
    dy <- positions$y[i] - positions$y[j]
    dz <- positions$z[i] - positions$z[j]
    dist <- sqrt(dx^2 + dy^2 + dz^2)
    force <- dist * 0.05
    positions$x[i] <- positions$x[i] - dx / dist * force
    positions$y[i] <- positions$y[i] - dy / dist * force
    positions$z[i] <- positions$z[i] - dz / dist * force
    positions$x[j] <- positions$x[j] + dx / dist * force
    positions$y[j] <- positions$y[j] + dy / dist * force
    positions$z[j] <- positions$z[j] + dz / dist * force
  }
}

# Add positions to nodes
nodes$x <- positions$x
nodes$y <- positions$y
nodes$z <- positions$z

# Debug: Print node positions
print("Node positions after force-directed simulation:")
print(nodes)

# Prepare link coordinates and expand frame and text to match
link_x <- unlist(lapply(1:nrow(links), function(i) c(
  nodes$x[links$source[i] + 1], 
  nodes$x[links$target[i] + 1], 
  NA)))
link_y <- unlist(lapply(1:nrow(links), function(i) c(
  nodes$y[links$source[i] + 1], 
  nodes$y[links$target[i] + 1], 
  NA)))
link_z <- unlist(lapply(1:nrow(links), function(i) c(
  nodes$z[links$source[i] + 1], 
  nodes$z[links$target[i] + 1], 
  NA)))

# Expand frame and text to match the length of x, y, z (repeat each value 3 times)
link_frame <- rep(links$frame, each = 3)
link_text <- rep(paste("Positive %:", links$Positive_Pct, "<br>Negative %:", links$Negative_Pct, 
                       "<br>Sample Size:", links$Sample_Size, "<br>Source:", links$Source), each = 3)

# Debug: Print lengths to verify
print("Length of link_x, link_y, link_z:")
print(length(link_x))
print("Length of link_frame:")
print(length(link_frame))
print("Length of link_text:")
print(length(link_text))

# Create node sizes and colors based on the latest data for each node
node_sizes <- rep(5, nrow(nodes))
node_colors <- rep(50, nrow(nodes))
for (i in 1:nrow(nodes)) {
  if (nodes$type[i] == "Region") {
    region_data <- data %>% filter(Region == nodes$name[i])
    if (nrow(region_data) > 0) {
      latest_row <- region_data %>% arrange(desc(Poll_Date)) %>% slice(1)
      node_sizes[i] <- latest_row$Sample_Size / 100
      node_colors[i] <- latest_row$Positive_Pct
    }
  } else {
    opinion_data <- data %>% filter(Opinion_Category == nodes$name[i])
    if (nrow(opinion_data) > 0) {
      latest_row <- opinion_data %>% arrange(desc(Poll_Date)) %>% slice(1)
      node_sizes[i] <- latest_row$Sample_Size / 100
      node_colors[i] <- latest_row$Positive_Pct
    }
  }
}

# Create 3D force-directed bubble network with holographic effects
fig <- plot_ly() %>%
  add_trace(
    type = "scatter3d",
    mode = "markers",
    x = nodes$x,
    y = nodes$y,
    z = nodes$z,
    text = nodes$name,
    hoverinfo = "text",
    marker = list(
      size = node_sizes,
      color = node_colors,
      colorscale = "RdYlGn",
      opacity = 0.7,
      line = list(color = "rgba(255, 255, 255, 0.8)", width = 2),
      showscale = TRUE,
      colorbar = list(title = "Positive %")
    )
  ) %>%
  add_trace(
    type = "scatter3d",
    mode = "lines",
    x = link_x,
    y = link_y,
    z = link_z,
    line = list(
      width = links$Positive_Pct / 5,
      color = "rgba(0, 191, 255, 0.7)"
    ),
    frame = link_frame,
    hoverinfo = "text",
    text = link_text
  ) %>%
  layout(
    title = list(
      text = "Tesla Public Opinion Holographic Network (2025)",
      font = list(size = 16, color = "white"),
      y = 0.95
    ),
    font = list(color = "white"),
    paper_bgcolor = "black",
    plot_bgcolor = "black",
    scene = list(
      xaxis = list(title = "", showgrid = FALSE, zeroline = FALSE, showticklabels = FALSE),
      yaxis = list(title = "", showgrid = FALSE, zeroline = FALSE, showticklabels = FALSE),
      zaxis = list(title = "", showgrid = FALSE, zeroline = FALSE, showticklabels = FALSE),
      bgcolor = "black"
    ),
    showlegend = FALSE,
    updatemenus = list(
      list(
        type = "buttons",
        buttons = list(
          list(method = "animate", args = list(NULL, NULL), label = "Play")
        )
      )
    )
  )

# Display the plot in RStudio to confirm it renders
print(fig)

# Save as HTML with self-contained option
htmlwidgets::saveWidget(fig, "tesla_opinion_holographic_network.html", selfcontained = TRUE)
```

## How to Run

1. **Install R and RStudio**: Ensure you have R and RStudio installed on your system.
2. **Install Required Packages**: Run the following in R to install the necessary libraries:
   ```R
   install.packages(c("plotly", "dplyr", "htmlwidgets"))
   ```
3. **Set Up the Data**:
   - Create a directory (e.g., `C:/Users/Veteran`).
   - Save the three CSV files (`tesla_cybertruck_service_complaints_2021_2025.csv`, `tesla_csi_pre_post_cybertruck_2021_2025.csv`, `tesla_public_opinion_post_doge_2025.csv`) in this directory using the data provided above.
4. **Run the Scripts**:
   - Open each R script in RStudio.
   - Update the `setwd("C:/Users/Veteran")` line to match the directory where your CSV files are stored.
   - Run each script (`Ctrl+Shift+S` in RStudio) to generate the HTML files.
5. **View the Visualizations**:
   - Open the generated HTML files (e.g., `tesla_cybertruck_sankey.html`) in a browser (Chrome or Firefox recommended).
   - Interact with the visualizations: rotate, zoom, hover for details, and click "Play" for animations.

## Why It Matters

These visualizations are more than just eye candy—they provide deep insights into Tesla’s performance and public perception in a way that’s both intuitive and engaging:
- **Service Complaints (Sankey Diagram)**: Highlights critical issues like the Accelerator Pedal recall (300 complaints in 2024), helping Tesla identify areas for improvement in Cybertruck production and customer service.
- **CSI Scores (3D Globe)**: Reveals regional variations in customer satisfaction, such as California’s drop from 88 to 83 post-recall, which could inform targeted marketing or service campaigns.
- **Public Opinion (Network and Holographic Network)**: Tracks sentiment shifts post-DOGE, showing how opinions like Musk Leadership (55% positive in California) evolve, which is crucial for understanding brand perception in a politically charged environment.
- **Futuristic Presentation**: The holographic, 3D designs align with Tesla’s innovative brand, making these visualizations ideal for presentations to stakeholders, investors, or at tech conferences. They demonstrate how data can be both informative and visually stunning, setting a new standard for data storytelling.

## Conclusion

**TeslaVision3D** pushes the boundaries of data visualization, transforming raw Tesla data into immersive, futuristic experiences. Whether you’re analyzing Cybertruck complaints, mapping customer satisfaction, or tracking public sentiment, these visualizations offer a fresh, engaging perspective. The combination of 3D effects, animation, and Tesla-inspired styling makes this project a standout for data scientists, Tesla enthusiasts, and anyone who loves innovative tech.

Feel free to fork this repository, experiment with the scripts, or adapt them for your own datasets. Let’s keep pushing the limits of what data visualization can achieve! 🚗⚡️

---

**Created by [Maurice McDonald]**  
**Date**: March 22, 2025  
**License**: MIT License
```

---

### How to Set Up the Repository
1. **Create the Repository**:
   - Go to GitHub and create a new repository named `TeslaVision3D`.
2. **Add Files**:
   - Create a `README.md` file and paste the content above.
   - Create a `data/` directory and add the three CSV files:
     - `data/tesla_cybertruck_service_complaints_2021_2025.csv`
     - `data/tesla_csi_pre_post_cybertruck_2021_2025.csv`
     - `data/tesla_public_opinion_post_doge_2025.csv`
   - Create a `scripts/` directory and add the four R scripts:
     - `scripts/tesla_cybertruck_sankey.R`
     - `scripts/tesla_csi_globe.R`
     - `scripts/tesla_opinion_network.R`
     - `scripts/tesla_opinion_holographic_network.R`
3. **Update the Scripts**:
   - In each R script, update the `setwd("C:/Users/Veteran")` line to `setwd("data/")` to point to the `data/` directory in the repository.
4. **Commit and Push**:
   - Commit all files to the repository and push to GitHub.
5. **Add a License**:
   - Add an MIT License file to the repository for open-source sharing.

---

### Final Notes
This README is designed to be professional, engaging, and GitHub-ready, with clear instructions for replication and a compelling narrative about the project’s value. The visualizations are showcased with their full code, and the "Why It Matters" section ties the technical work to real-world impact. You’re all set to share this with the world! 🚗⚡️

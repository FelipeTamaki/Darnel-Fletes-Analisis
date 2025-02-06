# Overview

Welcome to my analysis of freight and transportation for "Darnel Group." This project was developed with the goal of fostering both my personal growth and contributing to the company from an analytical perspective. It explores convenient freight options and strategies to minimize costs when transporting to different locations.

The data was provided by the company: 
[FLETES LARRAZ 2024](./FLETES%20LARRAZ%202024.xlsx), which provides a foundation for my analysis, containing detailed information on freight shipments, including pricing, service details, and tracking information. Through a series of Python scripts, I explore key questions such as the most visited locations, cost per location, the percentage of freight relative to revenue for "Darmel Group"

# The Questions

1. What are the most frequented destinations and how are they related to the average cost per trip?

2. How does the freight cost relate to the declared value of the sale?

3. Which of the two service providers offers the most cost-effective solution?

4. What is the relationship between the number of destinations and the cost of the freight for both providers?


# Tools I Used
For my deep dive into Darmel´s transportation, I harnessed the power of several key tools:

* Python: The backbone of my analysis, allowing me to analyze the data and find critical insights.I also used the following Python libraries:
  * Pandas Library: This was used to analyze the data.
  * Matplotlib Library: I visualized the data.
  * Seaborn Library: Helped me create more advanced visuals.
  * Jupyter Notebooks: The tool I used to run my Python scripts which let me easily include my notes and analysis.
  * Visual Studio Code: My go-to for executing my Python scripts.
  * Git & GitHub: Essential for version control and sharing my Python code and analysis, ensuring collaboration and project tracking.

# Data Preparation and Cleanup
This section outlines the steps taken to prepare the data for analysis, ensuring accuracy and usability.

# Import & Clean Up Data
I start by importing necessary libraries and loading the dataset, followed by initial data cleaning tasks to ensure data quality.

```python

# Importing Libraries
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt
import numpy as np

# Loading Data
df = pd.read_excel("FLETES LARRAZ 2024.xlsx", sheet_name="ENERO 2025")

## Dropping Unnecesary rows and columns
df.drop(0,inplace=True)
df.drop(1,inplace=True)
df.drop(3,inplace=True)
df.drop(4,inplace=True)
df.drop(columns="Unnamed: 0",inplace=True)
df.drop(columns="Unnamed: 11",inplace=True)
df.drop(columns="Unnamed: 12",inplace=True)
df.drop(columns="Unnamed: 13",inplace=True)
df.drop(columns="Unnamed: 15",inplace=True)
df.drop(columns="Unnamed: 16",inplace=True)
df = df.drop(df.columns[-2:], axis=1)


# The values in the first row are assigned as the title of each column
df= df.rename(columns=df.iloc[0]).drop(df.index[0]).reset_index(drop=True)
```

# Data Used for My Analysis

For this analysis, I selected data from January 2025, as it was the most reliable and consistent dataset recommended by the company. Other months exhibited more inconsistencies, which could affect the accuracy of the results. Additionally, due to "Darnel Group" frequently changing clients, there is considerable variation in the locations, which could impact data visualization.

While the analysis is based on January 2025 data, this choice is not a limitation. The Python script is flexible and can be easily adapted to work with any month by modifying the sheet name in the following line of code:

```python
df = pd.read_excel("FLETES LARRAZ 2024.xlsx", sheet_name="ENERO 2025")
```

# The Analysis
## 1. What are the most frequented destinations and how are they related to the average cost per trip?

I began by processing the dataset and grouping the data by location to analyze the frequency of trips. Using aggregation functions, I was able to determine how many trips and the average cost per destination.  This query highlights key patterns in the data, providing insights into how the frequency of trips might influence or be influenced by the costs of transportation to different destinations.

View my notebook with detailed steps here: [Analysis by location](1_Analysis%20by%20location.ipynb)

### Visualize data

```python
sns.barplot(data=df_localidades, x="LOCALIDAD", y="VIAJES", ax=ax[0])

sns.barplot(x="LOCALIDAD", y="COSTO POR VIAJE", data=df_localidades, ax=ax[1])
ax[1].set_title("Costos Promedio por Localidad")
ax[1].yaxis.set_major_formatter(mticker.FuncFormatter(lambda x, _: f'{x:,.0f}'))
```
### Results
![Visualization By Location](Images\Analisis%20per%20location.png)

### Insights

The prominence of CABA suggests it serves as a major hub, likely due to its status as the economic, administrative, and cultural center of Argentina. This might reflect a combination of:
* High population density.
* Centralized business districts 
* attracting workers and visitors.
* Well-developed infrastructure encouraging travel.

The rapid decline in trip frequency across other localities indicates that travel demand is unevenly distributed, likely influenced by economic activity and population concentration in those areas.

Rosario stands out with an exceptionally high average cost per trip, significantly surpassing all other localities. This is due to the fact that trips to Rosario cover a much longer distance in comparison to the rest of the trips, consequently having much more higher costs.

For most other localities, the average costs are relatively uniform, suggesting similar trip lengths or pricing structures.

## 2. How does the freight cost relate to the declared value of the sale per location?

To find how is the freight cost relate to the declared value of the sale. I filtered per location, the total cost of all trips and the declared value in January 2025. With this columns we can calculate the percentage of the declared value that has to be spend in the freight. 

View my notebook with detailed steps here: [Percentage of declared value per location](2_Percentage%20of%20Declared%20Value%20per%20Location.ipynb)

### Visualize data

```python
fig,ax = plt.subplots()
sns.barplot(x="LOCALIDAD", y="PORCENTAJE VALOR DECLARADO", data= df_localidades, ax=ax)
ax.set_title("Porcentaje Valor Declarado por Localidad")
ax.set_xticklabels(df_localidades["LOCALIDAD"].unique(), rotation=90)
```
### Results
![Visualization By Location](Images\Porcentaje%20valor%20declarado.png)

### Insights
It´s important to mention, that the optimal percentage is less than 5%.

Rosario stands out prominently, with more than 30% of its transactions involving a declared value, significantly surpassing all other localities.

Localities like Gerli and Burzaco show moderately higher percentages (around 10–15%), positioning them as the second and third highest after Rosario. This is due to their increase in distance between Darmel´s factory and their delivery point.

Localities such as CABA, Ciudadela, and Morón are relatively consistent in having a small percentage of declared value, indicating uniform behavior across these regions. 

## 3. Which of the two service providers offers the most cost-effective solution?

To find out which service provider a service that better meets the economic needs of the company we will use some data given by Darmel´s general manager.

#### First option (Provider A):
The first provider charges depending on the location and the amount of destinations of the freight.

Base price 
* $186600 for CABA
* $195600 for Buenos Aires
* 217200 for La Plata, Berazategui, Ensenada, between others.
* $354000 for Rosario
* $55500 For each destination added 

#### Second Option (Provider B):
Charges a fix based price depending on the amount of destinations of the freight.

* Base price: $232925
* $10700 For Each Destination added after the 5th Destination

To analyze both options, we will filter the data by flight and count the number of trips, adding two columns that show the price of both providers.

View my notebook with detailed steps here: [Providers analysis](3_4%20Providers%20analysis.ipynb)

### Visualize Data
``` python
default_palette = sns.color_palette("deep")
reversed_palette = [default_palette[1], default_palette[0]]

sns.countplot(data=df_viajes, x="LOCALIDAD", hue="Opcion_Conveniente", palette=reversed_palette, ax=ax[0])

sns.countplot(data=df_viajes, x="Cantidad_de_Destinos", hue="Opcion_Conveniente",palette="deep", ax=ax[1])

max_value = df_viajes["Cantidad_de_Destinos"].value_counts().max()
ax[1].set_yticks(np.arange(0, max_value + 1, step=1))
```

### Results
![Convinient provider](Images\Proveedor%20conveniente.png)

### Insights

Provider B consistently proves to be the most convenient option across all locations and destinations, showcasing its dominance regardless of geographical area. Whether in major localities like Buenos Aires and CABA or smaller ones such as Ciudadela, Lomas del Mirador, Avellaneda, and Morón, Provider_B is the preferred choice, often with a significant margin. Similarly, when considering the number of destinations, Provider B remains the top option in almost every scenario. The only exception arises when there is just one trip, where Provider A gains notable representation, standing out as a viable alternative.

## 4. What is the relationship between the number of destinations and the cost of the freight?

It´s important to clarify that trips to Rosario are not going to be considered, as a consecuence of their elevated costs shown and explained prevously.

To find out how does the cost of the freight changes depending on the amount of trips done by it, we can use the dataframe we obtained before. This is due to the data needed in order to depict this information, which include the amount of destination of each truck and the costs of each provider. 

View my notebook with detailed steps here: [Analysis providers](3_4%20Providers%20analysis.ipynb)
### Visualize Data
``` python
df_viajes.loc[df_viajes["Cantidad_de_Destinos"]<=5, "PROVEEDOR_B"] = 232925
df_viajes.loc[df_viajes["Cantidad_de_Destinos"]>5, "PROVEEDOR_B"] = 232925 + (df_viajes["Cantidad_de_Destinos"]-5) * 10700
plt.figure(figsize=(8, 6))
sns.scatterplot(data=df_viajes, x="Cantidad_de_Destinos", y="PROVEEDOR_A", label="Proveedor A", color="blue")
sns.scatterplot(data=df_viajes, x="Cantidad_de_Destinos", y="PROVEEDOR_B", label="Proveedor B", color="red")
```

### Results
![Relation between amount of destinations and both providers](Images\Relacion%20costo%20por%20proveedor%20y%20cantidad%20de%20destinos.png)

### Insights
Cost Differences Between Providers:

* "Provider A" has significantly higher costs compared to "Provider B" as the number of destinations increases.
* "Provider B" maintains relatively stable costs across different numbers of destinations, with only slight variations.

Competitive Advantage:
* For lower destination numbers (1-3), the cost difference between the two providers is smaller.
* For higher destination numbers (4+), "Proveedor B" appears to be significantly more cost-effective

# What I Learned
Throughout this project, I deepened my understanding on how companies work with services providers and enhanced my technical skills in Python, especially in data manipulation and visualization. Here are a few specific things I learned:

* Advanced Python Usage: Utilizing libraries such as Pandas for data manipulation, Seaborn and Matplotlib for data visualization, and other libraries helped me perform complex data analysis tasks more efficiently.

* Data Cleaning Importance: I learned that thorough data cleaning and preparation are crucial before any analysis can be conducted, ensuring the accuracy of insights derived from the data.

* Realistic Business Case: Throughout this project I was able to use all my business knowledge in a real case scenario. Having previous knowledge, helped me to analyze the data with detail and dig deeper into it and thus obtatining the best possible conclusions.

## Insights
This project provided several general insights into Darmel´s transport logistics:

* Uneven Distribution of Trips and Costs: CABA is the most frequently traveled destination due to its economic and transportation centrality, while Rosario has a significantly higher average trip cost due to its greater distance.

* Relationship Between Freight Cost and Declared Value: Most locations have a freight cost percentage below 5% of the declared value, but Rosario stands out with over 30%, making it a high-cost outlier.

* Provider B is More Cost-Effective in Most Cases: Provider B is the most economical option in almost all scenarios, except when there is only one destination in the trip, where Provider A becomes competitive.

* Freight Cost and Number of Destinations: As the number of destinations increases, Provider A's cost rises significantly, while Provider B maintains a more controlled cost increase, making it the better option for multiple destinations.

# Challenges I Faced

This project was not without its challenges, but it provided good learning opportunities:

Data Inconsistencies: Handling missing or inconsistent data entries requires careful consideration and thorough data-cleaning techniques to ensure the integrity of the analysis.

Complex Data Visualization: Designing effective visual representations of complex datasets was challenging but critical for conveying insights clearly and compellingly.

Balancing Breadth and Depth: Deciding how deeply to dive into each analysis while maintaining a broad overview of the data landscape required constant balancing to ensure comprehensive coverage without getting lost in details.

# Conclusion

This exploration has been a great learning experience, allowing me to apply my technical skills and business knowledge to a real-world case. By analyzing Darmel Group’s freight data, I gained valuable insights into cost structures, provider efficiency, and transport logistics.

A key takeaway is that Provider B is consistently the most cost-effective option, especially when multiple destinations are involved. Additionally, understanding the uneven distribution of trips and costs helped clarify why locations like Rosario incur significantly higher freight expenses. Identifying cost percentages relative to the declared value also provided a clear picture of which destinations require closer cost monitoring.

Beyond the technical aspects, this project reinforced the importance of data cleaning and preparation in ensuring accurate and reliable insights. It also highlighted how data visualization is essential for communicating findings effectively.
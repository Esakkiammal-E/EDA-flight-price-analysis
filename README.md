#Loading the Dataset

Flight = pd.read_csv(“D:\\flight data.csv”)
#This line reads a CSV file located at the specified path into a DataFrame called Flight.

Flight.head()
# Displays the first 5 rows of the DataFrame to inspect the top records of the dataset.

Flight.tail()
#Displays the last 5 rows of the DataFrame to inspect the bottom records.

#Checking for Missing Values
Flight.isnull().sum()
#This checks for missing (null) values in the entire dataset. The isnull() function returns a DataFrame of boolean values indicating where null values exist, and .sum() counts the number of missing values in each column.

#Data Overview
Flight.info()
#This method provides an overview of the DataFrame, including the number of rows, columns, non-null values, and data types.

Flight.describe().T
#Generates descriptive statistics (mean, standard deviation, min, max, quartiles) for numerical columns. The .T transposes the DataFrame to make the columns easier to read as rows.

Flight.describe(include='object')
#Provides descriptive statistics for categorical columns (like count, unique values, top frequency).

#Data Cleaning
Flight.head().style.hide(axis='index')
#Hides the index column from the displayed output of the DataFrame. The axis='index' specifies that the index (row labels) should be hidden.

#Data Visualization and Insights
#Airlines Count
Airline = flight['airline'].value_counts()
#This line counts the occurrences of each unique airline in the airline column and stores it in the variable Airline.

plt.figure(figsize=(10, 6))
#Creates a new figure of size 10x6 inches for the plot.

sns.barplot(x=Airline.index, y=Airline.values, palette='viridis')
#Uses Seaborn's barplot() to plot the count of flights per airline. Airline.index refers to the airline names, and Airline.values are the corresponding counts. The palette='viridis' is used to define the color palette.

plt.xticks(rotation=45)
#Rotates the x-axis labels (airline names) by 45 degrees for better readability.

plt.title(“Airlines Count”)
#Adds a title to the plot.

plt.xlabel(“Airlines”)
#Labels the x-axis as "Airlines."

plt.ylabel(“Count”)
#Labels the y-axis as "Count."

plt.show()
#Displays the bar plot.

#Departure Time vs. Arrival Time
plt.figure(figsize=(8, 5))
#Sets up a figure of size 8x5 inches.

sns.barplot(x='departure_time', y='arrival_time', data=flight, color='cyan')
#Creates a bar plot that compares departure times (x) with arrival times (y), using the flight DataFrame and a cyan color for the bars.

plt.title('Departure Time vs Arrival Time')
#Adds the plot title.

plt.xlabel('Departure Time')
#Labels the x-axis as "Departure Time."

plt.ylabel('Arrival Time')
#Labels the y-axis as "Arrival Time."

plt.show()
#Displays the plot.


6.3. Source City vs. Destination City

data = flight.groupby(['source_city', 'destination_city']).size().unstack()
#Groups the dataset by source_city and destination_city, counts the number of flights for each city pair, and uses unstack() to pivot the grouped data into a matrix-like format where rows are source_city and columns are destination_city.

data.plot(kind='bar', stacked=True, figsize=(10, 6), colormap='plasma')
#Creates a stacked bar plot for the grouped data. stacked=True means bars for different destination cities are stacked on top of each other. The colormap='plasma' adds a color scheme to the plot.

plt.title('Source City vs Destination City')
#Sets the title of the plot.

plt.xlabel('Source City')
#Labels the x-axis as "Source City."

plt.ylabel('Number of Flights')
#Labels the y-axis as "Number of Flights."

plt.xticks(rotation=45)
#Rotates the x-axis labels by 45 degrees.

plt.legend(title='Destination City')
#Adds a legend with the title "Destination City."

plt.tight_layout()
#Adjusts the layout so the elements fit better.

plt.show()
#Displays the stacked bar plot.


#Price Variance Across Airlines
plt.figure(figsize=(8, 5))
#Sets up the figure with dimensions 8x5 inches.
sns.boxplot(x='airline', y='price', data=flight)
#Creates a boxplot with airline on the x-axis and price on the y-axis. The flight DataFrame is used as the source data.

plt.title('Price Variance Across Airlines')
#Adds the title to the plot.

plt.xlabel('Airlines')
#Labels the x-axis as "Airlines."

plt.ylabel('Price')
#Labels the y-axis as "Price."

plt.show()
#Displays the boxplot.

#Price Variance After Log Transformation
Flight['price'] = np.log1p(flight['price'])
#Applies a logarithmic transformation to the price column using np.log1p(), which calculates the log of (1 + price) to avoid taking the log of zero.

sns.boxplot(x='airline', y='price', data=flight)
#Same as before but now the price values are transformed.

plt.title('Price Variance of Airlines (After Log Transformation)')
#Updates the plot title.

plt.ylabel('Log of Price')
#Updates the y-axis label to "Log of Price."

plt.show()
#Displays the updated boxplot.

#Price Based on Departure and Arrival Times

plt.figure(figsize=(12, 8))
#Sets the figure size to 12x8 inches.

plt.subplot(2, 1, 1)
#Creates the first subplot (2 rows, 1 column, this is the 1st plot).

plt.plot(flight['departure_time'], flight['price'], marker='o', color='blue')
#Plots departure_time on the x-axis and price (after log transformation) on the y-axis. Each point is marked with circles (marker='o') and the line is blue.

plt.title('Ticket Price by Departure Time')
#Adds a title for the first plot.

plt.xlabel('Departure Time')
#Labels the x-axis of the first plot.

plt.ylabel('Log of Price')
#Labels the y-axis of the first plot.

plt.subplot(2, 1, 2)
#Creates the second subplot.

plt.plot(flight['arrival_time'], flight['price'], marker='o', color='green')
#Plots arrival_time vs. price in the second plot with green markers and lines.

plt.title('Ticket Price by Arrival Time')
#Adds a title for the second plot.

plt.xlabel('Arrival Time')
#Labels the x-axis of the second plot.

plt.tight_layout()
#Ensures subplots do not overlap.

plt.show()
#Displays both subplots.

#Price Changes with Source and Destination
aggregated = flight.groupby(['source_city', 'destination_city'])['price'].mean().reset_index()
#Groups the data by source_city and destination_city, calculates the mean price for each pair, and resets the index to flatten the result into a DataFrame.

pivot = aggregated.pivot(index='source_city', columns='destination_city', values='price')
#Converts the grouped DataFrame into a pivot table where rows are source_city, columns are destination_city, and cell values are the mean price.

sns.heatmap(pivot, annot=True, cmap='coolwarm', fmt='.0f')
#Creates a heatmap from the pivot table, annotating each cell with its value. The cmap='coolwarm' applies a color gradient, and fmt='.0f' formats the values as integers.

plt.title('Price Changes by Source and Destination Cities')
#Adds a title to the heatmap.

plt.show()
#Displays the heatmap.


#Duration of Travel vs. Cities

mean_duration = flight.groupby(['source_city', 'destination_city'])['duration'].mean().reset_index()
#Groups the data by source_city and destination_city, then calculates the mean flight duration for each pair and resets the index.

duration_pivot = mean_duration.pivot(index='source_city', columns='destination_city', values='duration')
#Converts the grouped data into a pivot table where rows represent source_city, columns represent destination_city, and values represent the mean duration.

sns.heatmap(duration_pivot, annot=True, cmap='Greens', fmt='.1f')
#Creates a heatmap to visualize the mean flight duration between source and destination cities. Each cell displays the average duration, with the color gradient provided by the cmap='Greens' parameter, and values formatted to one decimal place with fmt='.1f'.

plt.title('Duration of Travel by Source and Destination Cities')
#Adds a title to the heatmap.

plt.show()
#Displays the heatmap.
grouped_data = flight.groupby(['destination_city', 'class'])['price'].mean().reset_index()
"""flight.groupby(['destination_city', 'class']): Groups the DataFrame by destination_city and class, which allows us to aggregate data based on these two categories.
['price'].mean(): For each group, this calculates the mean price, providing the average price of flights for each combination of destination city and class.
reset_index(): Resets the index of the resulting DataFrame so that destination_city and class become regular columns again, making the DataFrame easier to work with for plotting."""
plt.figure(figsize=(12, 6))
#Initializes a new figure with a specified size (12 inches wide and 6 inches tall) to ensure readability.
sns.barplot(x='destination_city', y='price', hue='class', data=grouped_data, palette='Set2')
"""sns.barplot(...): Creates a bar plot:
x='destination_city': Sets the x-axis to represent different destination cities.
y='price': Sets the y-axis to show the average price.
hue='class': Differentiates bars by flight class (e.g., economy, business) using different colors.
data=grouped_data: Specifies the DataFrame to use for plotting."""

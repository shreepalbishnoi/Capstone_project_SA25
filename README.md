# Capstone_project_SA25
Final project on Real-Time Dynamic Pricing for Urban Parking

This project implements a real-time dynamic pricing engine for a network of 14 urban parking lots. The system adjusts parking prices based on live data streams, including vehicle traffic, occupancy, queue lengths, and special events, to optimize space utilization and revenue. The entire pipeline, from data ingestion to live visualization, is built to handle streaming data and provide instantaneous pricing updates.

#Tech Stack

Core Language: Python 3.10

Real-Time Data Processing: Pathway - A reactive data processing framework for building real-time machine learning and data pipelines.

Data Manipulation: Pandas & NumPy - For efficient in-memory data transformation and numerical computation within the pricing UDF.

Real-Time Visualization: Bokeh - For creating interactive, real-time plots that update dynamically in a web browser or notebook.

Development Environment: Google Colab         

Detailed Workflow Explanation
Data Ingestion (Simulated Stream):
The pipeline starts by reading the dataset.csv file using Pathway's pw.io.csv.read connector, configured in 'streaming' mode.

This effectively simulates a real-time data feed, where each row of the CSV is treated as a new event arriving in the stream.

A ParkingInputStream schema is defined to enforce data types and structure on the incoming data, ensuring pipeline robustness.

Real-Time Processing with Pathway & Pandas UDF:

The core of the pricing logic is encapsulated in a User-Defined Function (UDF) named calculate_dynamic_price_udf, which is designed to work with pandas DataFrames.

Pathway automatically micro-batches the incoming data stream into small DataFrames. The .map() method applies our UDF to each of these batches.

Inside the UDF:

Feature Engineering: New features like occupancy_rate are calculated (Occupancy / Capacity). Categorical data like TrafficConditionNearby is mapped to numerical weights.

Demand Calculation: A demand score is computed using a weighted linear combination of several factors:

occupancy_rate: Higher occupancy increases demand.

QueueLength: Longer queues signify higher demand.

traffic_weight: Heavy traffic slightly reduces demand (as it might deter parkers).

IsSpecialDay: Special days (holidays, events) increase demand.

vehicle_weight: Heavier/larger vehicles have a slightly higher impact.

Price Calculation: The calculated demand score is normalized using np.tanh to keep it within a predictable range (-1 to 1). This normalized demand is then used to adjust a BASE_PRICE. The final price is clipped to stay within a reasonable minimum and maximum threshold (e.g., 0.5x to 2.0x the base price).

The UDF returns a new DataFrame containing the original data plus the calculated dynamic_price and a timestamp. Pathway's .unfold() method unnests this DataFrame back into a stream of individual records.

Live Visualization with Bokeh:

The final, enriched stream of data is written to a Pathway output table (output_table).

A separate process (a loop in the notebook) periodically queries this table to fetch the latest pricing data.

A Bokeh plot is initialized with an empty ColumnDataSource.

In each iteration of the update loop:

The new data from the Pathway table is fetched.

The source.stream() method of the Bokeh ColumnDataSource is called. This efficiently appends the new data points to the plot's data source.

push_notebook() updates the plot in the Colab output cell without needing to redraw the entire figure, creating a smooth, real-time visualization of the changing prices for all 14 parking spaces.

How to Run the Project
Prerequisites:

A Google Account to use Google Colab.

Setup:

Open the .ipynb file in Google Colab.

Upload the dataset.csv file to your Colab environment. You can do this by clicking the "Files" icon on the left sidebar and selecting "Upload".

Installation:

Run the first code cell to install the necessary Python libraries:

!pip install -q pathway bokeh

Execution:

Run the notebook cells sequentially from top to bottom.

The script will:

Define the pricing model parameters and the UDF.

Initialize and run the Pathway pipeline in a background thread.

Set up and display the initial Bokeh plot.

Start the real-time update loop, which will populate the plot with dynamic pricing data for the next 60 seconds.

Dataset Schema
The dataset.csv contains records with the columns, representing the state of a parking lot at a specific time.

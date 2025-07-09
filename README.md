# Real-Time Dynamic Pricing for Urban Parking

This project implements a **real-time dynamic pricing engine** for a network of 14 urban parking lots. It adjusts parking prices based on **live data streams** such as vehicle traffic, occupancy, queue lengths, and special events — helping optimize **space utilization and revenue**.

📍 Developed and tested in **Google Colab**, the pipeline handles streaming data from CSV files, processes it in real-time using **Pathway**, and renders live updates using **Bokeh**.

--

## Tech Stack

| Category                | Tools & Libraries               |
|-------------------------|---------------------------------|
|  Language             | Python 3.10                      |
|  Real-Time Processing | [Pathway](https://pathway.com) |
|  Data Manipulation    | Pandas, NumPy                   |
|  Visualization        | Bokeh                          |
|  Environment          | Google Colab                   |

--

##  Project Workflow

###  1. Data Ingestion (Simulated Stream)

- Read the `dataset.csv` using `pw.io.csv.read()` in **streaming mode**.
- Each row simulates a real-time event.
- Data is validated via a `ParkingInputStream` schema.

###  2. Real-Time Processing with Pathway + Pandas UDF

- A **User-Defined Function** `calculate_dynamic_price_udf` runs on micro-batches of DataFrames.
- Features like `occupancy_rate` and `traffic_weight` are engineered.
- Demand score is computed using:
  - Occupancy
  - Queue Length
  - Traffic
  - Special Day
  - Vehicle Type
- Final dynamic price = `BASE_PRICE × f(demand)`  
  (Normalized via `np.tanh` and clipped to safe range)

###  3. Live Visualization with Bokeh

- A `Bokeh` plot dynamically updates via:
  - `ColumnDataSource.stream()`
  - `push_notebook()` for smooth real-time rendering.
- Prices for 14 parking lots are shown updating for 60 seconds.

---

##  How to Run the Project

### Prerequisites

- A **Google Account** to access Google Colab

### Setup

1. **Open the notebook** in Google Colab  
2. **Upload `dataset.csv`** from your computer using the "Files" panel  
3. **Install dependencies** using the following cell:

```python
!pip install pathway bokeh --quiet

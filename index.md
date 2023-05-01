---
layout: default
---
# Introduction

On this webpage, you can find an elaborate explanation of my [ABE 516X](https://github.com/isu-abe/516x) (Data Science and Analytics for Agricultural and Biosystem Engineers) final project. During the course, I learned about research techniques linked with data science ideas and their practical use. The course involved conducting analyses and research on themes connected to Agriculture and Biosystem Engineering and Technology, methods for creating and preserving data analysis pipelines that can be replicated, the best approaches for displaying data visually and presenting data-driven results to diverse audiences, and principles of data ethics and integrity.

During the semester, we learned about various topics and tools that researchers can use in ABE (i.e., Text Scrapping, Supervised and Unsupervised Machine Learning models, Cloud Shel and Git). Thus, in this project, I will explain how I used five of these research methods and concepts to improve my research on combining and analyzing streaming environment data for anomaly detection in animal production facilities.

Livestock and poultry facilities commonly have alarm systems to notify caretakers in case of emergencies, traditionally defined as exceeding user-defined temperature thresholds. However, these alarm systems have numerous limitations, including limited sensing options, maintenance and debugging issues, expensive and challenging wiring, and a high failure rate. The next generation of alarms incorporate various critical sensors (such as water, pressure, relative humidity, etc.), cellular gateway connectivity, cloud-based analytics, and mobile applications, creating a vast amount of data that can be utilized for predictive and prescriptive anomaly detection. While wireless sensing systems generate real-time updates to a dashboard, the challenge remains in analyzing and interpreting streaming environment data. Therefore, this project aims to discuss the initial research approaches that will lead to developing techniques for early notification of potential challenges using anomaly detection in swine facilities based on environmental and water usage data. This approach would reduce economic losses, enable precision livestock farming, and benefit producers invested in next-generation alarm systems - that, by utilizing wireless sensors platform with cloud integration for real-time anomaly detection, can receive notifications for potential problems that may impact productivity, such as inconsistent environment and improper management decisions.

## The two questions I will be investigating in this project are:

* Is there a significant difference in outlier/anomaly detection given different smoothing techniques and running it in the actual data?
* What is/are the best smoothing technique to apply to my data to reduce noise and identify trends in time-series data?

## What five research methods and concepts learned in this class will be applied in this project?

1.  The use of an API to collect data from the cloud
2.  Data wrangling with the implementation of resampling statistics (Fill/Predicting missing data points)
3.  Employment of Python library that aids in data visualization and exploration
4.  Use of smoothing techniques and an unsupervised machine learning model for outlier/anomaly detection
5.  Ability to make my analysis reproducible

* * *

# First Concept/Method: API

My data can be obtained manually from the sponsoring company's website or through an API. By default, all the farm's data are stored for a maximum of fifteen days before being deleted. Therefore, before implementing the API with the knowledge gained in this course, I had to keep good track of when I had to manually download those files. For almost a year and a half, I have failed twice and missed the download window for a day or two, which is not a big deal when looking at the vast amount of data I have, but this could be better by having no missing days! 

With the implementation of a script that can retrieve data from the API, I can forget about downloading the data myself and setting calendar reminders and alarms. In class, I wrote a functional Jupyther Notebook that can call, authenticate and download the CSV files of multiple gateways (Famrs) I have access. Even though I had a functional script on my hands, making those API calls was not completely autonomous since I had to press run on the notebook.

From my application's API, I can perform two different requests. One that returns me the status (minimum and maximum readings) of every sensor connected to that gateway over the last 15-minute period or the other option that returns the status of every sensor over the last 24-hour window.

My goal was to schedule when the script would run. I researched the best methods to schedule a Python script and found multiple ways to do it using Microsoft Azure, Windows Task Scheduler, JupyterLab, etc. My initial idea was to keep two laptops executing the script every 24 hours at midnight using Windows Task Scheduler. It worked fine for the first few days, but I started noticing that it was not making the calls or the files were not being saved properly. My second option was to have JupytherLab running on those computers, and I had no issues with it both during setup and execution over the last weeks.

### How to use Jupyther Scheduler:
1. Install Jupyther Scheduler from the PyPI registry via *pip*
2. Open JupyterLab (Can be easily found on Anaconda Navigator) 
3. Select open the Jupyter Notebook that contains the API routine
4. Click on the blue calendar icon located at the top left corner of your notebook
5. Create a Job name
6. Choose output formats (leave both default options active)
7. Select "Run on a schedule"
8. Choose the Interval, Time, and Time zone you wish to use
9. Press "Create"
10. Now you can see the status and general information of your job

Remember that Jupyter Scheduler runs Jupyter notebooks in the background, either once or on a schedule. I recommend giving preference to JupyterLab due to the ease of making changes to your job configurations and because it is optimized to run Jupyter Notebooks. For more information on how to schedule your Jupyter Notebooks refer to [Jupyter Scheduler Library](https://jupyter-scheduler.readthedocs.io/en/latest/)

### Functions description:

* **gitData(gateway)**: function uses as argument object input the name of the farm from which we desire to pull data from

* **savingData(gateway_name)**: function uses the name of the farm as an object argument and saves the data in a csv file. Notice that for every request data, we save the output in the cvs file as a new line input. If the file is not existent, it will be created in that directory

* **job()**: function allows us to call both functions described above for every farm that we desire
     * time.sleep(15) was used as a 15 seconds request buffer since the API does not allow users to make calls in less than 10 seconds in between requests

**Disclaimer:** The notebook was not embedded in this website because it contains confidential information. Access will be provided for the instructor in a separate platform.

# Second Concept/Method: Data Wrangling

This portion of my analysis considers the formatting of the data (CSV) files manually obtained by downloading it from the cloud - because that's the formatting, I have more data available now. I expect to be able to work with the API files by the end of June 2023 since, at that point, I will have a significant amount of points to conduct an analysis.

Differently from the API CSV outputs that only return the minimums and maximums of each sensor connected to the gateway of 15 minutes or 24-hour periods, the manually downloaded data contains all sensors (Temperature, Humidity, and Water Levels) connected to the gateway to record the parameters simultaneously, but at irregular time intervals. The time intervals range from around 1 minute to more than 10 minutes, and parameters recorded by each sensor may or may not change between consecutive updates (i.e., sparse logging). 

I defined a function that performs a series of operations with my input raw data file and outputs a data frame that is easier to work with. The following are the operations within the function:

1. Identify which farm we are working with
2. Resamples each farm sensor given a resampling window
3. Linear interpolates any missing values (Note: Future work needs to be done to account for if only one or two hours of observations were missing, we use linear interpolation to impute the missing observations because we expected no dramatic change in the parameters within a brief period. However, if more than two hours of data are missing during the daytime, we discard the entire day's data because interpolation may be unreliable over long time windows)
4. Calculates the water consumption rates given the maximum and minimums during that time-series interval
5. Arranges all the values in an appropriate data frame with correct indexes and formating
6. Returns a data frame

### Function description:

* **create_df(Farm, Month, RSP)**: This function creates a data frame based on the raw data files, resample it at the desired resampling period, uses linear interpolation to fill in missing values, calculates the water consumption at that period, and outputs a data frame arranged in optimal formatting to be used in future steps of my research.

    * Farm = string specifying the Farm of interest (i.e., "Farm1", "Farm2", etc)
    * Month = string specifying the Month and year of interest (i.e., "November 2022")
    * RSP = Resampling period of interest, which is the input for pd.resample function where RSP is the offset string or object representing target conversion (i.e., "1min", "1H", "1W", "1M", etc)

```python
def create_df(Farm, Month, RSP):
    
    data = pd.read_csv(Farm + " " + Month + ".csv")

    if Farm == "Farm1" or Farm == "Farm2" or Farm == "Farm3" or Farm == "Farm4" or Farm == "Farm5": 
      DOI1 = data[(data.SensorName == farms[Farm]["Name_H1"]) & (data.SensorType == farms[Farm]["Type_H1"])]
      DOI2 = data[(data.SensorName == farms[Farm]["Name_T1"]) & (data.SensorType == farms[Farm]["Type_T1"])]
      DOI3 = data[(data.SensorName == farms[Farm]["Name_W1"]) & (data.SensorType == farms[Farm]["Type_W1"])]

      DOI4 = data[(data.SensorName == farms[Farm]["Name_H2"]) & (data.SensorType == farms[Farm]["Type_H2"])]
      DOI5 = data[(data.SensorName == farms[Farm]["Name_T2"]) & (data.SensorType == farms[Farm]["Type_T2"])]
      DOI6 = data[(data.SensorName == farms[Farm]["Name_W2"]) & (data.SensorType == farms[Farm]["Type_W2"])]

      DOI7 = data[(data.SensorName ==  farms[Farm]["Name_OFCT"]) & (data.SensorType == farms[Farm]["Type_OFCT"])]
      DOI8 = data[(data.SensorName ==  farms[Farm]["Name_ODT"]) & (data.SensorType == farms[Farm]["Type_ODT"])]


      DOI1['Timestamp'] = pd.to_datetime(DOI1['Timestamp'])
      DOI1 = DOI1.resample(RSP, on='Timestamp').agg({'Value':'mean'})
      DOI1.loc[DOI1["Value"] == 0.0, "Value"] = np.NAN

      DOI2['Timestamp'] = pd.to_datetime(DOI2['Timestamp'])
      DOI2 = DOI2.resample(RSP, on='Timestamp').agg({'Value':'mean'})
      DOI2.loc[DOI2["Value"] == 0.0, "Value"] = np.NAN

      DOI3['Timestamp'] = pd.to_datetime(DOI3['Timestamp'])
      DOI3_min = DOI3.resample(RSP, on='Timestamp').agg({'Value':'min'})
      DOI3_min.loc[DOI3_min["Value"] == 0.0, "Value"] = np.NAN #np.NAN #Removes all 0.0 values and make them NaN
      DOI3_max = DOI3.resample(RSP, on='Timestamp').agg({'Value':'max'})
      DOI3_max.loc[DOI3_max["Value"] == 0.0, "Value"] = np.NAN

      DOI4['Timestamp'] = pd.to_datetime(DOI4['Timestamp'])
      DOI4 = DOI4.resample(RSP, on='Timestamp').agg({'Value':'mean'})
      DOI4.loc[DOI4["Value"] == 0.0, "Value"] = np.NAN

      DOI5['Timestamp'] = pd.to_datetime(DOI5['Timestamp'])
      DOI5 = DOI5.resample(RSP, on='Timestamp').agg({'Value':'mean'})
      DOI5.loc[DOI5["Value"] == 0.0, "Value"] = np.NAN

      DOI6['Timestamp'] = pd.to_datetime(DOI6['Timestamp'])
      DOI6_min = DOI6.resample(RSP, on='Timestamp').agg({'Value':'min'})
      DOI6_min.loc[DOI6_min["Value"] == 0.0, "Value"] = np.NAN
      DOI6_max = DOI6.resample(RSP, on='Timestamp').agg({'Value':'max'})
      DOI6_max.loc[DOI6_max["Value"] == 0.0, "Value"] = np.NAN

      DOI7['Timestamp'] = pd.to_datetime(DOI7['Timestamp'])
      DOI7 = DOI7.resample(RSP, on='Timestamp').agg({'Value':'mean'})
      DOI7.loc[DOI7["Value"] == 0.0, "Value"] = np.NAN

      DOI8['Timestamp'] = pd.to_datetime(DOI8['Timestamp'])
      DOI8 = DOI8.resample(RSP, on='Timestamp').agg({'Value':'mean'})
      DOI8.loc[DOI8["Value"] == 0.0, "Value"] = np.NAN


      df1 = pd.merge(DOI1, DOI2, on="Timestamp", suffixes=(" DOI1", " DOI2"))
      df2 = pd.merge(DOI3_min, DOI3_max, on="Timestamp", suffixes=(" Min DOI3", " Max DOI3"))
      df3 = pd.merge(DOI4, DOI5, on="Timestamp", suffixes=(" DOI4", " DOI5"))
      df4 = pd.merge(DOI6_min, DOI6_max, on="Timestamp", suffixes=(" Min DOI6", " Max DOI6"))
      df5 = pd.merge(DOI7, DOI8, on="Timestamp", suffixes=(" DOI7", " DOI8"))

      df12 = pd.merge(df1, df2, on="Timestamp")
      df34 = pd.merge(df3, df4, on="Timestamp")

      df = pd.merge(df12, df34, on="Timestamp")
      df = pd.merge(df, df5, on="Timestamp")

      #Linear interpolate missing values that it is not related to water measuremnts
      df['Value DOI1'].interpolate(method='linear', inplace=True)
      df['Value DOI2'].interpolate(method='linear', inplace=True)
      df['Value DOI4'].interpolate(method='linear', inplace=True)
      df['Value DOI5'].interpolate(method='linear', inplace=True)
      df['Value DOI7'].interpolate(method='linear', inplace=True)
      df['Value DOI8'].interpolate(method='linear', inplace=True)

      #Fill min NAN values with previous max
      df['Value Min DOI3'].fillna(df['Value Max DOI3'].shift(1), inplace=True)
      df['Value Min DOI6'].fillna(df['Value Max DOI6'].shift(1), inplace=True)

      #Find water consumption by RSP
      df[farms[Farm]["Name_W1"] + ' Consumption'] = df['Value Max DOI3'] - df['Value Min DOI3']
      df[farms[Farm]["Name_W2"] + ' Consumption'] = df['Value Max DOI6'] - df['Value Min DOI6']

      #Linear interpolate missing water consumption values
      df[farms[Farm]["Name_W1"] + ' Consumption'].interpolate(method='linear', inplace=True)
      df[farms[Farm]["Name_W2"] + ' Consumption'].interpolate(method='linear', inplace=True)

      #Last Step is to Fill missing max and min values by using the interpolated water consumption
      while df['Value Max DOI3'].isnull().values.any():
        df['Value Max DOI3'].fillna((df['Value Max DOI3'].shift(1) + df[farms[Farm]["Name_W1"] +  ' Consumption']), inplace=True)
      while df['Value Min DOI3'].isnull().values.any():
        df['Value Min DOI3'].fillna((df['Value Max DOI3'] - df[farms[Farm]["Name_W1"] +  ' Consumption']), inplace=True)

      while df['Value Max DOI6'].isnull().values.any():
        df['Value Max DOI6'].fillna((df['Value Max DOI6'].shift(1) + df[farms[Farm]["Name_W2"] +  ' Consumption']), inplace=True)
      while df['Value Min DOI6'].isnull().values.any():
        df['Value Min DOI6'].fillna((df['Value Max DOI6'] - df[farms[Farm]["Name_W2"] +  ' Consumption']), inplace=True)

      df = df.rename(columns={"Value DOI1": farms[Farm]["Name_H1"], "Value DOI2": farms[Farm]["Name_T1"], "Value Min DOI3": "Min " + farms[Farm]["Name_W1"], "Value Max DOI3": "Max " + farms[Farm]["Name_W1"],\
                              "Value DOI4": farms[Farm]["Name_H2"], "Value DOI5": farms[Farm]["Name_T2"], "Value Min DOI6": "Min " + farms[Farm]["Name_W2"], "Value Max DOI6": "Max " + farms[Farm]["Name_W2"],\
                              "Value DOI7": farms[Farm]["Name_OFCT"], "Value DOI8": farms[Farm]["Name_ODT"]})

      df = df[[farms[Farm]["Name_H1"], farms[Farm]["Name_T1"], "Min " + farms[Farm]["Name_W1"], "Max " + farms[Farm]["Name_W1"], farms[Farm]["Name_W1"] + " Consumption",\
               farms[Farm]["Name_H2"], farms[Farm]["Name_T2"], "Min " + farms[Farm]["Name_W2"], "Max " + farms[Farm]["Name_W2"], farms[Farm]["Name_W2"] + " Consumption",\
               farms[Farm]["Name_OFCT"], farms[Farm]["Name_ODT"]]]
    
    return df
```
# Thrid Concept/Method: Data Visualization

The nature of my data requires a good way of graphically visualizing it in order to be able to manually identify patterns and trends that I should keep in mind before running any smoothing techniques or any machine and deep learning models. Before taking this class, I was doing all the data wrangling via Jupyter Notebook, but all the graphs were done using Excel - since it was the graphing tool I was more comfortable using. But after this semester, I became way more comfortable making high-end plots using Python libraries such as Seaborn and Plotly. I chose to use Plotly as my main graphical library in this project because it is an interactive visualization tool that allows me to zoom in and out, pan, take snapshots, and select specific plotting features, which helps me explore and easily discover patterns in my dataset. In this portion, I defined two functions for graphical data visualization. One allows me to plot a single graph that traces all the features from my data frame, while the other plots the same data but separate it by sensor type and placement (room placement given farm orientation) at the farm. 

### Function description:

* **plot_df(df, Title)**: This function outputs a single interactive Plotly graph with all the features within the data frame.

    * df = input desired data frame to be plotted (i.e., "Farm1_20min_SepDec2022")
    * Title = input string that will be displayed as graph title (i.e., "Farm1 Sep-Oct 2022 @ 20min")
    * Plot output example:
    
        **When all the sensors/features are selected:**
    ![picture](https://github.com/FelipePicchi/Felipe_abe516x/blob/master/_includes/plot_df%20with%20all%20features.png)
        **When only one sensors/feature is selected:**
    ![picture](https://github.com/FelipePicchi/Felipe_abe516x/blob/master/_includes/plot_df%20with%20only%20one%20feature.png)
   
* **plot_dfHTW(df, Farm)**: This function outputs an interactive Plotly graph that contains three subplots tracing separate graphs for Humidity, Temperature, and Water. Furthermore, this plot has a dropdown menu where you can select the graph to show the farm orientation 1 and 2 separately or together.

    * df = input desired data frame to be plotted (i.e., "Farm1_20min_SepDec2022")
    * Farm = string specifying the Farm of interest (i.e., "Farm1", "Farm2", etc)
    * Plot output example:
    
    ![picture](https://github.com/FelipePicchi/Felipe_abe516x/blob/master/_includes/plot_dfHTW.png)
    
```python
import plotly.graph_objects as go
from plotly.subplots import make_subplots

def plot_df(df, Title):
    fig = go.Figure()
    
    # Add a trace for each column in the DataFrame
    for col in df.columns:
        fig.add_trace(go.Scatter(x=df.index, y=df[col], mode='lines', name=col))
        
    # Set the title and axis labels
    fig.update_layout(title=Title, xaxis_title='Timestamp', yaxis_title='Value')
    
    # Show the plot
    fig.show()

def plot_dfHTW(df, Farm):
    
    fig = make_subplots(rows=3, cols=1, subplot_titles=("Humidity", "Temperature", "Water"))

    # Create traces for room 1 values
    H1_trace = go.Scatter(x=df.index, y=df[farms[Farm]["Name_H1"]], name=farms[Farm]["Name_H1"])
    T1_trace = go.Scatter(x=df.index, y=df[farms[Farm]["Name_T1"]], name=farms[Farm]["Name_T1"])
    W1_trace = go.Scatter(x=df.index, y=df[(farms[Farm]["Name_W1"]  + " Consumption")], name=(farms[Farm]["Name_W1"]  + " Consumption"))
    W1_min_trace = go.Scatter(x=df.index, y=df[("Min " + farms[Farm]["Name_W1"])], name=("Min " + farms[Farm]["Name_W1"]))
    W1_max_trace = go.Scatter(x=df.index, y=df[("Max " + farms[Farm]["Name_W1"])], name=("Max " + farms[Farm]["Name_W1"]))

    # Create traces for room 2 values
    H2_trace = go.Scatter(x=df.index, y=df[farms[Farm]["Name_H2"]], name="West Room Humidity")
    T2_trace = go.Scatter(x=df.index, y=df[farms[Farm]["Name_T2"]], name="West Room Temp")
    W2_trace = go.Scatter(x=df.index, y=df[(farms[Farm]["Name_W2"]  + " Consumption")], name=(farms[Farm]["Name_W2"]  + " Consumption"))
    W2_min_trace = go.Scatter(x=df.index, y=df[("Min " + farms[Farm]["Name_W2"])], name=("Min " + farms[Farm]["Name_W2"]))
    W2_max_trace = go.Scatter(x=df.index, y=df[("Max " + farms[Farm]["Name_W2"])], name=("Max " + farms[Farm]["Name_W2"]))

    # Add traces to subplots based on their column type
    fig.add_trace(H1_trace, row=1, col=1)
    fig.add_trace(H2_trace, row=1, col=1)
    fig.add_trace(T1_trace, row=2, col=1)
    fig.add_trace(T2_trace, row=2, col=1)
    fig.add_trace(W1_trace, row=3, col=1)
    fig.add_trace(W2_trace, row=3, col=1)
    fig.add_trace(W1_min_trace, row=3, col=1)
    fig.add_trace(W2_min_trace, row=3, col=1)
    fig.add_trace(W1_max_trace, row=3, col=1)
    fig.add_trace(W2_max_trace, row=3, col=1)

    # Update the layout to allow for toggling between room 1, room 2, or both values in each graph
    fig.update_layout(updatemenus=[dict(active=0, buttons=list([
        dict(label=farms[Farm]["Orientation1"], method="update", args=[{"visible": [True, False, True, False, True, False, True, False]}, {"title": f'{Farm} {farms[Farm]["Orientation1"] } Values'}]),
        dict(label=farms[Farm]["Orientation2"], method="update", args=[{"visible": [False, True, False, True, False, True, False, True]}, {"title": f'{Farm} {farms[Farm]["Orientation1"] } Values'}]),
        dict(label="Both Rooms", method="update", args=[{"visible": [True, True, True, True, True, True, True, True]}, {"title": f'{Farm} {farms[Farm]["Orientation1"]} and {farms[Farm]["Orientation2"]} Values'}])
    ]))])

    fig.show()
```

# Fourth Concept/Method: Data Smoothing and ML Model

## Smoothing Techniques

There are several data smoothing techniques that I could consider applying to our time-series data before running any machine or deep learning model for outlier detection. The choice of smoothing technique will depend on the specific characteristics of my time-series data and the analysis goals. Among various techniques, I chose to test **Moving Average**, **Savitzky-Golay**, and lastly, **Wavelet Transform**. Before researching more about smoothing techniques that could be applied to this project, I was only familiar with Moving Average and Savitzky-Golay. But I came across the method Wavelet Transform - which I am still exploring and experimenting with all the available parameters and modes.

### Smoothing with Moving Average
 
Smoothing using moving average is the first of three smoothing techniques I explore in this project. This is a common technique for reducing noise and identifying trends in time-series data. It involves taking a sliding window of a specified length, computing the average value within that window, and repeating this process for each subsequent data point in the time series, producing a new smoothed time series that highlights long-term trends while minimizing the impact of short-term noise. However, it introduces a lag -of the same size as the sliding window- in the resulting time series since each point of the smoothed value is based on the prior data.

#### Function description:

* **my_movingAvg(Title, Data, variable, window_size, output_plot=False)**:

    * Title = string that will be displayed as graph title (i.e., "Farm1 Sep-Oct 2022 @ 20min")
    * Data = input desired data frame to have a feature smoothed (i.e., "Farm1_20min_SepDec2022")
    * variable = string representing the farm feature of interest. (i.e., "East Room Humidity", "Nort Room Temp", etc)
    * window_size = int, timedelta, str, offset, or BaseIndexer subclass. Size of the moving window: If an integer, the fixed number of observations used for each window. If a timedelta, str, or offset, the time period of each window. Each window will be a variable sized based on the observations included in the time-period. This is only valid for datetimelike indexes
    * output_plot = bool, default False. Whether to include the Plotly graph in the result containig tracing for both actual and smoothed data

```python
def my_movingAvg(Title, Data, variable, window_size, output_plot=False):
    df = Data[[variable]].copy()

    # apply the moving average to smooth the data
    smoothed_data = df[variable].rolling(window_size).mean()

    # creates a new column in the dataframe to store the smoothed data
    df[f'{variable} smoothed'] = smoothed_data

    # creates a Plotly figure that shows both the actual and smoothed data
    fig = go.Figure()

    fig.add_trace(go.Scatter(x=df.index, y=df[variable], mode='lines', name=variable))
    fig.add_trace(go.Scatter(x=df.index, y=df[f'{variable} smoothed'], mode='lines', name=f'{variable} smoothed', opacity=0.5))

    fig.update_layout(title= f'{Title} Actual vs Smoothed Data for {variable} with Moving Average', xaxis_title='Date', yaxis_title='Value')

    # optionally output the Plotly figure
    if output_plot:
        fig.show()

    smoothed_df = df.copy()
    smoothed_df = smoothed_df.fillna(method='bfill')
    
    return smoothed_df
```

### Smoothing with Savitzky-Golay

Smoothing using [Savitzky-Golay](https://docs.scipy.org/doc/scipy/reference/generated/scipy.signal.savgol_filter.html) is the second of three smoothing techniques I explore in this project. This is also used to reduce noise and to extract trends from time-series data. It works by fitting a polynomial function to a sliding window of data points and using the coefficients of that polynomial to estimate the smoothed value at the center of the window. This process is repeated for each data point in the series, resulting in a smoothed time series. Unlike moving average smoothing, Savitzky-Golay smoothing allows for the preservation of high-frequency features in the data while still reducing noise.

#### Function description:

* **my_savgolFilter(Title, Data, variable, window_size, poly_order, output_plot=False)**:

    * Title = string that will be displayed as graph title (i.e., "Farm1 Sep-Oct 2022 @ 20min")
    * Data = input desired data frame to have a feature smoothed (i.e., "Farm1_20min_SepDec2022")
    * variable = string representing the farm feature of interest. (i.e., "East Room Humidity", "Nort Room Temp", etc)
    * window_size = integer with the length of the filter window (i.e., the number of coefficients). If mode is ‘interp’, window_length must be less than or equal to the size of the variable input
    * poly_order = integer representing the order of the polynomial used to fit the samples. poly_order must be less than window_length
    * output_plot = bool, default False. Whether to include the Plotly graph in the result containig tracing for both actual and smoothed data

```python
from scipy.signal import savgol_filter

def my_savgolFilter(Title, Data, variable, window_size, poly_order, output_plot=False):

    df = Data[[variable]].copy()

    # apply the Savitzky-Golay filter
    smoothed_data = savgol_filter(df[variable], window_size, poly_order, mode='interp')

    # creates a new column in the dataframe to store the smoothed data
    df[f'{variable} smoothed'] = smoothed_data

    # creates a Plotly figure that shows both the actual and smoothed data
    fig = go.Figure()

    fig.add_trace(go.Scatter(x=df.index, y=df[variable], mode='lines', name=variable))
    fig.add_trace(go.Scatter(x=df.index, y=df[f'{variable} smoothed'], mode='lines', name=f'{variable} smoothed', opacity=0.5))

    fig.update_layout(title= f'{Title} Actual vs Smoothed Data for {variable} with Savitzky-Golay', xaxis_title='Date', yaxis_title='Value')

    # optionally output the Plotly figure
    if output_plot:
        fig.show()

    smoothed_df = df.copy()
    
    return smoothed_df
```

### Smoothing with Wavelet Transform

Smoothing using [Wavelet Transform](https://pywavelets.readthedocs.io/en/0.2.2/index.html#) is the last smoothing technique I explore in this project. As the previous, this technique is used for reducing noise and identifying trends in time-series data. It works by decomposing the signal into a series of wavelet coefficients at different scales and frequencies specified by the user. By discarding the high-frequency coefficients representing noise or other data fluctuations, the smoothed signal preserves the low-frequency coefficients corresponding to more prevalent trends in the data. Different from the other methods, this is a bit more complex, given the various wavelet [families](https://wavelets.pybytes.com/) I can experiment with.

#### Function description:

* **my_waveletFilter(Title, Data, variable, wavelet, level, output_plot=False)**:
    * Title = string that will be displayed as graph title (i.e., "Farm1 Sep-Oct 2022 @ 20min")
    * Data = input desired data frame to have a feature smoothed (i.e., "Farm1_20min_SepDec2022")
    * variable = string representing the farm feature of interest. (i.e., "East Room Humidity", "Nort Room Temp", etc)
    * wavelet = string wavelet to use in the transform (i.e., "db1", "haar", "bior", etc)
    * level = integer representing the number of decomposition steps to perform
    * output_plot = bool, default False. Whether to include the Plotly graph in the result containig tracing for both actual and smoothed data
    * *Note*: Aside from the input parameters in this function, it is advised to experiment with other wavedec and threshold parameters (i.e., model, value, etc)

```python
import pywt

def my_waveletFilter(Title, Data, variable, wavelet, level, output_plot=False):

    df = Data[[variable]].copy()

    # apply the wavelet transform
    coeffs = pywt.wavedec(df[variable], wavelet, level=level, mode='symmetric')
    coeffs[1:] = [pywt.threshold(i, value=5, mode='soft') for i in coeffs[1:]]
    smoothed_data = pywt.waverec(coeffs, wavelet)

    # creates a new column in the dataframe to store the smoothed data
    df[f'{variable} smoothed'] = smoothed_data
    

    # creates a Plotly figure that shows both the actual and smoothed data
    fig = go.Figure()

    fig.add_trace(go.Scatter(x=df.index, y=df[variable], mode='lines', name=variable))
    fig.add_trace(go.Scatter(x=df.index, y=df[f'{variable} smoothed'], mode='lines', name=f'{variable} smoothed', opacity=0.5))

    fig.update_layout(title= f'{Title} Actual vs Smoothed Data for {variable} with Wavelets', xaxis_title='Date', yaxis_title='Value')

    # optionally output the Plotly figure
    if output_plot:
        fig.show()

    smoothed_df = df.copy()
    return smoothed_df
```

### Machine Learning technique for outlier/anomaly detection: Isolation Forest

Isolation Forest is my choice of Machine Learning technique for outlier/anomaly detection in this project. It works by isolating observations that are abnormal or somewhat different from the majority of the data. Unlike other ML techniques for outlier detection, it achieves this by constructing random decision trees and partitioning the data points into increasingly smaller and more isolated subsets - it identifies anomalies as those data points that require fewer splits to be isolated from the rest of the data. Isolation Forest has the ability to handle high-dimensional data and has an advantageous computational ability.

#### Function description:

* **my_isolationForest(Title, Data, variable, contaminationLevel, plot_value_counts=True)**: This function will produce a Plotly graph with tracing for the variable of interest with abnormalities marked in red. It will also output a percentage of anomalies detected given the total number of points in out data, and it is also possible to plot a bar graph depicting the number of points with and without anomalies.

    * Title = string that will be displayed as graph title (i.e., "Farm1 Sep-Oct 2022 @ 20min moving Avg detection")
    * Data = input desired data frame to have a feature smoothed (i.e., "WestRoom_Far1_1day_movingAvg")
    * variable = string representing the farm feature of interest. (i.e., "East Room Humidity smoothed", "Nort Room Temp"...)
    * contaminationLevel = ‘auto’ or float, default=’auto’. The amount of contamination of the data set, i.e. the proportion of outliers in the data set. Used when fitting to define the threshold on the scores of the samples (i.e, 0.05, 0.15)
    * output_plot = bool, default False. Whether to include the Plotly graph in the result containig tracing for both actual and smoothed data

```python
from sklearn.ensemble import IsolationForest

def my_isolationForest(Title, Data, variable, contaminationLevel, plot_value_counts=True):
    
    data = Data[[variable]]
    model = IsolationForest(contamination=contaminationLevel)
    model.fit(data)
    data['anomaly'] = model.predict(data)
    
    # Calculate the percentage of anomalies
    percent_anomalies = round((sum(data['anomaly'] == -1) / len(data)) * 100, 2)
    
    # Create subplots for the anomaly plot and optional value counts bar chart
    fig = make_subplots(rows=1 + plot_value_counts, cols=1, subplot_titles=('Anomaly Detection', 'Anomaly Observation'))

    # Anomaly detection plot
    normal_trace = go.Scatter(x=data.index, y=data[variable], mode='lines', name='Normal')
    fig.add_trace(normal_trace, row=1, col=1)

    # Select anomalies only
    anomalies = data.loc[data['anomaly'] == -1, [variable]]

    anomaly_trace = go.Scatter(x=anomalies.index, y=anomalies[variable], mode='markers', name='Anomaly', 
                               marker=dict(color='red', size=8, line=dict(width=1, color='black')))
    fig.add_trace(anomaly_trace, row=1, col=1)

    fig.update_xaxes(title_text='Date', row=1, col=1)
    fig.update_yaxes(title_text=variable, row=1, col=1)

    if plot_value_counts:
        # Anomaly value counts bar chart
        value_counts_trace = go.Bar(x=['Not an Anomaly', 'Anomaly'], y=data['anomaly'].value_counts().values, name='Data Points')
        fig.add_trace(value_counts_trace, row=2, col=1)
        fig.update_layout(title= f'{Title} Isolation Forest Anomaly Detection', height=800, width=1000)
    else:
        fig.update_layout(title= f'{Title} Isolation Forest Anomaly Detection', height=500, width=1000)

    fig.show()
    return f'{percent_anomalies} is the percentage of anomalies oberved in this data'
```

# Fifth Concept/Method: Ability to make my analysis reproducible

First of all, replicability is one of my main concerns since the data wrangling portion, given that I have data files for five different farms. And although they are connected to the same type of gateway, they have slightly different sensor naming and placement at the farms. That is why I made a dictionary that all of my functions can reference, given the farm name. By making my code parametric to the specific farm I am working with, I can ensure that it will easily adapt to any differences in sensor naming or farm orientation. Implementing this practice allowed me to have only one Jupyter notebook that can work with any farm instead of having a notebook specific to each farm.

Secondly, I opted to create a variety of functions instead of having to do certain repetitive tasks over and over when trying to experiment with my data. This allows not only me to perform a certain analysis quickly but also any person to simply read the function documentation and be able to perform the same analysis and replicate my findings.

To test how easy it is to replicate and experiment with my functions, I first created resampled data frames using **create_df()** at 20 minutes, 1 hour, 12 hours, and 1-day resampling windows and plotted these using both **plot_df()** and **plot_dfHTW()**. These graphs aided me in observing interesting patterns that I would like to study further. Secondly, in the interest of showing the capabilities of my smoothing and ML functions, I only applied the three smoothing treatments (**my_movingAvg()**, **my_savgolFilter()**, **my_waveletFilter()**) and the anomaly detection (**my_isolationForest()**) to one sensor (Farm1: "*West Room Temp*") at two different resampling windows (20 minutes and 1 day). The table bellow depicts the results obtained:

#### Farm 1 Analysis

| Resampling window | Sensor         | Smoothing method  | Percentage of anomalies (using Isolation Forest) | Outputed Graph Name |
|:----------------- |:---------------|:------------------|:-------------------------------------------------|---------------------|
|20 minutes         | West Room Temp | None              | 10.01                                            | Treatment 1         |
|20 minutes         | West Room Temp | Moving Average    | 9.94                                             | Treatment 1a        |  
|20 minutes         | West Room Temp | Savitzky-Golay    | 10.01                                            | Treatment 1b        |
|20 minutes         | West Room Temp | Wavelets          | 10.01                                            | Treatment 1c        |
|1 day              | West Room Temp | None              | 15.57                                            | Treatment 2         |
|1 day              | West Room Temp | Moving Average    | 15.57                                            | Treatment 2a        |
|1 day              | West Room Temp | Savitzky-Golay    | 15.57                                            | Treatment 2b        |
|1 day              | West Room Temp | Wavelets          | 15.57                                            | Treatment 2c        |

#### Follow all the eight outputed graphs from the analysis above

* **Treatment 1**
    ![picture](https://github.com/FelipePicchi/Felipe_abe516x/blob/master/_includes/T1.png)
* **Treatment 1a**
    ![picture](https://github.com/FelipePicchi/Felipe_abe516x/blob/master/_includes/T1a.png)
* **Treatment 1b**
    ![picture](https://github.com/FelipePicchi/Felipe_abe516x/blob/master/_includes/T1b.png)
* **Treatment 1c**
    ![picture](https://github.com/FelipePicchi/Felipe_abe516x/blob/master/_includes/T1c.png)
    
* **Treatment 2**
    ![picture](https://github.com/FelipePicchi/Felipe_abe516x/blob/master/_includes/T2.png)
* **Treatment 2a**
    ![picture](https://github.com/FelipePicchi/Felipe_abe516x/blob/master/_includes/T2a.png)
* **Treatment 2b**
    ![picture](https://github.com/FelipePicchi/Felipe_abe516x/blob/master/_includes/T2b.png)
* **Treatment 2c**
    ![picture](https://github.com/FelipePicchi/Felipe_abe516x/blob/master/_includes/T2c.png)



```
Long, single-line code blocks should not wrap. They should horizontally scroll if they are too long. This line should be long enough to demonstrate this.
```

```
The final element.
```

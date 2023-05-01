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

    * Farm = string specifying the Farm of interest (i.e., "Farm1", "Farm2"...)
    * Month = string specifying the Month and year of interest (i.e., "November 2022")
    * RSP = Resampling period of interest, which is the input for pd.resample function where RSP is the offset string or object representing target conversion (i.e., "1min", "1H", "1W", "1M"...)

# Thrid Concept/Method: Data Visualization

The nature of my data requires a good way of graphically visualizing it in order to be able to manually identify patterns and trends that I should keep in mind before running any smoothing techniques or any machine and deep learning models. Before taking this class, I was doing all the data wrangling via Jupyter Notebook, but all the graphs were done using Excel - since it was the graphing tool I was more comfortable using. But after this semester, I became way more comfortable making high-end plots using Python libraries such as Seaborn and Plotly. I chose to use Plotly as my main graphical library in this project because it is an interactive visualization tool that allows me to zoom in and out, pan, take snapshots, and select specific plotting features, which helps me explore and easily discover patterns in my dataset. In this portion, I defined two functions for graphical data visualization. One allows me to plot a single graph that traces all the features from my data frame, while the other plots the same data but separate it by sensor type and placement (room placement given farm orientation) at the farm. 

### Function description:

* **plot_df(df, Title)**: This function outputs a single interactive Plotly graph with all the features within the data frame.

    * df = input desired data frame to be plotted (i.e., "Farm1_20min_SepDec2022")
    * Title = input string that will be displayed as graph title (i.e., "Farm1 Sep-Oct 2022 @ 20min")
   
* **plot_dfHTW(df, Farm)**: This function outputs an interactive Plotly graph that contains three subplots tracing separate graphs for Humidity, Temperature, and Water. Furthermore, this plot has a dropdown menu where you can select the graph to show the farm orientation 1 and 2 separately or together.

    * df = input desired data frame to be plotted (i.e., "Farm1_20min_SepDec2022")
    * Farm = string specifying the Farm of interest (i.e., "Farm1", "Farm2"...)

# Fourth Concept/Method: Data Smoothing and ML Model

## Smoothing Techniques

There are several data smoothing techniques that I could consider applying to our time-series data before running any machine or deep learning model for outlier detection. The choice of smoothing technique will depend on the specific characteristics of my time-series data and the analysis goals. Among various techniques, I chose to test **Moving Average**, **Savitzky-Golay**, and lastly, **Wavelet Transform**. Before researching more about smoothing techniques that could be applied to this project, I was only familiar with Moving Average and Savitzky-Golay. But I came across the method Wavelet Transform - which I am still exploring and experimenting with all the available parameters and modes.

### Smoothing with Moving Average
 
Smoothing using moving average is the first of three smoothing techniques I explore in this notebook. This is a common technique for reducing noise and identifying trends in time-series data. It involves taking a sliding window of a specified length, computing the average value within that window, and repeating this process for each subsequent data point in the time series, producing a new smoothed time series that highlights long-term trends while minimizing the impact of short-term noise. However, it introduces a lag -of the same size as the sliding window- in the resulting time series since each point of the smoothed value is based on the prior data.

#### Function description:

* **my_movingAvg(Title, Data, variable, window_size, output_plot=False)**:

    * Title = string that will be displayed as graph title (i.e., "Farm1 Sep-Oct 2022 @ 20min")
    * Data = input desired data frame to have a feature smoothed (i.e., "Farm1_20min_SepDec2022")
    * variable = string representing the farm feature of interest. (i.e., "East Room Humidity", "Nort Room Temp"...)
    * window_size = int, timedelta, str, offset, or BaseIndexer subclass. Size of the moving window: If an integer, the fixed number of observations used for each window. If a timedelta, str, or offset, the time period of each window. Each window will be a variable sized based on the observations included in the time-period. This is only valid for datetimelike indexes.
    * output_plot = bool, default False. Whether to include the Plotly graph in the result containig tracing for both actual and smoothed data.














Text can be **bold**, _italic_, or ~~strikethrough~~.

[Link to another page](./another-page.html).

There should be whitespace between paragraphs.

There should be whitespace between paragraphs. We recommend including a README, or a file with information about your project.

# Header 1

This is a normal paragraph following a header. GitHub is a code hosting platform for version control and collaboration. It lets you and others work together on projects from anywhere.

## Header 2

> This is a blockquote following a header.
>
> When something is important enough, you do it even if the odds are not in your favor.

### Header 3

```js
// Javascript code with syntax highlighting.
var fun = function lang(l) {
  dateformat.i18n = require('./lang/' + l)
  return true;
}
```

```ruby
# Ruby code with syntax highlighting
GitHubPages::Dependencies.gems.each do |gem, version|
  s.add_dependency(gem, "= #{version}")
end
```

#### Header 4

*   This is an unordered list following a header.
*   This is an unordered list following a header.
*   This is an unordered list following a header.

##### Header 5

1.  This is an ordered list following a header.
2.  This is an ordered list following a header.
3.  This is an ordered list following a header.

###### Header 6

| head1        | head two          | three |
|:-------------|:------------------|:------|
| ok           | good swedish fish | nice  |
| out of stock | good and plenty   | nice  |
| ok           | good `oreos`      | hmm   |
| ok           | good `zoute` drop | yumm  |

### There's a horizontal rule below this.

* * *

### Here is an unordered list:

*   Item foo
*   Item bar
*   Item baz
*   Item zip

### And an ordered list:

1.  Item one
1.  Item two
1.  Item three
1.  Item four

### And a nested list:

- level 1 item
  - level 2 item
  - level 2 item
    - level 3 item
    - level 3 item
- level 1 item
  - level 2 item
  - level 2 item
  - level 2 item
- level 1 item
  - level 2 item
  - level 2 item
- level 1 item

### Small image

![Octocat](https://github.githubassets.com/images/icons/emoji/octocat.png)

### Large image

![Branching](https://guides.github.com/activities/hello-world/branching.png)


### Definition lists can be used with HTML syntax.

<dl>
<dt>Name</dt>
<dd>Godzilla</dd>
<dt>Born</dt>
<dd>1952</dd>
<dt>Birthplace</dt>
<dd>Japan</dd>
<dt>Color</dt>
<dd>Green</dd>
</dl>

```
Long, single-line code blocks should not wrap. They should horizontally scroll if they are too long. This line should be long enough to demonstrate this.
```

```
The final element.
```

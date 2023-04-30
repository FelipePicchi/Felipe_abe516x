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
2.  Implementation of resampling statistics (Fill/Predicting missing data points)
3.  Employment of Python library that aids in data visualization and exploration
4.  Use of an unsupervised machine learning model for outlier/anomaly detection
5.  Ability to make my analysis reproducible


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

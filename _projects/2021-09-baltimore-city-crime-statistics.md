---
layout: page
title: Baltimore City Crime Data Dashboard
description: Project to construct a data dashboard to track and visualize crime statistics for Baltimore City.
importance: 4
img: assets/img/balt_city_crime/bcc_homepage.png
category: academic
---

**NOTE: This project was done over a duration of 12 weeks to fulfill the Course Project requirement for CMSC 636 - Data Visualization at University of Maryland, Baltimore County. The following is a paraphrased version of the 8 page project report that was written in conjunction with four peers: Rohit Mokashi, Jason Seaman, Paul Ledala, Dingyi Pei.**

Time Period: September, 2021 -- December, 2021
<br><br>

## Contents
-----
* [Introduction](#introduction)
* [Dataset Information](#dataset-information)
* [Goals](#goals)
* [Approach](#approach)
  * [D3 Visualizations](#d3-visualizations)
  * [Python Visualizations](#python-visualizations)
* [Future Improvements](#future-improvements)
<br><br>

## Introduction
-----
From the award-winning crime drama “The Wire”, to the site of the 2015 Freddie Gray riots, Baltimore has a contentious relationship with race and policing. The increasing number of crime incidents is an issue for both police departments and law enforcement. Increased crime is a product of inefficient policing, which in turn leads to more crime and therefore even worse policing. Therefore, data analysis becomes not just an essential task, but an urgent need of the hour, requiring analysts to carry out a comprehensive study of crime incidents to identify factors that contribute to the committing of a crime. Visualization techniques have been shown to be useful in various domains but have not been widely studied for applications in crime analysis where it is useful to identify the relationships among different entities such as people, vehicles, addresses and organizations. It is critical for crime analysts to retrieve, understand and analyze these relationships efficiently and effectively.
<br><br>

## Dataset Information
-----
In January 2021, current Baltimore mayor Brandon Scott re-launched the [Open-Baltimore](https://data.baltimorecity.gov/datasets/baltimore::part-1-crime-data-/explore) data project with policing transparency in mind, which provides us with Service calls, Crime, Arrest, and incident data for the city of Baltimore. The crime dataset is daily updated from October 30th, 1963, till the present.
<br><br>

## Goals
-----
* Create a data dashboard with different visualizations based on crime statistics of Baltimore City present online at the Open-Baltimore project.
* Build visual representations capable of handling large datasets and enable police to explore, compare, and analyze evolutionary trends and patterns of crime incidents.
<br><br>

## Approach
-----
The major deliverable of this project is a platform developed and structured on the webpage based on the backend server, allowing users to select parameters they are interested in to observe particular visualizations. The frontend of the product is a Vue application that gives the user an exploratory data experience with the Baltimore Police Department’s crime and arrest data. The user is immediately greeted with a large map of Baltimore’s municipal boundaries and a recent selection of crime data posted around the city.

<div class="row">
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/balt_city_crime/bcc_homepage.png" title="Homepage of Project" class="img-fluid rounded z-depth-1" %}
	</div>
</div>
<div class="row">
	<div class="col-sm mt-3 mt-md-0" style="text-align: center;vertical-align: middle;">
		Homepage of Project
	</div>
</div>
<br>

### D3 Visualizations
Considering the geographic nature of the crime incidents, an interactive map of Baltimore City was used for geospatial visualization. D3 was used as the primary approach here to show the incidents were mapped according to their geolocation. For more information, based on the geographical visualization, the specific incident categories at each area were added to provide a clear and general overview.

<div class="row">
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/balt_city_crime/d3_viz.png" title="D3 Visualization" class="img-fluid rounded z-depth-1" %}
	</div>
</div>
<div class="row">
	<div class="col-sm mt-3 mt-md-0" style="text-align: center;vertical-align: middle;">
		D3 Visualization
	</div>
</div>
<br>

### Python Visualizations
Several visualizations are created in Python using Matplotlib and seaborn libraries, which are very versatile libraries that help in plotting a wide range of visualizations. In this part, we focused more on the temporal domain of the crime data and provided the trajectory of incidents over years, months, days of the week and hours of the day. Multiple visualizations are developed allowing the users to select the parameters to observe the crime.

<div class="row">
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/balt_city_crime/bcc_py_viz1.png" title="Incidents By Day grouped by Month" class="img-fluid rounded z-depth-1" %}
	</div>
</div>
<div class="row">
	<div class="col-sm mt-3 mt-md-0" style="text-align: center;vertical-align: middle;">
		Incidents By Day grouped by Month
	</div>
</div>
<br>
<div class="row">
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/balt_city_crime/bcc_py_viz2.png" title="Incidents By District grouped by Month" class="img-fluid rounded z-depth-1" %}
	</div>
</div>
<div class="row">
	<div class="col-sm mt-3 mt-md-0" style="text-align: center;vertical-align: middle;">
		Incidents By District grouped by Month
	</div>
</div>
<br>
<div class="row">
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/balt_city_crime/bcc_py_viz3.png" title="Indoor and Outdoor Crimes by Month" class="img-fluid rounded z-depth-1" %}
	</div>
</div>
<div class="row">
	<div class="col-sm mt-3 mt-md-0" style="text-align: center;vertical-align: middle;">
		Indoor and Outdoor Crimes by Month
	</div>
</div>
<br>
<div class="row">
	<div class="col-sm mt-3 mt-md-0">
		{% include figure.html path="assets/img/balt_city_crime/bcc_py_viz4.png" title="Indoor and Outdoor Crimes by District" class="img-fluid rounded z-depth-1" %}
	</div>
</div>
<div class="row">
	<div class="col-sm mt-3 mt-md-0" style="text-align: center;vertical-align: middle;">
		Indoor and Outdoor Crimes by District
	</div>
</div>
<br>

## Future Improvements
-----
Due to time constraints, there was no chance to perform optimal data handling. Given the scope of the project, sufficient data preprocessing was done but there definitely is more potential for improvement. Sometimes, requesting a chart took up to 20 seconds, which calls for further investigation into data processing for quicker chart generation.

The version delivered for the project was running on a single data dump. If this project was to be made for an actual client, the ideal scenario would be to write an ETL code that would perform incremental data dumps, with the ETL framework consisting of a control table that tracks the last performed dump and only fetch the necessary rows from the source into the application.

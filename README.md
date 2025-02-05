<h1 align="center">Citi Bike Inventory Tracker</h1>

<h4 align="center">An end-to-end data-intensive application to track and predict Citi Bike inventory in New York, NY.</h4>

<hr>

## Table of Contents

- [Introduction](#introduction)
- [Data Sources](#data-sources)
- [Extract, Transform, Load](#extract-transform-load)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Modeling and ML Ops](#modeling-and-ml-ops)
- [Application](#application)
- [Contributors](#contributors)

<hr>

## Introduction

Citi Bike is a bike sharing program that allows users to rent bikes from various stations across Manhattan, Brooklyn, Queens, the Bronx, Jersey City, and Hoboken. The system has over 2,000 stations and over 30,000 bikes available for rent.

The goal of this project is to build an end-to-end application that tracks and predicts the inventory of a specific station that was assigned to our team by our instructor. The application will also take factors such as weather and bike availability into account when predicting the number of bikes available at the station.

> [!NOTE]
> Unfortunately, I am unable to share the data used for this project nor any screenshots of the application due to it having been taken offline prior to writing this document.

## Data Sources

The data used in this project comes from Citi Bike and weather data stored in the _Databricks_ file system across multiple CSV files. These CSV files are stored in separate directories for each data source, and new CSV files are added to the directories as new data becomes available.

## Extract, Transform, Load

The extract, transform, load (ETL) pipeline for this application is built using _Databricks_ and _PySpark_, and adheres to the [medallion architecture](https://www.databricks.com/glossary/medallion-architecture) design pattern.

1. Bronze layer
   - Raw data is ingested from the various data sources and stored in [delta tables](https://docs.databricks.com/en/introduction/delta-comparison.html#delta-tables-default-data-table-architecture) with little to no transformation.
   - Data in this layer is ingested via batch jobs for historical data as well as streaming jobs for real-time data.
2. Silver layer
   - Transformations are applied to data from the bronze layer to ensure data quality and consistency.
   - Defines an explicit schema for the data coming from the bronze layer.
   - Any machine learning models downstream are trained using the transformed data from this layer.
   - Implements partitioning and Z-ordering for optimized querying of the data.
3. Gold layer
   - Aggregates and refines data from the silver layer for real-time analytics and monitoring.
   - Data in this layer will be consumed downstream in the [Application](#application) step.

As a whole, the pipeline is designed to be resilient to failures and scale to large amounts of data. Additionally, it is meant to be immutable, meaning that there are no unintended side effects when reprocessing the same input data.

## Exploratory Data Analysis

The exploratory data analysis (EDA) phase of the project examines data from the silver layer of the ETL pipeline to uncover key trends and patterns.

Some examples of topics explored in this phase include:

- Monthly trip trends for the assigned station
- Daily trip trends for the assigned station
- How holidays affect the usage trends of the assigned station
- How weather affects the usage trends of the assigned station

## Modeling and ML Ops

This phase of the project focused on building a forecasting model to predict hourly net bike changes at the assigned station and putting it into production. The model is integrated into the _Databricks_ [MLflow](https://www.databricks.com/product/managed-mlflow) platform for tracking and versioning.

- Uses historical data from the silver layer to predict the net change in bike availability by hour for the assigned station.
  - Incorporates weather data as a feature to account for seasonal variations in bike availability.
- Registers models at staging and production levels in the _Databricks_ [model registry](https://docs.databricks.com/en/mlflow/models.html#register-models-in-the-model-registry).
- Logs models parameters, metrics, and artifacts of [MLflow experiments](https://docs.databricks.com/en/mlflow/experiments.html) for tracking, versioning, and reproducibility.
- Implements hyperparameter tuning techniques (e.g., [HyperOpt](https://docs.databricks.com/en/machine-learning/automl-hyperparam-tuning/index.html#hyperopt)) to optimize model performance.

The design of this step ensures that the forecasting model is scalable, production-ready, easily integrated into the application, and can be improved over time.

## Application

The final step of the project is to integrate the forecasting model into a real-time monitoring and inference system. It also has the job of ensuring that model predictions are continuously updated and evaluated for optimal performance.

- Displays data from the gold layer, such as:
  - Current datetime
  - Versions of staging and production models
  - Station name as well as its location marked on a map
  - Current weather (temperature and precipitation)
  - Total docks at the station
  - Total bikes available at the station
  - Forecast of the available bikes at the station for the next 4 hours
    - Alert if bikes a predicted to be out of stock or full within the next 4 hours
  - Residual plot illustrating the error in forecasts between the staging and production models
- Monitors and compares the performance of staging and production models.
  - The difference in performance between these models will widen as more real-time data is ingested via streaming.
  - Archives the current production model and promotes the staging model once the difference in performance is large enough.

<hr>

## Contributors

- **Jake Brehm** - [Email](mailto:mail@jakebrehm.com) | [LinkedIn](http://linkedin.com/in/jacobbrehm) | [Github](http://github.com/jakebrehm)
- **Jingtian Wang** - [Email](13jtjoshua@gmail.com) | [LinkedIn](https://www.linkedin.com/in/joshjingtianwang/) | [GitHub](https://github.com/JoshJingtianWang)
- **Corryn Collins** - [Email](mailto:corrync13@gmail.com) | [LinkedIn](https://www.linkedin.com/in/corryn-collins) | [Github](https://github.com/corrync)
- **Tessa Charles** - [Email](mailto:tmcharles1@outlook.com) | [LinkedIn](https://www.linkedin.com/in/tessa-m-charles) | [Github](https://github.com/tcharles12345)
- **Varun Arvind** - [Email](mailto:varun.arvind@gmail.com) | [LinkedIn](https://www.linkedin.com/in/varun-arvind)

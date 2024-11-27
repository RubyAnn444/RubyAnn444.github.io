# Capstone Project - Google Data Analytics 
### How does a bike-share navigate speedy success?

#### Introduction
Hey! My name is Ruby and I am currently on my path to becoming a Data Analyst! This project is the last step of completing the Google Data Analytics Professional Certificate. I hope you find this interesting :dizzy: For feedback, feel free to contact me.

#### Background 
I am assuming the role of a Junior Marketing Analyst in a fictional bike-sharing company called **Cyclistic**. Until now, Cyclistic’s marketing strategy relied on building general awareness and appealing to
broad consumer segments. One approach that helped make these things possible was the flexibility of its pricing plans: single-ride passes, full-day passes, and annual memberships.
Customers who purchase single-ride or full-day passes are referred to as casual riders.
Customers who purchase annual memberships are Cyclistic members.    
      
Cyclistic’s finance analysts have concluded that annual members are much more profitable
than casual riders. Although the pricing flexibility helps Cyclistic attract more customers,
The Director of Marketing believes that maximizing the number of annual members will be key to future growth. Rather than creating a marketing campaign that targets all-new customers, she believes there is a solid opportunity to convert casual riders into members. She notes that casual riders are already aware of the Cyclistic program and have chosen Cyclistic for their mobility needs.   
     
The program features more than **5,800** bicyles and **600** docking stations in the city of Chicago. Cyclistic sets itself apart by also offering reclining bikes, hand
tricycles, and cargo bikes, making bike-share more inclusive to people with disabilities and riders who can’t use a standard two-wheeled bike. The majority of riders opt for traditional bikes; about 8% of riders use the assistive options. Cyclistic users are more likely to ride for leisure, but about 30% use the bikes to commute to work each day.    
The business goal of the company is to optimize their marketing strategy in order to maximise the conversion of casual customers to members. In order to do that, however, the team needs to better understand how
annual members and casual riders differ, why casual riders would buy a membership, and how
digital media could affect their marketing tactics.   
     
My job is now to provide a detailed analysis on customer behaviour, looking at data between October 2023 and September 2024. I am focussing especially on the differences between the two customer groups, providing the executive team with a wide range of deliverables in each stage of the process.  
   
I am following the process of   
1. Ask
2. Prepare
3. Process
4. Analyse
5. Share

The case study recommends using Excel and SQL for some of the steps. I will be using R fr the majority of the case study, as becoming proficient in R is currently my biggest learning goal.  

#### Ask

The main problem I am trying to solve is ensuring long term profitability by adjusting the current marketing strategy of the firm. The insights will allow the marketing team to make informed decisions about their new strategy. Targeting the optimal customer segment depends on understanding buying behaviour of current members. Insights can drive business decisions by providing key information about the values of current members, their purchasing history and usage frequency, their potentially unmet needs and demographics. Insights will allow to design a strategy that targets the customer segment that leads to profit growth in the long run.

**Deliverable 1 - A clear statement of the business task**  
Delivering a detailed analysis on customer segments and their behavior, allowing to strategically maximise the conversion of casual customers.   

#### Prepare
##### Source and Data Organization
I am using the Cyclistic's historical trip data from October 2023 to September 2024 retrieved from [divvy_tripdata](https://divvy-tripdata.s3.amazonaws.com/index.html). The data has been made available by Motivate International Inc. under this [license](https://www.divvybikes.com/data-license-agreement). I have downloaded 12 csv files and stored them on my local drive. I used the function rbind in R to combine the files.

```
data202310 <- read_csv('/Users/rubygrambauer/Desktop/HiDrive/Case Study_csv/2023-10.csv')
data202311 <- read_csv('/Users/rubygrambauer/Desktop/HiDrive/Case Study_csv/2023-11.csv')
data202312 <- read_csv('/Users/rubygrambauer/Desktop/HiDrive/Case Study_csv/2023-12.csv')
data202401 <- read_csv('/Users/rubygrambauer/Desktop/HiDrive/Case Study_csv/2024-01.csv')
data202402 <- read_csv('/Users/rubygrambauer/Desktop/HiDrive/Case Study_csv/2024-02.csv')
data202403 <- read_csv('/Users/rubygrambauer/Desktop/HiDrive/Case Study_csv/2024-03.csv')
data202404 <- read_csv('/Users/rubygrambauer/Desktop/HiDrive/Case Study_csv/2024-04.csv')
data202405 <- read_csv('/Users/rubygrambauer/Desktop/HiDrive/Case Study_csv/2024-05.csv')
data202406 <- read_csv('/Users/rubygrambauer/Desktop/HiDrive/Case Study_csv/2024-06.csv')
data202407 <- read_csv('/Users/rubygrambauer/Desktop/HiDrive/Case Study_csv/2024-07.csv')
data202408 <- read_csv('/Users/rubygrambauer/Desktop/HiDrive/Case Study_csv/2024-08.csv')
data202409 <- read_csv('/Users/rubygrambauer/Desktop/HiDrive/Case Study_csv/2024-09.csv')

datatotal <- rbind(data202310, data202311, data202312, data202401, data202402, data202403, data202404, data202405, data202406, data202407, data202408, data202409)

```
The output of the following function shows that the total dataset includes 5,854,544 observations    
```
str(datatotal)
```

The data is organised in a table form that is stored in one csv file for each month. Each observation is equivalent to an ordered ride by a customer. Each ride is identified with a unique ID (primary key) and the following variables are recorded:  
* rideable_type (type of vehicle)
* started_at (moment of departure in date time format)
* ended_at (moment of arrival in date time format)
* start_station_name (name of the station - descriptive naming with street names)
* start_station_id
* end_station_name
* end_station_id
* start_lat
* start_lng
* end_lat
* end_lng
* member_casual (two options to classify whether the trip has been made by a casual renter or a member)    

Personal information has been removed from the dataset. Therefore, analysis is only possible on the ride dimension. I am unable to identify the amount of trips made by an individual customer.

#### Process
I have used R to explore, clean and transform the data. Looking at *null values, duplicates, outliers, format errors, typos and excess information.  

To begin, I have added two columns as indicated in the case study using the function `mutate`.
```
> calc_data <- mutate(datatotal, ride_length = seconds_to_period(ended_at - started_at), weekday = weekdays(started_at))
> 
> glimpse(calc_data)
Rows: 5,854,544
Columns: 15
$ ride_id            <chr> "4449097279F8BBE7", "9CF060543CA7B439", "667F21F4D6BDE69C", "F92714CC6B019B…
$ rideable_type      <chr> "classic_bike", "electric_bike", "electric_bike", "classic_bike", "classic_…
$ started_at         <dttm> 2023-10-08 10:36:26, 2023-10-11 17:23:59, 2023-10-12 07:02:33, 2023-10-24 …
$ ended_at           <dttm> 2023-10-08 10:49:19, 2023-10-11 17:36:08, 2023-10-12 07:06:53, 2023-10-24 …
$ start_station_name <chr> "Orleans St & Chestnut St (NEXT Apts)", "Desplaines St & Kinzie St", "Orlea…
$ start_station_id   <chr> "620", "TA1306000003", "620", "TA1306000003", "TA1306000003", "620", "620",…
$ end_station_name   <chr> "Sheffield Ave & Webster Ave", "Sheffield Ave & Webster Ave", "Franklin St …
$ end_station_id     <chr> "TA1309000033", "TA1309000033", "TA1307000111", "TA1307000111", "TA13070001…
$ start_lat          <dbl> 41.89820, 41.88864, 41.89807, 41.88872, 41.88872, 41.89812, 41.89818, 41.88…
$ start_lng          <dbl> -87.63754, -87.64441, -87.63751, -87.64445, -87.64445, -87.63753, -87.63755…
$ end_lat            <dbl> 41.92154, 41.92154, 41.88584, 41.88584, 41.88584, 41.92154, 41.92154, 41.88…
$ end_lng            <dbl> -87.65382, -87.65382, -87.63550, -87.63550, -87.63550, -87.65382, -87.65382…
$ member_casual      <chr> "member", "member", "member", "member", "member", "member", "member", "casu…
$ ride_length        <Period> 12M 53S, 12M 9S, 4M 20S, 5M 26S, 11M 30S, 14M 22S, 12M 0S, 5M 18S, 21M 4…
$ weekday            <chr> "Sunday", "Wednesday", "Thursday", "Tuesday", "Monday", "Wednesday", "Tuesd…
```
##### Null values 
The first step  in order to better understand null values is to count them! I have used `colSums(is.na())` to count the amount of null values in each column. Since these null values might indicate a valuable insight, I have further analysed them, checking for their average trip duration. The average trip duration of trips without a start station ID is 29 seconds. My hypothesis is, that these trips have been cancelled due to technical issues, however further context is missing. **When gathering insights about behavioural trends, I will not consider these null values any further**.
```
> na_counts <- colSums(is.na(datatotal))

> print(na_counts)
           ride_id      rideable_type         started_at           ended_at start_station_name 
                 0                  0                  0                  0            1056535 
  start_station_id   end_station_name     end_station_id          start_lat          start_lng 
           1056535            1091792            1091792                  0                  0 
           end_lat            end_lng      member_casual 
              7441               7441                  0 

## creating a dataframe only containing rows with no start_station_name and adding the difference in coordinates
data_na <- mutate(filter(calc_data, is.na(calc_data$start_station_name)), difference_lat = end_lat - start_lat, difference_lng = end_lng - start_lng)
glimpse(data_na)

## calculation of mean length (duration) and mean distance
mean_length_na <- mean(data_na$ride_length)
mean_difference_lat <- mean(data_na$difference_lat)
mean_difference_lng <- mean(data_na$difference_lng)


> print(mean_length_na)
[1] 29.25416
> print(mean_difference_lat)
[1] -0.0005656879
> print(mean_difference_lng)
[1] -3.607662e-05

```



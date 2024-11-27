# Capstone Project - Google Data Analytics 
## How does a bike-share navigate speedy success?

### Introduction
Hey! My name is Ruby and I am currently on my path to becoming a Data Analyst! This project is the last step of completing the Google Data Analytics Professional Certificate. I hope you find this interesting :dizzy:   
If you have any feedback, feel free to reach out to me 💗. 

### Background 
I am assuming the role of a Junior Marketing Analyst in a fictional bike-sharing company called **Cyclistic**. Until now, Cyclistic’s marketing strategy relied on building general awareness and appealing to broad consumer segments. One approach that helped make these things possible was the flexibility of its pricing plans: single-ride passes, full-day passes, and annual memberships.   
Customers who purchase single-ride or full-day passes are referred to as casual riders.   
Customers who purchase annual memberships are Cyclistic members.    
      
Cyclistic’s finance analysts have concluded that annual members are much more profitable than casual riders. Although the pricing flexibility helps Cyclistic attract more customers,
The Director of Marketing believes that maximizing the number of annual members will be key to future growth. Rather than creating a marketing campaign that targets all-new customers, she believes there is a solid opportunity to convert casual riders into members. She notes that casual riders are already aware of the Cyclistic program and have chosen Cyclistic for their mobility needs.   
     
The program features more than **5,800** bicyles and **600** docking stations in the city of Chicago. Cyclistic sets itself apart by also offering reclining bikes, hand
tricycles, and cargo bikes, making bike-share more inclusive to people with disabilities and riders who can’t use a standard two-wheeled bike. The majority of riders opt for traditional bikes; about 8% of riders use the assistive options. Cyclistic users are more likely to ride for leisure, but about 30% use the bikes to commute to work each day.    
The business goal of the company is to optimize their marketing strategy in order to maximise the conversion of casual customers to members. In order to do that, however, the team needs to better understand how annual members and casual riders differ, why casual riders would buy a membership, and how digital media could affect their marketing tactics.   
     
My job is now to provide a detailed analysis on customer behaviour, looking at data between October 2023 and September 2024. I am focussing especially on the differences between the two customer groups, providing the executive team with a wide range of deliverables in each stage of the process.  
   
I am following the process of   
1. Ask
2. Prepare
3. Process
4. Analyse
5. Share
6. Act

The case study recommends using Excel and SQL for some of the steps. I will be using R fr the majority of the case study, as becoming proficient in R is currently my biggest learning goal.  

### 1. Ask

The main problem I am trying to solve is ensuring long term profitability by adjusting the current marketing strategy of the firm. The insights will allow the marketing team to make informed decisions about their new strategy. Targeting the optimal customer segment depends on understanding buying behaviour of current members. Insights can drive business decisions by providing key information about the values of current members, their purchasing history and usage frequency, their potentially unmet needs and demographics. Insights will allow to design a strategy that targets the customer segment that leads to profit growth in the long run.

*** Hypothesis
Members are residents of Chicago, mainly commuting to university or work during the week while casual riders are using the product offering for leisure activities.    

### 2. Prepare
#### Source and Data Organization
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
The output of the following function `str(datatotal)`shows that the total dataset includes 5,854,544 observations    


The data is organised in a table form that is stored in one csv file for each month. Each observation is equivalent to an ordered ride by a customer. Each ride is identified with a unique ID (primary key) `ride_id` and the following variables are recorded:  
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

### 3. Process
I have used R to explore, clean and transform the data. Looking at *null values, duplicates and outliers*.  

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
#### Null values 
The first step  in order to better understand null values is to count them! I have used `colSums(is.na())` to count the amount of null values in each column. Since these null values might indicate a valuable insight, I have further analysed them, checking for their average trip duration. The average trip duration of trips without a start station ID is 29 seconds, and changes in longtitude and latitude are observable. My hypothesis is, that these trips have been cancelled due to technical issues, however further context is missing. **When gathering insights about behavioural trends, I will not consider these null values any further**.
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

## dropping the rows with null values
> clean_data <- drop_na(calc_data)
> print(colSums(is.na(clean_data)))
           ride_id      rideable_type         started_at           ended_at start_station_name 
                 0                  0                  0                  0                  0 
  start_station_id   end_station_name     end_station_id          start_lat          start_lng 
                 0                  0                  0                  0                  0 
           end_lat            end_lng      member_casual        ride_length            weekday 
                 0                  0                  0                  0                  0 
```
#### Duplicates
The only relevant variable to check for duplicates is our primary key `ride_id`. 121 duplicates are identified and removed with the `unique()` function. The variable `clean_data` is updated (count of rows before removing duplicates = 4,228,216; after removing duplicates 4,228,095).
```
##identifying duplicates in the primary key ride_id

> print(sum(duplicated(clean_data$ride_id)))
[1] 121
> clean_data <- clean_data[!duplicated(clean_data$ride_id, fromLast = TRUE), ]
> print(sum(duplicated(clean_data2$ride_id)))
[1] 0
```
#### Outliers 
I analysed the `ride_length` column for outliers and identified several negative values via filtering and sorting. For the purpose of this analysis, I have removed rides with a negative duration. Further analysis and a consultation of a specialist is needed in order to understand the context behind this data. 
```
> print(min(clean_data2$ride_length))
[1] -57
> print(count(filter(clean_data, ride_length < 0)))
# A tibble: 1 × 1
      n
  <int>
1    56
##updating the variable and checking if there are any negative values left
> clean_data <- clean_data[clean_data$ride_length>=0, ]
> print(count(filter(clean_data, ride_length < 0)))
# A tibble: 1 × 1
      n
  <int>
1     0
```
### 4. Analyse & 5. Share
At first of course I will prepare a nice and crisp descriptive analysis to familiarise myself more with the data. The R function `str` and `summary`, do a pretty good job on showcasing the datas features. Below you can see my results.
The columns `started_at` and `ended_at`, show the expected results, confirming that the dataset ranges from 2023-10-01 til 2024-09-30. The most interesting summary is `ride_length`, showing that the ride ranges from 0 seconds til >1 day! The average biker rides for about 16 1/2 minutes. Good workout! :muscle:
```
> summary(clean_data)
   ride_id          rideable_type        started_at                        ended_at                     
 Length:4228039     Length:4228039     Min.   :2023-10-01 00:00:05.00   Min.   :2023-10-01 00:02:02.00  
 Class :character   Class :character   1st Qu.:2024-02-17 09:06:34.50   1st Qu.:2024-02-17 09:18:27.00  
 Mode  :character   Mode  :character   Median :2024-05-31 23:00:52.00   Median :2024-05-31 23:18:32.00  
                                       Mean   :2024-05-04 03:56:45.15   Mean   :2024-05-04 04:13:17.26  
                                       3rd Qu.:2024-08-02 11:41:32.51   3rd Qu.:2024-08-02 11:57:05.93  
                                       Max.   :2024-09-30 23:52:58.17   Max.   :2024-09-30 23:59:52.55  
 start_station_name start_station_id   end_station_name   end_station_id       start_lat    
 Length:4228039     Length:4228039     Length:4228039     Length:4228039     Min.   :41.65  
 Class :character   Class :character   Class :character   Class :character   1st Qu.:41.88  
 Mode  :character   Mode  :character   Mode  :character   Mode  :character   Median :41.89  
                                                                             Mean   :41.90  
                                                                             3rd Qu.:41.93  
                                                                             Max.   :42.06  
   start_lng         end_lat         end_lng       member_casual       ride_length                   
 Min.   :-87.86   Min.   :41.65   Min.   :-87.84   Length:4228039     Min.   :0S                     
 1st Qu.:-87.66   1st Qu.:41.88   1st Qu.:-87.66   Class :character   1st Qu.:5M 48S                 
 Median :-87.64   Median :41.89   Median :-87.64   Mode  :character   Median :10M 4.07399988174438S  
 Mean   :-87.64   Mean   :41.90   Mean   :-87.64                      Mean   :16M 32.1228168166446S  
 3rd Qu.:-87.63   3rd Qu.:41.93   3rd Qu.:-87.63                      3rd Qu.:18M 4.28200006484985S  
 Max.   :-87.53   Max.   :42.06   Max.   :-87.53                      Max.   :1d 1H 9M 22S           
   weekday         
 Length:4228039    
 Class :character  
 Mode  :character  
                   
```
I got really interested in the distribution of the rides on weekdays. Seems like the winner is Wednesday! On Sunday the number of rides is by far the lowest.
```
> weekday_counts <- table(clean_data$weekday)
> print(weekday_counts)

   Friday    Monday  Saturday    Sunday  Thursday   Tuesday Wednesday 
   590292    588906    637763    566349    613315    590236    641178 
```
Last but not least, I compared the overall count of casual riders and members with each other. (I added commas for readability). These values show an interesting picture of the current distribution of rides. It is important to remember tho, what these numbers cannot show!  
One can assume, that one annual member is taking several rides wheras it is more unlikely that a casual rider will committ to riding the bike more often. When interpreting these differences, it seems at first as if the company is already doing a great job at attracting annual members. However, since I am unable to count the amounts of individuals, these numbers do not give a concrete insight into current customer numbers. 
```
> member_casual_counts <- table(clean_data$member_casual)
> print(member_casual_counts)

 casual    member 
1,504,205 2,723,834
```
Let's analyse and visualise a little more, looking at the average ride length of both customer groups. At first, I ordered the weekdays and then created a bar chart that visualizes the distribution of rides throughout the week for members and casual riders. It shows clearly, that members are mainly using the bikes throughout the week, possibly for work purposes. This is a valuable insight for the marketing team.
```
> clean_data$weekday <- factor(clean_data$weekday, levels = c("Monday","Tuesday","Wednesday","Thursday","Friday","Saturday","Sunday"))

> clean_data %>% 
+   ggplot()+ 
+   labs(title = "Number of rides by weekday", subtitle =  "Casual Riders and Members", x = "Day of the week")+
+   scale_y_continuous(name = "Count (thousands)", labels = function(y) y/ 1000)+
+   geom_bar(aes(x = weekday, fill = weekday)) +
+   facet_wrap(~member_casual) + 
+   theme(axis.text.x = element_text(angle = 45))+
+   scale_fill_brewer(palette = "Set2")
> 
```
<img width="527" alt="Bildschirmfoto 2024-11-27 um 18 13 22" src="https://github.com/user-attachments/assets/007d088f-e3ab-4972-85b5-30cc2fd8471a">

Next I looked at the mean ride length of each member group, also distributed over the days of the week. Members have on average, a shorter ride_length. This is also a valuable insight for the marketing team. As an addition to my analysis, one could perform a geographic analysis of current casual riders and members to compare which docking stations are the most popular and what sights/institutions/offices are around.
```
clean_data <- mutate(clean_data, ride_length_in_s = ended_at - started_at)
clean_data %>% 
  group_by(weekday, member_casual) %>% 
  summarise(mean_ride_length = mean(ride_length_in_s)) %>% 
  ggplot() +
  labs(title = "Mean ride length by day of the week", caption = "This graph shows the mean ride length for both casual customers and for members", x = "Day of the week", y = "Mean ride length")+
  scale_fill_brewer(palette = "Set2")+
  geom_bar(aes(x = weekday, y = mean_ride_length, fill = weekday), stat = "identity", show.legend = FALSE)+
  theme(axis.text.x = element_text(angle = 45))+
  facet_wrap(~member_casual) +
  scale_y_continuous(name = "Mean ride length (minutes)", labels =function(y) round(y/60, digits=1))
```
<img width="530" alt="Bildschirmfoto 2024-11-27 um 18 15 29" src="https://github.com/user-attachments/assets/8b6c642c-8469-4a24-a2a3-cd819765877b">

I also conducted a two-part analysis on the vehicles used by each customer group. The absolute numbers show, that the traditional bike is by far the most used vehicle type. E-bikes come in second, while e-scooters do not seem very popular. In order to compare the two customer groups, I looked at the relative distrubution of all three vehicle types. Both customer groups, show very similar behaviour. Further analysis could investigate the actual availability/usage ratio of electric scooters and compare between both member groups.
```
## most used vehicle type - relative
clean_data %>% 
  ggplot() +
  labs(title = "Proportion of vehicles used by customer groups", x = "Casual/Member")+
  scale_fill_brewer(palette = "Set2")+
  geom_bar(aes(x = member_casual, fill = rideable_type), show.legend = TRUE, position = "fill")+
  theme(axis.text.x = element_text(angle = 45))

## absolute count of vehicle type
clean_data %>% 
  ggplot() +
  labs(title = "Amount of rides per vehicle", x = "Vehicle Type")+
  scale_fill_brewer(palette = "Set2")+
  geom_bar(aes(x = rideable_type, fill = rideable_type), show.legend = TRUE, position = "dodge")+
  theme(axis.text.x = element_text(angle = 40))+
  facet_wrap(~member_casual)+
  scale_y_continuous(name = "Count (thousands)", labels = function(y) y/1000)

```
<img width="534" alt="Bildschirmfoto 2024-11-27 um 18 20 34" src="https://github.com/user-attachments/assets/c7e77c32-88a9-47d1-8f6e-c31b9e94c236"><img width="519" alt="Bildschirmfoto 2024-11-27 um 18 21 07" src="https://github.com/user-attachments/assets/2cf92b73-066c-4338-b84a-f2820f252645">

### 6. Act
My main conclusion of this analysis is threefold    
* Members have more frequent but on average shorter rides, while casual drivers ride less frequently for a longer time
* Both customer groups mostly use the classic bike
* Members mostly use the service throughout the week while casual customers ride the bike on the weekend more frequently

These findings clearly confirm the hypothesis that members tend to use the bike sharing offering for commuting from the residential area to their workplace, university or school, while casual riders are using the product for leisure or tourism.  
  
Eventhough my task is not to formulate the marketing strategy, I would recommend to target international students and busy businesspeople, who want to be active without investing in their own bike. Short-term memberships would be an amazing option to attract more casual riders into purchasing a membership. Tourist packages would allow to convert "short-term casuals" to commit to Cyclistics offering. 

## Thanks 💗
Thank you for browsing through my analysis! I surely had a lot of fun doing this case study and I am excited about where I am going to move from here.
Bye for now, I hope you have a great day!

# Google Data Analytics Professional Certificate Case Study: Cyclistic

## Introduction
**Client:** Cyclistic

Cyclistic is a bike-share program that features more than 5,800 bicycles and 600 docking stations. Cyclistic sets itself apart by also offering reclining bikes, hand tricycles, and cargo bikes, making bike-share more inclusive to people with disabilities and riders who can’t use a standard two-wheeled bike. The majority of riders opt for traditional bikes; about 8% of riders use the assistive options. Cyclistic users are more likely to ride for leisure, but about 30% use the bikes to commute to work each day.

**Report to:** Lily Moreno, Director of Marketing

**Audience:** Cyclistic executive team

## Ask
1. How do annual members and casual riders use Cyclistic bikes differently?
2. Why would casual riders buy Cyclistic annual memberships?
3. How can Cyclistic use digital media to influence casual riders to become members?

Moreno has assigned me with answering the first question and brainstorming ideas for a marketing strategy that will encourage riders to make the switch from casual riders to annual members.

## Prepare
I was given access to the complete ride history of 2023. I decided that I needed to determine the number of rides and average ride length for both casual riders and members. I also wanted to find the average number of rides per day of the week for each type of rider, and the same information for each season of the year. The original files can be found [here](https://divvy-tripdata.s3.amazonaws.com/index.html).

## Process
I uploaded each month's worth of data into BigQuery, where I used SQL to extract the data that I needed, exclude the unnecessary data, and clean and organize the data into fresh tables for analysis, while keeping original copies of the raw data separate for reference.

### Key SQL Queries

```sql

/*After looking at the data and considering the question put forth by the prompt, I decided that not all columns provided where relevant to my analysis, so I chose to look only at the ones that I thought would be most helpful, and created a couple more of my own, including time extraction, day of week, and ride length. I worked with one month at a time before combining them into a new, clean table*/

SELECT
    ride_id,
    member_casual,
    EXTRACT(TIME FROM started_at) AS start_time,
    EXTRACT(TIME FROM ended_at) AS end_time,
    EXTRACT(DAYOFWEEK FROM started_at) AS day_of_week,
    CAST
        (EXTRACT(MINUTE FROM (ended_at - started_at)) AS FLOAT64)
        AS ride_length_minutes
FROM winged-yeti-423618-j9.case_study_1.jan_original
;

/*After I made a table that fit my needs, I saved it as a new table and ran my further queries from there, preserving the orginal data.

I first wanted to see the number of rides and average length of a ride on a given weekday in January, based on member type. I used count-distinct to eliminate any duplicate rows. I also wanted the see the days of the week as actual names, instead of numbers.

I'm also applying a filter to remove any rides under 5 minutes and over 12 hours, as I believe those very short or very long rides are outliers that will skew my results.*/

SELECT
  member_casual,
  CASE day_of_week
    WHEN 1 THEN 'Sunday'
    WHEN 2 THEN 'Monday'
    WHEN 3 THEN 'Tuesday'
    WHEN 4 THEN 'Wednesday'
    WHEN 5 THEN 'Thursday'
    WHEN 6 THEN 'Friday'
    WHEN 7 THEN 'Saturday'
    ELSE 'Unknown'
  END AS day_name,
  COUNT(DISTINCT ride_id) AS ride_count,
  ROUND(AVG(ride_length_minutes),1) AS ride_length_avg
FROM
  winged-yeti-423618-j9.case_study_1.january
WHERE ride_length_minutes >= 5 AND ride_length_minutes <= 720
GROUP BY day_of_week, member_casual
ORDER BY CASE day_of_week
  WHEN 1 THEN 1
  WHEN 2 THEN 2
  WHEN 3 THEN 3
  WHEN 4 THEN 4
  WHEN 5 THEN 5
  WHEN 6 THEN 6
  WHEN 7 THEN 7
  ELSE 8
END,
member_casual
;

/*Next, I repeated the process with the other months of the year 2023, and perfomed a UNION with each of the 12 tables to create one for the whole year.*/
```
## Analyze
In my analysis, I discovered several trends:

There are about one-third more annual members than casual riders.
Members consistently take more rides than casual riders.
Casual riders tend to take longer rides, especially in the warmer summer months.
Casual riders predominantly use the service on weekends, while members ride more during the week.
This suggests that locals use Cyclistic for commuting, and a mix of locals and tourists use the service for leisure on summer weekends.

## Share
I have prepared a Tableau story featuring a series of dashboards that illustrate my findings, including brief annotations explaining the charts in more detail.

### [View my Tableau story here, on Tableau Public.](https://public.tableau.com/views/GoogleDataAnalyticsCaseStudy-Cyclistic_17170094618250/Story1?:language=en-US&:sid=&:display_count=n&:origin=viz_share_link)

## Act
The final slide contains a few suggestions for Moreno and the executives to consider for the upcoming marketing campaign. My top 3 recommendations are:

1. Offer a Discounted Membership: Provide a discounted membership or a free month to new members who sign up during the slower fall and winter months.
2. Promote Commuting Benefits: Highlight the advantages of reducing car usage for a cleaner, greener, and healthier city to encourage locals to bike to work during the week.
3. Referral Program: Offer members a code they can "gift" to a friend, redeemable for a free ride for first-time users, to encourage them to try the service and consider buying a membership.

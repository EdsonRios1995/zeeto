# zeeto
Requests by zeeto

Both requests are present in zeeto ipynb, running all the cells would create the requested json files (One oriented by records and another orientes by columns), create a database and run the query for creating the table of question 2.

Kindly let me know of all your suggestions and comments about this.


Requirements: 
1) zeeto2.csv 
2) zeeto.ipynb 

instructions: 
1) Run zeeto.ipynb cell by cell or all at once. 

SQL query:


SELECT r.campaign_name, r.visits, r.impressions, i.last_impressed_at_pst, r.conversions, c.last_converted_at_pst, tr.total_revenue

FROM 
(SELECT campaign_name, 
count(CASE event_type WHEN 'visit_created' THEN 1 END) as visits,
count(CASE event_type WHEN 'campaign_impressed' THEN 1 END) as impressions, 
count(CASE event_type WHEN 'campaign_converted' THEN 1 END) as conversions from raw_campaign_events group by campaign_name) r 

LEFT join 
(SELECT campaign_name, MAX(datetime(timestamp_utc||'+08:00')) as last_impressed_at_pst FROM raw_campaign_events where event_type = 'campaign_impressed' group by campaign_name,event_type) i
on r.campaign_name = i.campaign_name

LEFT join 
(SELECT campaign_name, MAX(datetime(timestamp_utc||'+08:00')) as last_converted_at_pst FROM raw_campaign_events where event_type = 'campaign_converted' group by campaign_name,event_type) c
on r.campaign_name = c.campaign_name

LEFT JOIN
(SELECT campaign_name, sum(revenue) as total_revenue FROM raw_campaign_events where event_type = 'campaign_converted' group by campaign_name) tr
on r.campaign_name = tr.campaign_name

group by r.campaign_name;



*Notes:
First try with pandas calculations lead to different results. It should be a bug with library. Final try should be the one taken into account.

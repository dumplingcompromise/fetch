#Fetch Rewards Coding Exercise - Analytics Engineer
https://fetch-hiring.s3.amazonaws.com/analytics-engineer/ineeddata-data-modeling/data-modeling.html

## First: Review Existing Unstructured Data and Diagram a New Structured Relational Data Model

The three datasets that were provided with the assignment were "Receipts", "Users", and "Brands". 

After inspecting the JSON in the files, it became clear that there was no natural identifier linking Brands to Receipts because the pipe-=delimited values in Receipts > rewardsReceiptItemList did not contain a brand ID or barcode value to link to the Brands table. It also became evident that another level of granularity would be needed to link Receipts to Brands, which I created via the receipt_product table. 

<img width="820" alt="Screen Shot 2022-04-14 at 7 33 00 AM" src="https://user-images.githubusercontent.com/12734395/163382697-f3ecf97f-a935-4c34-b55d-c0d1b52bea12.png">

##Second: Write a query that directly answers a predetermined question from a business stakeholder

What are the top 5 brands by receipts scanned for most recent month?

`select brand, count(*)  
from 
fetch.receipt_product p
join fetch.brands b on p.brand_id = brands.brand_id
join fetch.receipts r on r._id = p.receipts_id
where date_trunc('month', r.purchasedate) = max(date_trunc('month', r.purchasedate))
order by count(*) desc
limit 5
`

## Third: Evaluate Data Quality Issues in the Data Provided

The brand data contained a number of duplicates which made it unfit to be a dimension table. 

Additionally, the pipe-delimited values in the rewardsReceiptItemList didn't seem to contain any information that would allow us to link to the brand dimension. It was also not a quick task to figure out how to parse the data in rewardsReceiptItemList in columns as there could be a number of different values in any given position of the pipe-delimited string.

The issues became evident after I utilized https://jsonformatter.org/ to convert the JSON to CSVs and inspected the CSVs utilizing Google Sheets. 


## Fourth: Communicate with Stakeholders

Hello-

While constructing a new analytical database for our receipts data I encountered some discrepancies that I was hoping to clarify. The main issue I encountered was the lack of a brand identifier and/or barcode in the Receipt Item List data that is sent over with each transaction. 

Below is an example of a record in that field. Can you help me identify what the "013562300631" number below represents? If you know of any other ways we can use the data below to join to our brands data that would be helpful. Also if you could provide any details around how data will be structured in rewardsReceiptItemList, that would be helpful as well in setting up a structured databased around this data. 

013562300631|Annie's Homegrown Organic White Cheddar Macaroni & Cheese Shells, 6 Oz|50.00|50.00|013562300631|50.00|true|POINTS_GREATER_THAN_THRESHOLD|1|1|Action not allowed for user and CPG|5332f5f3e4b03c9a25efd0ae|5|ANNIE'S HOMEGROWN MULTI-SERVING MAC & CHEESE|5332f5f3e4b03c9a25efd0ae  



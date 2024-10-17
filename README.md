# Coffee Shop Sales Project

### Dashboard Link : https://github.com/user-attachments/assets/6d3e6104-e065-4d16-8a67-50b50e63f797

## Problem Statement

ABC is a fictitious Coffee Shop having it's store locations in 3 districts of New York state. This dashboard helps to understand the performance of the coffee sales business in these 3 stores overa period of 6 moths(from January 2023 to June 2023) and guide the decisison makers regarding future strategies on the basis of insights gathered from the dashboard.

The project is carried out in 2 parts, where in Part 1, we use SQL to retrieve information from the database, by firing multiple queries depending upon the business requirements. In Part 2, we will use Power-BI, to visualise and analyse the data by incorporating interactive elements like charts, heat maps and KPI banners. 

KPI requirements: 

    1) Total Sales Analysis: Calculate total sales for each respective month, determine month-on-month increase/decrease and calculate difference in sales between selected month and the previous month. 

    2) Total Order Analysis: Calculate total number of orders for each respective month, determine month-on-month increase/decrease and calculate difference in number of orders placed between selected month and the previous month. 

    3) Total Quantity Sold Analysis: Calculate total quantity sold for each respective month, determine month-on-month increase/decrease and calculate difference in total quantity placed between selected month and the previous month.


### Part 1: Data Preparation & Cleaning through Excel and MY SQL  

- Step 1 : Review the dataset which is available as a csv file in excel and analyse the number of column heads and the number of rows for the data.  

- Step 2: Load data into MS SQL server by creating a new database and importing the aforementioned flat files as tables. The data is then cleaned and transformed by checking for duplicates modifying the columns types appropriately for each of the column heads. 

- Step 3: The next step includes firing SQL queries depending upon the business requirements and storing the results. We will also prepare a WORD file to document the SQL queries for future reference. 

- Step 4: In MY SQL, we will use the following SQL queries to determine the KPI requirements as mentioned in the Problem statement. 

#### Total Sales Analysis: 

    	a) Calculate Total Sales for each respective month - 

    SELECT ROUND(SUM(transaction_qty * unit_price), 1) AS   Total_Sales
    FROM [Coffee Shop Sales]
    WHERE MONTH(transaction_date) = 5 --May Month

    b) Determine month-on-month increase/decrease and calculate difference in Sales between selected month and the previous month - 

    SELECT 
    MONTH(transaction_date) AS month,
    ROUND(SUM(unit_price * transaction_qty)) AS total_sales,
    (SUM(unit_price * transaction_qty) - LAG(SUM(unit_price * transaction_qty), 1)
    OVER (ORDER BY MONTH(transaction_date))) / LAG(SUM(unit_price * transaction_qty), 1) 
    OVER (ORDER BY MONTH(transaction_date)) * 100 AS mom_increase_percentage
    FROM [Coffee Shop Sales]
    WHERE MONTH(transaction_date) IN (4, 5) -- for months of April and May
    GROUP BY MONTH(transaction_date)
    ORDER BY MONTH(transaction_date);

 Below is the snapshot of the query fired and the subsequent result:
     ![CS 1](https://github.com/user-attachments/assets/934ff7f4-eea1-4aab-a20d-b176fc90fd16)
    

   #### Total Order Analysis:  

    a) Calculate Total Orders for each respective month - 

    SELECT  COUNT(transaction_id) AS Total_Orders
    FROM [Coffee Shop Sales]
    WHERE MONTH(transaction_date) = 3 --March Month

   The result for the above query is displayed as: 

   ![CS 2](https://github.com/user-attachments/assets/87c07d85-61e6-41c7-b7e5-31760cba70e4)

    b) Determine month-on-month increase/decrease and calculate difference in number of orders placed between selected month and the previous month - 

    SELECT 
    MONTH(transaction_date) AS month,
    ROUND(COUNT(transaction_id), 1) AS total_orders,
    (COUNT(transaction_id) - LAG(COUNT(transaction_id),1) 
    OVER (ORDER BY MONTH(transaction_date))) / LAG(COUNT(transaction_id),1) 
    OVER (ORDER BY MONTH(transaction_date)) * 100 AS MOM_increase_percentage
    FROM 
    [Coffee Shop Sales]
    WHERE 
    MONTH(transaction_date) IN (4,5) -- for April and May
    GROUP BY 
    MONTH(transaction_date)
    ORDER BY 
    MONTH(transaction_date);

#### Total Quantity Analysis:

    a) Calculate total quantity sold for each respective month - 
    
    SELECT
    SUM(transaction_qty) AS Total_Quantity_Sold
    FROM [Coffee Shop Sales]
    WHERE MONTH(transaction_date) = 5 -- for month of May


![CS 3](https://github.com/user-attachments/assets/116d615d-1d7a-40ba-b698-c46acc4e7420)

    b) Determine month-on-month increase/decrease and calculate difference in total quantity placed between selected month and the previous month - 
    SELECT
    MONTH(transaction_date) AS Month, --Month Number
    ROUND(SUM(transaction_qty),0) AS Total_Quantity_Sold, --Total Quantity Column
    (SUM(transaction_qty) - LAG(SUM(transaction_qty), 1)
    OVER(ORDER BY MONTH(transaction_date))) / LAG(SUM(transaction_qty), 1)
    OVER(ORDER BY MONTH(transaction_date)) * 100 AS MOM_Increase_Percentage
    FROM [Coffee Shop Sales]
    WHERE MONTH(transaction_date) IN(4,5) -- For months of April and May
    GROUP BY MONTH(transaction_date)
    ORDER BY MONTH(transaction_date)
Below is the snapshot of the query fired for Total Quantity Analysis and the subsequent result:
![CS 4](https://github.com/user-attachments/assets/9ef6bd20-dbe1-4093-8ddd-42537bd76503)

- Step 5: Now, for displaying the metrics for the daily Sales, Quantity Sold and Orders Placed, we will use the following query: 

      SELECT
      SUM(unit_price * transaction_qty) AS total_sales,
      SUM(transaction_qty) AS total_quantity_sold,
      COUNT(transaction_id) AS total_orders
      FROM [Coffee Shop Sales]
      WHERE transaction_date = '2023-05-18'; --For 18 May 2023

If you want to get exact Rounded off values then use below query to get the result:

    SELECT
      CONCAT(ROUND(SUM(unit_price * transaction_qty)/1000, 1), 'K') AS total_sales,
      CONCAT(ROUND(SUM(transaction_qty)/1000, 1), 'K') AS total_quantity_sold,
      CONCAT(ROUND(COUNT(transaction_id)/1000, 1), 'K') AS total_orders
      FROM [Coffee Shop Sales]
      WHERE transaction_date = '2023-05-18'; --For 18 May 2023

![CS 5](https://github.com/user-attachments/assets/bbb906c0-b213-4728-915c-f3b0dea073e2)

- Step 6: For the Sales Analysis by weekdays and weekends, and understanding the performance variations, the following query is used - 

      SELECT
      CASE WHEN DAYOFWEEK(transaction_date) IN (1,7) THEN 'Weekends'
      ELSE 'Weekdays'
      END AS Date_Type,
      ROUND(SUM(unit_price * transaction_qty),2) AS Total_Sales
      FROM 
      [Coffee Shop Sales]
      WHERE MONTH(transaction_date) = 5  -- Filter for May
      GROUP BY 
      CASE 
      WHEN DAYOFWEEK(transaction_date) IN (1, 7) THEN 'Weekends'
      ELSE 'Weekdays'
      END;
![CS 6](https://github.com/user-attachments/assets/a8d56946-4a1f-4683-9a84-78949f7b5d9e)

- Step 7: The Sales Analysis for the store wise location is calculated - 

      SELECT store_location,
      SUM(unit_price * transaction_qty) AS Total_Sales
      FROM [Coffee Shop Sales]
      WHERE MONTH(transaction_date) = 5
      GROUP BY store_location
      ORDER BY SUM(unit_price * transaction_qty) DESC;


![CS 7](https://github.com/user-attachments/assets/55206836-e318-466a-b0ec-82a532f47a00)

- Step 8: Now, for the daily Sales Analysis with Average Line, we’ll start with the following query – 

      SELECT AVG(Total_Sales) AS Average_Sales
      FROM
      (
      SELECT SUM(unit_price * transaction_qty) AS Total_Sales
      FROM [Coffee Shop Sales]
      WHERE MONTH(transaction_date) = 5 -- May Month
      GROUP BY transaction_date – for Individual dates
      ) AS Internal_Query

![CS 8](https://github.com/user-attachments/assets/18784243-4f89-4646-bcc6-bdc63e3ec4e9)

- Step 9: 8)	Next step is calculating the daily sales for a selected month using the below query – 

      SELECT 
      DAY(transaction_date) AS day_of_month,
      ROUND(SUM(unit_price * transaction_qty),1) AS total_sales
      FROM [Coffee Shop Sales]
      WHERE MONTH(transaction_date) = 5  -- Filter for May
      GROUP BY DAY(transaction_date)
      ORDER BY DAY(transaction_date);

      
![CS 9](https://github.com/user-attachments/assets/c3e6fe67-bd65-4c37-8822-760bdfe6009b)

- Step 10: Sales Trend for the daily sales with Sales status for each day(whether it's Below Average and Above Average) - 

      SELECT 
      day_of_month,
      CASE 
        WHEN total_sales > avg_sales THEN 'Above Average'
        WHEN total_sales < avg_sales THEN 'Below Average'
        ELSE 'Average'
      END AS sales_status,
      total_sales
      FROM (
      SELECT 
        DAY(transaction_date) AS day_of_month,
        SUM(unit_price * transaction_qty) AS total_sales,
        AVG(SUM(unit_price * transaction_qty)) OVER () AS avg_sales
      FROM 
        [Coffee Shop Sales]
      WHERE 
        MONTH(transaction_date) = 5  -- Filter for May
		GROUP BY 
        DAY(transaction_date)
      )AS sales_data
      ORDER BY 
      day_of_month;

The results(for May month) will appear as follows - 


![CS 10](https://github.com/user-attachments/assets/0e861068-8d09-4b44-9a0a-381f4833676d)

- Step 11: For analysing the Sales w.r.t. Product Category, we will use the below query - 

      SELECT 
      product_category,
	  ROUND(SUM(unit_price * transaction_qty),1) as Total_Sales
      FROM [Coffee Shop Sales]
      WHERE
	  MONTH(transaction_date) = 5 
      GROUP BY product_category
      ORDER BY SUM(unit_price * transaction_qty) DESC;

![CS 11](https://github.com/user-attachments/assets/5a4b22ca-b824-496d-9004-f169794d8c80)

- Step 12: The query for examining the Top 10 Product Type is fired as follows - 


      SELECT 
      TOP 10 product_type,
	  ROUND(SUM(unit_price * transaction_qty),1) as Total_Sales
      FROM [Coffee Shop Sales]
      WHERE
	  MONTH(transaction_date) = 5 
      GROUP BY product_type
      ORDER BY SUM(unit_price * transaction_qty) DESC;

![CS 12](https://github.com/user-attachments/assets/c18b2d4e-d623-4169-a21f-c57691bbf579)

- Step 13: In the Sales analysis by days and hours for the Coffee Shop - 

      SELECT 
      CASE 
        WHEN DAYOFWEEK(transaction_date) = 2 THEN 'Monday'
        WHEN DAYOFWEEK(transaction_date) = 3 THEN 'Tuesday'
        WHEN DAYOFWEEK(transaction_date) = 4 THEN 'Wednesday'
        WHEN DAYOFWEEK(transaction_date) = 5 THEN 'Thursday'
        WHEN DAYOFWEEK(transaction_date) = 6 THEN 'Friday'
        WHEN DAYOFWEEK(transaction_date) = 7 THEN 'Saturday'
        ELSE 'Sunday'
        END AS Day_of_Week,
        ROUND(SUM(unit_price * transaction_qty)) AS Total_Sales
        FROM [Coffee Shop Sales]
        WHERE 
        MONTH(transaction_date) = 5 -- Filter for May (month number 5)
        GROUP BY 
        CASE 
        WHEN DAYOFWEEK(transaction_date) = 2 THEN 'Monday'
        WHEN DAYOFWEEK(transaction_date) = 3 THEN 'Tuesday'
        WHEN DAYOFWEEK(transaction_date) = 4 THEN 'Wednesday'
        WHEN DAYOFWEEK(transaction_date) = 5 THEN 'Thursday'
        WHEN DAYOFWEEK(transaction_date) = 6 THEN 'Friday'
        WHEN DAYOFWEEK(transaction_date) = 7 THEN 'Saturday'
        ELSE 'Sunday'
        END;

![CS 13](https://github.com/user-attachments/assets/40502297-10c6-483d-9f01-578ce8fba809)        

TO GET SALES FOR ALL HOURS FOR MONTH OF MAY

    SELECT 
    HOUR(transaction_time) AS Hour_of_Day,
    ROUND(SUM(unit_price * transaction_qty)) AS Total_Sales
    FROM 
    [Coffee Shop Sales]
    WHERE 
    MONTH(transaction_date) = 5 -- Filter for May (month number 5)
    GROUP BY 
    HOUR(transaction_time)
    ORDER BY 
    HOUR(transaction_time);


![CS 14](https://github.com/user-attachments/assets/1a9768f7-4c6c-4677-84c2-bad005dacbd2)




### Part 2: Data Visualisation Through Power BI

- Step 1: Open Power BI Desktop and select Import Data from Excel in GET DATA tab. Select the Load option and the entire data will be loaded in the Power BI. In the Table view option, analyse the number of rows loaded in the Power BI to cross check whether entire data is imported. 

- Step 2: Open Power query editor & in view tab under Data preview section, check "column distribution", "column quality" & "column profile" options. check whether entire table is populated correctly and the data types are matched accordingly to the column heads.

- Step 3: Now, to calculate the Time-Intelligence Functions, we need to add a Date Table. Following DAX expression was used for calculating the Date Table: 

      Date Table = CALENDAR(MIN(Transactions[transaction_date]), MAX(Transactions[transaction_date]))
The Calendar Table uses dynamic start and end dates to optimize the storage space. Extract the Month, Month Number Columns from the Calendar by using the following DAX Functions: 

      Month = FORMAT('Date Table'[Date].[Date], "mmm")
      Month Number = MONTH('Date Table'[Date]
      Month Year = FORMAT('Date Table'[Date], "mmm yyyy")

- Step 4: Now, in the Model View of the Power BI Desktop, create a relationship between newly built Date Table remember and the transactions table via ONE-TO-MANY Cardinality Relationship on transaction_date and set the cross-filter direction as SINGLE. 


![CS15](https://github.com/user-attachments/assets/50375ee6-2a0d-4d99-88e5-af184694918c)

- Step 5 : In the Report View of the Power BI Desktop, select the background theme. You can either select an image or in this case use a background color by clicking on the Format Page option in the Visualization Tab. Note: reduce the transparency to 0%. For changing the Canvas size, select Canvas Settings and choose the desired option from the drop down menu.

- Step 6 : Now, select the required Text Box for the Title of Dashboard by selecting the Shape option from Insert Tab. 
Format the background color(in this case I selected #03801A color) and shape accordingly. 

- Step 7 : Now, for the first KPI requirement i.e. Sales Analysis - we will calculate a new column named Sales for calculating the sales value of each transaction using DAX as follows: 

      Sales = Transactions[unit_price] * Transactions[transaction_qty]

For calculating the Total Sales, a new measure named "Total Sales" is added using the below DAX function: 

     Total Sales = SUM(Transactions[Sales])

However, if we wish to calculate the Total Sales w.r.t. each month, we nedd to add  slicer with month year in the data field. To display the slicer result in a sequential order, sort the column in the Home ribbon by month number. Thus, the 1st KPI requirement of Total sales for each month is visualised through a Card Visual which is then formatted in accordance to the theme and colors involved. 


- Step 8: Now, for the change in Sales over the months is visualised through a Line Chart. Note: Do remember to create date hierarchy on the Date field for viaulising the Line Chart appropriately. 
Select the Date hierarchy on X- axis and Total Sales as a component on Y- axis. 

- Step 9: The next step in visualizing the Current Month Sales, Previous Month Sales and the difference in Sales b/w the Current Month and Previous Month is determined using the DAX formulas:

      CM Sales = VAR selected_month = SELECTEDVALUE('Date Table'[Month])
      RETURN
      TOTALMTD(CALCULATE([Total Sales], 'Date Table'[Month] = selected_month), 'Date Table'[Date])

      PM Sales = CALCULATE([CM Sales], DATEADD('Date Table'[Date], -1, MONTH)) 

Now, for calculating the Month-on-Month Growth along with difference in Sales is calculated as follows: 

      MoM Growth & Diff Sales = 

      VAR month_diff = [CM Sales] - [PM Sales]
      VAR mom = ([CM Sales] - [PM Sales])/[PM Sales]
      VAR _sign = IF(month_diff > 0, "+", "")
      VAR _sign_trend = 
      var positive_icon = UNICHAR(9650)
         var negative_icon = UNICHAR(9660)
         var result = IF(month_diff > 0, positive_icon, negative_icon)
         return result
          
      RETURN
      _sign_trend & " " & _sign & FORMAT(mom, "#0.0%" & " | " & _sign & FORMAT(month_diff/1000, "0.0K")) & " " & "vs LM"

Note: Do Group the different elements of the Sales KPI banner together by sleecting the individual elements and choosing the GROUP option.
      The resultant Sales KPI banner with all the elemnets for the May month will be visualized as below: 


![CS 15](https://github.com/user-attachments/assets/ce968c1b-760c-4167-b968-9dad13e5667c)

- Step 10 : The other 2 KPI banners namely - Total Orders and Total Quantity are also visualized using the above steps 7-9, and making necessary changes wherever required accordingly. 
The resultant 3 KPI banners will look as follows - 


![CS 16](https://github.com/user-attachments/assets/f6cf0cbf-2b13-439f-980b-226521edff77)

- Step 11: In order to visualize a Calendar table, we will incorporate a matrix chart and add certain new columns to build the Calendar Table. 

      Week Number = WEEKNUM('Date Table'[Date], 2)
      Day Number = FORMAT('Date Table'[Date], "D")
      Weekday Number = WEEKDAY('Date Table'[Date], 2)

The Matrix table is then formatted in accordance with the background theme and colors. The Dates individual background color is set in accordance to the Total Sales for that particular date(dark color symboloizing higher sales and lighter corresponding to low sales).

The Tool Tip to display the Total Sales, Total Orders and Total Quantity Sold is inserted by visualizing a Tool Tip Chart in a new page(Toggle ON the Allow use As A Tool Tip in the Page Information option). Insert Cards to display the aformentioned measures, donut chart, and Mom increase/decrease measure)

 Following is the screenshot of the final Calendar Table and the Tool Tip designed - 


![CS 17](https://github.com/user-attachments/assets/bc176c4c-5e6b-41b5-b24b-8a821ca029d8)


- Step 12: Now, in order to analyse the next Problem statement of analysing the sales proportion of the Coffee Shop w.r.t. Weekdays and Weekends, first, we need to segment the dates into the respective categories through the following DAX query: 

      Weekday/Weekend = IF('Date Table'[Day Name] = "Sat" || 'Date Table'[Day Name] = "Sun", "Weekend", "Weekday")
    
Now, we will a Donut Chart to visualize the data and format the chart in accordance to the background theme, font, and size.     

![CS 18](https://github.com/user-attachments/assets/11018379-4141-4e75-b60e-0447c046ef4a)


- Step 13: For the next part of the Sales Dashboard, we used a clustered bar chart to analyse the Sales by 3 different store location and month-on-month sales increase/decrease w.r.t. previous month of that certain location. A series of changes was made to make the dashboard look more interactive and informative. 


![CS 19](https://github.com/user-attachments/assets/729d1dfa-e5b8-4006-8ad0-c7323d940005)

- Step 14: The Sales Trend over the period is analysed using a Stacked Bar Chart with transaction date on X axis and Total Sales on Y axis. Additionally, an average line is added to signify which dats in a certain month performed above average and which were below average in terms of Total Sales. The Bar colors were also formatted accordingly. A DAX expression to bifurcate the Total Sales into 2 categories was used: 
        
        Color for Bars = IF([Total Sales]>[Daily Average Sales], "Above Average", "Below Average")

The snapshot of Sales Trend over the period will be as follows: 

![CS 20](https://github.com/user-attachments/assets/98f20c26-9e88-48fe-aeb1-8c0d60a577d3)

- Step 15: Another Bar Chart was also added to viualise the Sales figure by Product Category. For this, certain changes were made to the data labels to prvide information about the bars of the chart. 
Following DAX calculations were used - 

      Label for Product Category = SELECTEDVALUE(Transactions[product_category]) & " | " & FORMAT([Total Sales]/1000, "$0.00K")

      New MoM Label For Product Category = 

      VAR month_diff = [CM Sales] - [PM Sales]
      VAR mom = ([CM Sales] - [PM Sales])/[PM Sales]
      VAR _sign = IF(month_diff > 0, "+", "")
      VAR _sign_trend = 
      var positive_icon = UNICHAR(9650)
         var negative_icon = UNICHAR(9660)
         var result = IF(month_diff > 0, positive_icon, negative_icon)
         return result
      RETURN
      _sign_trend & " " & _sign & FORMAT(mom, "#0.0%")



![CS 21](https://github.com/user-attachments/assets/3ab58108-35f7-4b89-aab9-029a4fa7c72c)

- Step 16: Using the above Step 15, we will design a bar chart with Top 10 products by Sales figure. 

![CS 22](https://github.com/user-attachments/assets/f3e56b6d-77c3-4637-b474-31ad594c9043)

- Step 17: For the last chart of the dashboard, we need to visualise a Heat Map to analyse Sales w.r.t. Days and hours for the entire 6 months alomg with a Tool Tip to display the metrics associated with it. For this, we will first extract the hour field from transaction_time column by using the DAX expression - 

      Hour = HOUR(Transactions[transaction_time])

Now, a matrix chart is added, where the Total Sales, Hour and day Number fields are entered into respective fields. The chart is formatted correctly(cell elements are color coded according to Total Sales). For the tool tip, we will create a new card visual, with the Total Sales, Orders, Quantity, and mentioning the date and time of transaction. To project the Total Sales by Hours and Days values interactively, we will add correspondingly a bar chart and column chart with the values. 
The final Heat Map will be represented as follows: 

![CS 23](https://github.com/user-attachments/assets/c1827d29-9b99-42da-ac84-d5b056fd3682)


 - Step 18: The report was then published to Power BI Service.
 
 
 # Report Snapshot (Power BI DESKTOP)

![CS Final Dashboard](https://github.com/user-attachments/assets/a9585cff-5966-41e9-b194-c9ed66b03459)


# Insights

A single page report was created on Power BI Desktop & it was then published to Power BI Service.

Following inferences can be drawn from the dashboard;

### [1] Insights regarding Sales, Orders, and Quantity sold.

    a) The cumulative Total Sales of the Coffee Shop from 1 Jan 2023 to 30 June 2023 has increased by 102%(from $82K to 166K).

    b) The Total number of Orders has also seen a rapid increase of 104%(from Jan to June). 

    c) The Total Quantity Sold also increased by 104%(from #24870 in Jan to #50942 in June).
   

#### Thus, we can analyse that all 3 KPIs i.e. Total Sales, Total Quantity sold and Total Orders have seen an equivalent increase of 104% in this 6 month period. 
           
### [2] The Hell's Kitchen store was the best performing store among all 3 locations throughout the period. 

    a) The Sales for the Hell's Kitchen location increased from $27.82K in Jan 2023 to $56.96K in June 2023 while Astoria and Lower Manhattan Sales increase were a bit low. 

    b) The Total Sales and Total Quantity sold were also a bit higher than the other 2 locations. 

#### Thus, we can analyse that Hell's Kitchen is a good location for the Coffee Shop company in terms of all 3 KPIs. The company can launch some marketing activities to increase the foot fall in other 2 locations. 
  
  ### [3] Sales by Days|Hours.  
  
    a) The cumulative Total Sales for the Coffee Shop was highest within the time slot of 8 am to 11 am during all the months.

    b) The least performing time slot of the day was from 8 pm to 9 pm dusring the said time period. 

#### Thus, we can analyse that maximum sales were contributed during the morning hours(from 8 am to 11 am) while least performance was during the closing hour of 8 pm to 9 pm. Thus, the company can launch some offers during the least performing hour like offering a certain percentage of discount on their products to increase traffic. 

 ### [4] Some other insights: 
 
 ### Barista Espresso was the Top selling product during the aformentioned time period,forming approximately 12% - 13% of Total Sales. This was followed by Brewed Chai, Hot Chocolate and Gourmet Coffee. The Top performing product list is as follows: 

    a) Barista Espresso
    b) Brewed Chai
    c) Hot  Chocolate
    d) Gourmet Coffee
    e) Brewed Black Tea

 ### Top Products by Product Category:

    a) Coffee 
    b) Tea
    c) Bakery
    d) drinking Chocolate
    e) Coffee Beans

    

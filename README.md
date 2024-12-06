# Will this order be delivered on time? 
#### Jupyter Notebook with detailed analysis [here](update.this.com)

## Business Goal
In today's interconnected, globalized economy with supply chains and shipping routes spanning across the globe, the ability to accurately deliver an order at the promised time is paramount to customer satisfaction. This is tracked by a metric called On Time Delivery (OTD). Ability to predict OTD misses and take appropriate corrective actions would be beneficial to brand image, customer satisfaction and in turn, higher customer retentions and more orders, which would increase sales and profits!

For this specific analysis, We will be exploring how 'Days of Shipping(real)' and 'Days of Shipping(Scheduled)' are related and create a model to predict if the gap between the two would be zero or not? if zero, it is an on-time delivery.

This could be approached in two  ways - 
1. Treat Days of shipping(real) as a real value and apply regression techniques to predict the scheduled Days of shipping value to be as close to the real one as possible
2. Consider the difference between the the Real and Scheduled Days of shipping and treat this a a bianry problem and predict Yes/No for met/not-not met sceanrios and approach this as a Classification problem
   
For the purposes of this analysis, we will take the second path and model this as a yes/no question that would provide insights to the operations /logistcs team if there is a lurking danger of missing OTD on a particualr order (Even before the shipping process has started) and enable them to take corrective actions. We will only consider Late deliveries as 'missed' and treat early deliveries as 'met'. 

In subsequent iterations, we could add additional analysis and models to predict 'Early' scenarios as well as come up with a prediction model to predict expected shipping times for orders.

## Actionable Insights from the Analysis
Based on analysis so far - 
1. The scheduled number of days has the maximum impact on on-time delivery of an order (Indicates that if underlying rule engine for this predictio becoems better, otd will improve as expected)
2. The day on which the order is palced and the shipment is made has maximm impact on-time delivery.
>> - When predicted days are <4, there is a very high chance of not meeting OTD. These needs to be reviewed and addressed first as these could also be orders where the customer has paid extra for quicker shipping
>> -  January and December seem to have more misses. This could be due to shipping crunch as a results of holidays. Time to ship and mode of shipment should be adjusted to alleiviate this
>> -  When shipped or ordered on the last day of the month, there is a larger chance of missed OTD
>> -  First and Second Class shipment modes are not working and well and results in large number of misses. Same Day is better, but still misses ~50% of times.

## Data
This data is courtsey of <> and I offer my sincere thanks to the team for making this data available in the public domain. It has ~180K samples with 50 features 
#### Contents: 
| FIELDS | DESCRIPTION |
|---|---|
| Type | Type of transaction made |
| Days for shipping (real) | Actual shipping days of the purchased product |
| Days for shipment (scheduled) | Days of scheduled delivery of the purchased product|
| Benefit per order | Earnings per order placed |
| Sales per customer | Total sales per customer made per customer |
| Delivery Status | Delivery status of orders: Advance shipping, Late delivery, Shipping canceled, Shipping on time |
| Late_delivery_risk | Categorical variable that indicates if sending is late (1), it is not late (0) |
| Category Id | Product category code |
| Category Name | Description of the product category |
| Customer City | City where the customer made the purchase |
| Customer Country | Country where the customer made the purchase |
| Customer Email | Customer's email |
| Customer Fname | Customer name | 
| Customer Id | Customer ID |
| Customer Lname | Customer lastname |
| Customer Password | Masked customer key |
| Customer Segment | Types of Customers: Consumer, Corporate, Home Office |
| Customer State | State to which the store where the purchase is registered belongs |
| Customer Street | Street to which the store where the purchase is registered belongs |
| Customer Zipcode | Customer Zipcode |
| Department Id | Department code of store |
| Department Name | Department name of store |
| Latitude | Latitude corresponding to location of store |
| Longitude | Longitude corresponding to location of store |
| Market | Market to where the order is delivered: Africa, Europe, LATAM, Pacific Asia, USCA |
| Order City | Destination city of the order |
| Order Country | Destination country of the order |
| Order Customer Id | Customer order code |
| order date (DateOrders) | Date on which the order is made |
| Order Id | Order code |
| Order Item Cardprod Id | Product code generated through the RFID reader |
| Order Item Discount | Order item discount value |
| Order Item Discount Rate | Order item discount percentage |
| Order Item Id | Order item code |
| Order Item Product Price | Price of products without discount |
| Order Item Profit Ratio | Order Item Profit Ratio |
| Order Item Quantity | Number of products per order |
| Sales | Value in sales |
| Order Item Total | Total amount per order |
| Order Profit Per Order | Order Profit Per Order |
| Order Region | Region of the world where the order is delivered: Southeast Asia, South Asia, Oceania, Eastern Asia, West Asia, West of USA, US Center, West Africa, Central Africa, North Africa, Western Europe, Northern, Caribbean, South America, East Africa, Southern Europe, East of USA, Canada, Southern Africa, Central Asia, Europe, Central America, Eastern Europe, South of USA |
| Order State | State of the region where the order is delivered |
| Order Status | Order Status: COMPLETE, PENDING, CLOSED, PENDING_PAYMENT, CANCELED, PROCESSING, SUSPECTED_FRAUD, ON_HOLD, PAYMENT_REVIEW |
| Product Card Id | Product code |
| Product Category Id | Product category code |
| Product Description | Product Description |
| Product Image | Link of visit and purchase of the product |
| Product Name | Product Name |
| Product Price | Product Price |
| Product Status | Status of the product stock: If it is 1 not available, 0 the product is available |
| Shipping date (DateOrders) | Exact date and time of shipment |
| Shipping Mode | The following shipping modes are presented: Standard Class, First Class, Second Class, Same Day |

The data was of good quality with very few missing records
(Data details - initial table)

The data ha ~50/50 split for our purposes
(Link to the OTD gaps graph)

The following adjustments were done to improve the quality:
1. Remove Features / Samples
>> - Order Id, Order Customer Id, Order Item Id : Key
>> - Category Name : Co-rrelated to Category Id
>> - Customer Email, Customer Fname, Customer Lname, Customer Password : Co-rrelated to Customer Id
>> - Department Name : Co-rrelated to Department Id
>> - Product Image, Product Name, Product Description: Co-rrelated to Product Card Id
>> - Order Zip code has a large amount of null. This would be removed
>> - There are three records with Customer zip as Null. Remove those rows as well
2. Feature Engineering
>> - shipping date (DateOrders), order date (DateOrders). We will split this to epoch, day of week, week of year, month and year

With the base adjustment, the features looked as follows:
> ### Categorical Features
 (Categorical columns details)
> ### Numerical Features
(numerical Features spread)
> ### Correlation between Features  
(Correlation matrix - initial)

After evaluating these, the following actions were taken  
>> - 'product status' feature was always 0. Remove it
>> - Order Dates and Shipping dates have very high correlation. Further, the month and weeks have high correlation as well. Hence, dropping those. Further, Order with 'Shipping Cancelled' status should be removed to not skew the shipping results
>> - 'Category Id', 'Department Id', 'Product Category Id', 'Product Card Id' and 'Order Item Cardprod Id' have high positive correlation. To reduce computation, will only keep the 'Department Id' and drop the others
>> - 'Order Item Total' has very high correlation with 'Product Price' and 'Sales per customer'. Only 'Product Price Benefit per order' will be kept
>> - 'Order Profit Per Order' and 'Order Item Profit Ratio' are highly correlated. Will keep only the latter
>> - 'Order Item Product Price' and 'Product Price' are highly correlated. Keeping only the latter here as well
>> - 'Order Item Quantity' and 'Product price' have a very high inverse relationship. Will keep 'Order Item Quantity'
>> - 'Order Item Discount' and 'Order Item Discount Rate' have high correlation. Only keeping the latter
>> - 'Late Delivery Risk' and 'Gap' have high correlation, and it looks like it is captured based on actual shipping data. Hence, removing it
>> - 'Customer Zip Code' and 'longitude/latitude' have a very high correlation. Removing the former

After these adjustements, a good set of independent features were identified
(Correlation matrix - final)

## Modeling
As the dataset is fairly large, we will run the intial modeling against 50% of the dataset (for computational efficiencies) and then do the final set of training and optimization with the full dataset

As the final goal is to ensure that the model is able to predict OTD-NotMet cases effectively, we will focus on Recall of the model

GridSearchCV was used to evaluate the performance of various combinations of AUC/ROC metric to identify a good model from
1. KNeighborsClassifier
2. DecisionTreeClassifier
3. LogisticRegression
4. SVC (Support Vector Machine based classifier)
   
The data was scaled using standard scaler and encoded using one hot encoder. Further, care was taken to ensure the slight imbalance of data was properly managed by the models by setting the appropriate hyperparameters.

Initial analysis
(Model performance table)

### Best Model Chosen
>> Model Name: SVC - Tuned for Recall
>> Params: {'classifier__C': 10, 'classifier__degree': 3, 'classifier__gamma': 'scale', 'classifier__kernel': 'poly'}
**>> Train Recall: 0.999970**
>> Train AUC-ROC: 0.999971
>> Train Precision: 0.999970
**>> Test Recall : 0.986779**
>> Test AUC-ROC: 0.983971
>> Test Precision: 0.986779


## Next Steps
1. Analyse this data using and Ensemble model
2. Productionize the model



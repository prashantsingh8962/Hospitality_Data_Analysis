# Step-by-Step Workflow Guide:

1. Problem statements: AtliQ Grands, a chain of five-star hotels in India, has been experiencing a decline in market share and revenue in the luxury/business hotel category due to competitive strategies and ineffective decision-making. To regain their position, the managing director aims to incorporate "Business and Data Intelligence" but lacks an in-house data analytics team. Therefore, the revenue management team has decided to hire a third-party service provider to extract insights from their historical data.


2. We have metadata to understand the datasets:
    This file contains all the meta information regarding the columns described in the CSV files. we have provided 5 CSV files:
    1. dim_date(date, mmm yy, week no, day_type)
    2. dim_hotels(property_id, property_name, category, city)
    3. dim_rooms(room_id, room_class)
    4. fact_aggregated_bookings(property_id, check_in_date, room_category, successful_bookings, capacity)
    5. fact_bookings(booking_id, property_id, booking_date, check_in_date, check_out_date, no_guests, room_category, booking_platform, ratings_given, booking_status, revenue_generated, revenue_realized)

3. We start with discussing our mockup with the requirements of the Product Manager in the meeting.

   <img src="https://github.com/prashantsingh8962/Hospitality_Data_Analysis/blob/main/Resources/mock%20up%20dashboard_atliq%20grands.png" class="center">

4. Product Manager Recommendations:
      - RevPar(Revenue Per Available Room) = total revenue divided by total rooms available to sell. or ADR * Occupancy.
      - ADR(Average Daily Rate)
      - Occupancy%= total rooms occupied/ total rooms available to sell.
      - SRN(sellable rooms nights): Eg- if a hotel have all 100 hundred rooms to sell in June (30 days). its SRN is 3000(100*30).
      - DSRN(Daily sellable room nights): SRN/total no days = 3000/30 = 100.
      - realization: URN/BRN.
      - URN(utilized room nights):
      - BRN(Booked room nights): URN + no shows + cancellations                 (no shows means they booked but arrived.)
      - In this industry, weekends are Fridays  and Saturdays.
      - whether a hotel is business or leisure.
      - For taking decisions, he need data movements Like trends chart etc.
      - Level 1, level 2 means going deep down to find the cause of problem.(ask why? to your dashboard to go deep down)

5. Start with data transformation using power query and data modeling:
      - we input the whole folder "input" in the power query editor. We opened all five datasets by copy the input file and click on binary in front of desired table name.
      - In dim_rooms, we remove the first header line to make it proper.
      -  we also remove weekday column in dim_dates bcoz in this industry, weekends are fridays and Saturdays, we make it later. last, close and apply.
      -  In power query, we data modelling connect dim tables to fact table using 1-to-many relation.
  
          <img src="https://github.com/prashantsingh8962/Hospitality_Data_Analysis/blob/main/Resources/Data%20Modelling.png" class="center">
 
        

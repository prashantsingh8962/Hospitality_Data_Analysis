# Step-by-Step Workflow Guide:

1. Problem statements: AtliQ Grands, a chain of five-star hotels in India, has been experiencing a decline in market share and revenue in the luxury/business hotel category due to competitive strategies and ineffective decision-making. To regain their position, the managing director aims to incorporate "Business and Data Intelligence" but lacks an in-house data analytics team. Therefore, the revenue management team has decided to hire a third-party service provider to extract insights from their historical data.


2. We have metadata to understand the datasets:
    This file contains all the meta information regarding the columns described in the CSV files. we have provided 5 CSV files:
    1. dim_date(date, mmm yy, week no, day_type)
    2. dim_hotels(property_id, property_name, category, city)
    3. dim_rooms(room_id, room_class)
    4. fact_aggregated_bookings(property_id, check_in_date, room_category, successful_bookings, capacity)
    5. fact_bookings(booking_id, property_id, booking_date, check_in_date, check_out_date, no_guests, room_category, booking_platform, ratings_given, booking_status, revenue_generated, revenue_realized)

3. We start with discussing our mockup with the requirements of the Product Manager Mr.Hero.

   <img src="https://github.com/prashantsingh8962/Hospitality_Data_Analysis/blob/main/Resources/mock%20up%20dashboard_atliq%20grands.png" class="center">

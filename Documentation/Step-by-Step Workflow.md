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
 
 6. Building Metrics Using DAX: (Data Analysis Expression)
       - We created two calculated column named wn and date_type
   
             - wn = WEEKNUM(Dim_date[date])
             - Date_type = 
             var wfd = WEEKDAY(Dim_date[date])
             return 
             if(wfd > 5,"Weekend","Weekday")

       - We created the following measures(26):

             1. Revenue = SUM(fact_bookings[revenue_realized])
         
             2. Total Bookings = COUNT(fact_bookings[booking_id])
         
             3. Total Capacity = SUM(fact_aggregated_bookings[capacity])
         
             4. Total Succesful Bookings = SUM(fact_aggregated_bookings[successful_bookings])
         
             5. Occupancy % = DIVIDE([Total Successful Bookings],[Total Capacity],0)
         
             6. Average Rating = AVERAGE(fact_bookings[ratings_given])
         
             7. No of days = DATEDIFF(MIN(dim_date[date]),MAX(dim_date[date]),DAY) +1
         
             8. Total cancelled bookings = CALCULATE([Total Bookings],fact_bookings[booking_status]="Cancelled")
         
             9. Cancellation % = DIVIDE([Total cancelled bookings],[Total Bookings])
         
             10. Total Checked Out = CALCULATE([Total Bookings],fact_bookings[booking_status]="Checked Out")
         
             11. Total no show bookings = CALCULATE([Total Bookings],fact_bookings[booking_status]="No Show")
         
             12. No Show rate % = DIVIDE([Total no show bookings],[Total Bookings])
         
             13. Booking % by Platform = DIVIDE([Total Bookings],
                 CALCULATE([Total Bookings], 
                 ALL(fact_bookings[booking_platform])
                  ))*100
         
             14. Booking % by Room class = DIVIDE([Total Bookings],
                 CALCULATE([Total Bookings], 
                 ALL(dim_rooms[room_class])
                 ))*100
         
             15. ADR = DIVIDE( [Revenue], [Total Bookings],0)
         
             16. Realisation % = 1- ([Cancellation %]+[No Show rate %])
         
             17. RevPAR = DIVIDE([Revenue],[Total Capacity])
         
             18. DBRN = DIVIDE([Total Bookings], [No of days])
         
             19. DSRN = DIVIDE([Total Capacity], [No of days])
         
             20. DURN = DIVIDE([Total Checked Out],[No of days])

             21. Revenue WoW change % = 
                 Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
                 var revcw = CALCULATE([Revenue],dim_date[wn]= selv)
                 var revpw =  CALCULATE([Revenue],FILTER(ALL(dim_date),dim_date[wn]= selv-1))
                  return
                 DIVIDE(revcw,revpw,0)-1"

             22. Occupancy WoW change % = 
                 Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
                 var revcw = CALCULATE([Occupancy %],dim_date[wn]= selv)
                 var revpw =  CALCULATE([Occupancy %],FILTER(ALL(dim_date),dim_date[wn]= selv-1))
                 return
                 DIVIDE(revcw,revpw,0)-1"

             23. ADR WoW change % = 
                 Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
                 var revcw = CALCULATE([ADR],dim_date[wn]= selv)
                 var revpw =  CALCULATE([ADR],FILTER(ALL(dim_date),dim_date[wn]= selv-1))
                 return
                 DIVIDE(revcw,revpw,0)-1"


             24.  Revpar WoW change % = 
                 Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
                 var revcw = CALCULATE([RevPAR],dim_date[wn]= selv)
                 var revpw =  CALCULATE([RevPAR],FILTER(ALL(dim_date),dim_date[wn]= selv-1))
                 return
                 DIVIDE(revcw,revpw,0)-1"
   
         
             25. Realisation WoW change % = 
                 Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
                 var revcw = CALCULATE([Realisation %],dim_date[wn]= selv)
                 var revpw =  CALCULATE([Realisation %],FILTER(ALL(dim_date),dim_date[wn]= selv-1))
                 return
                 DIVIDE(revcw,revpw,0)-1"


             26.  DSRN WoW change % = 
                 Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
                 var revcw = CALCULATE([DSRN],dim_date[wn]= selv)
                 var revpw =  CALCULATE([DSRN],FILTER(ALL(dim_date),dim_date[wn]= selv-1))
                  return
                 DIVIDE(revcw,revpw,0)-1"


 ## Some Useful Terminologies in Hospitality Industry:
1	Revenue	To get the total revenue_realized <br>
2	Total Bookings	To get the total number of bookings happened <br>
3	Total Capacity	To get the total capacity of rooms present in hotels<br>
4	Total Succesful Bookings	To get the total succesful bookings happened for all hotels<br>
5	Occupancy %	"Occupancy means total successful bookings happened to the total rooms available(capacity)"<br>
6	Average Rating	Get the average ratings given by the customers<br>
7	No of days	"To get the total number of days present in the data. In our case, we have data from May to July. So 92 days."<br>
8	Total cancelled bookings	To get the"Cancelled" bookings out of all Total bookings happened<br>
9	Cancellation %	"calculating the cancellaton percentage."<br>
10	Total Checked Out	To get the successful 'Checked out' bookings out of all Total bookings happened<br>
11	Total no show bookings	"To get the""No Show"" bookings out of all Total bookings happened (""No show"" means those customers who neither cancelled nor attend to their booked rooms)"<br>
12	No Show rate %	calculating the no show percentage.<br>
13	Booking % by Platform	"To show the percentage contribution of each booking platform for bookings in hotels. We have booking platforms like makeyourtrip, logtrip, tripster etc)"<br>
14	Booking % by Room class	"To show the percentage contribution of each room class over total rooms booked. We have room classes like Standard, Elite, Premium, Presidential."<br>
15	ADR 	"Calculate the ADR(Average Daily rate) It is the ratio of revenue to the total rooms booked/sold. It is the measure of the average paid for rooms sold in a given time period"<br>
16	Realisation %	"calculate  the realisation percentage. It is nothing but the succesful ""checked out"" percentage over all bookings happened."<br>
17	RevPAR	"Calculate the RevPAR(Revenue Per Available Room) RevPAR represents the revenue generated per available room, whether or not they are occupied. RevPAR helps hotels measure their revenue generating performance to accurately price rooms. RevPAR can help hotels measure themselves against other properties or brands."<br>
18	DBRN	"calculate DBRN(Daily Booked Room Nights) This metrics tells on average how many rooms are booked for a day considering a time period"<br>
19	DSRN 	"calculate DSRN(Daily Sellable Room Nights) This metrics tells on average how many rooms are ready to sell for a day considering a time period"<br>
20	DURN	"calculate DURN(Daily Utilized Room Nights) This metric tells on average how many rooms are succesfully utilized by customers for a day considering a time period"<br>
21	Revenue WoW change %	"To get the revenue change percentage week over week.Here, revcw  for current week , revpw for previous week"<br>
22	Occupancy WoW change %	"To get the occupancy change percentage week over week. Here, revcw  for current week , revpw for previous week"<br>
23	ADR WoW change %	"To get the ADR(Average Daily rate) change percentage week over week. Here, revcw  for current week , revpw for previous week"<br>
24	Revpar WoW change %	"To get the RevPar(Revenue Per Available Room) change percentage week over week. Here, revcw  for current week , revpw for previous week"<br>
25	Realisation WoW change %	"To get the Realisation change percentage week over week. Here, revcw  for current week , revpw for previous week"<br>
26	DSRN WoW change %	"To get the DSRN(Daily Sellable Room Nights) change percentage week over week. Here, revcw  for current week , revpw for previous week"<br>

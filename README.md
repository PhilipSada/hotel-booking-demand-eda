# Hotel Booking Demand EDA
## Project Overview
This project uses python together with libraries such as Pandas, Matplotlib and Seaborn to conduct exploratory data analysis on a hotel booking demand dataset which can be found in Kaggle. 

## Exploratory Data Analysis
Questions answered during the exploratory data analysis:
1. Which country do most of the guests come from?
2. How long do guests stay in the hotels?
3. Which market segment has higher bookings?
4. How many bookings were cancelled?
5. Which month has the highest number of cancellations?

The full python code can be in found in this repository ('EDA_Hotel_Booking_Demand.ipynb').

### Which country do most of the guests come from?
```python
#Getting the records where the booking is not canceled
guest_country = data[data['is_canceled']==0]['country'].value_counts().reset_index()
#Renaming the columns
guest_country.columns=['country','Number of guests']

total_guests = guest_country['Number of guests'].sum()
guest_country['Guests in %'] = round(guest_country['Number of guests'] / total_guests * 100, 2)

#Plotting the bar chart
trace = go.Bar(
    x=guest_country['country'],
    y=guest_country['Guests in %'],
    #y=guest_country['Number of guests'],
    marker=dict(color='#5D3FD3')
)
data1 = [trace]
layout = go.Layout(
    title='Guests by Country'
)
fig = go.Figure(data=data1, layout=layout)
pyo.plot(fig)

#Geographical plot

map_guest = px.choropleth(
    guest_country,
    locations=guest_country['country'],
    #color=guest_country['Number of guests'],
    color=guest_country['Guests in %'],
    hover_name=guest_country['country'],
    title="Home Country of Guests"
)

map_guest.show()

#Drawing a conclusion 
number_of_guests_in_pgf= guest_country['Number of guests'].iloc[:3].sum()


percentage_of_guests_from_pgf = number_of_guests_in_pgf / total_guests * 100

print(number_of_guests_in_pgf)
print(percentage_of_guests_from_pgf)
```

![hotel_booking_demand_visual](https://github.com/PhilipSada/hotel-booking-demand-eda/assets/55988995/7e76ebba-05d5-4636-b176-745ebc72666d)
![hotel_booking_demand_visual_3](https://github.com/PhilipSada/hotel-booking-demand-eda/assets/55988995/150555a0-4f9a-495e-86f8-d9c2ccf11b84)



**Observation**: 52% of the guests who stay in the two hotels (Resort and City Hotel) are from Portugal, Great Britain and France

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

**The full python code can be in found in this repository ('EDA_Hotel_Booking_Demand.ipynb').**

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

### How long do guests stay in the hotels?
```python
df3=data[data['is_canceled']==0]
df3['total_nights'] = df3['stays_in_weekend_nights'] + df3['stays_in_week_nights']

df4=df3[['total_nights','hotel','is_canceled']]
hotel_stay=df4.groupby(['total_nights','hotel']).agg('count').reset_index()

#renaming the is_canceled column to number of stays
hotel_stay=hotel_stay.rename(columns={'is_canceled':'Number of stays'})
hotel_stay.head()

hotel_stay_r=hotel_stay[hotel_stay['hotel']=='Resort Hotel']
hotel_stay_r

hotel_stay_c=hotel_stay[hotel_stay['hotel']=='City Hotel']
hotel_stay_c

#Plotting the bar chart
trace = go.Bar(
    x=hotel_stay_r['total_nights'],
    y=hotel_stay_r['Number of stays'],
    marker=dict(color='#5D3FD3'),
    name='Resort Stay'
)
trace1 = go.Bar(
    x=hotel_stay_c['total_nights'],
    y=hotel_stay_c['Number of stays'],
    marker=dict(color='#CBC3E3'),
    name='City Stay'
)
data4 = [trace, trace1]
layout = go.Layout(
    title='Total Number of stays by Guest'
)
fig = go.Figure(data=data4, layout=layout)
pyo.plot(fig)
```
![hotel_booking_demand_visual_1](https://github.com/PhilipSada/hotel-booking-demand-eda/assets/55988995/d831dc46-0370-454f-9528-43a240267444)

**Observation:** In the case of city hotels, guests tend to prefer staying for 1-4 nights. While, for resort hotels, stays of 1-4 nights are common, but 7-night stays are notably popular as well.

### Which market segment has higher bookings?
```python
segments=data['market_segment'].value_counts()
segments

#plotting the pie chart
fig=px.pie(segments,
          values=segments.values,
          names=segments.index,
          title='Bookings per market segment',
          template='seaborn')

fig.update_traces(rotation=-90, textinfo='percent+label')
fig.show()
```
![hotel_booking_demand_visual_4](https://github.com/PhilipSada/hotel-booking-demand-eda/assets/55988995/fcc9faa3-7cdd-4563-a2ee-a9521ebf6351)

**Observation:** Online TA has the most bookings

### How many bookings were cancelled?
```python
  Cancel=data['is_canceled']==1
  cancel=Cancel.sum()
resort_cancellation=data.loc[data['hotel']=='Resort Hotel']['is_canceled'].sum()
city_cancellation=data.loc[data['hotel']=='City Hotel']['is_canceled'].sum()

print(f'Total Booking Cancelled: {cancel} .')
print(f'Total Resort Hotel Booking Cancelled: {resort_cancellation} .')
print(f'Total City Hotel Booking Cancelled: {city_cancellation} .'

```
**Result**
Total Booking Cancelled: 44199 .
Total Resort Hotel Booking Cancelled: 11120 .
Total City Hotel Booking Cancelled: 33079 .

### Which month has the highest number of cancellations?
```python
res_book_per_month=data.loc[(data['hotel']=='Resort Hotel')].groupby('arrival_date_month')['hotel'].count()
res_cancel_per_month=data.loc[(data['hotel']=='Resort Hotel')].groupby('arrival_date_month')['is_canceled'].count()

cty_book_per_month=data.loc[(data['hotel']=='City Hotel')].groupby('arrival_date_month')['hotel'].count()
cty_cancel_per_month=data.loc[(data['hotel']=='City Hotel')].groupby('arrival_date_month')['is_canceled'].count()

res_cancel_data= pd.DataFrame({'Hotel':'Resort Hotel',
                              'Month':list(res_book_per_month.index),
                              'Bookings':list(res_book_per_month.values),
                              'Cancellations':list(res_cancel_per_month.values)})


cty_cancel_data= pd.DataFrame({'Hotel':'City Hotel',
                              'Month':list(cty_book_per_month.index),
                              'Bookings':list(cty_book_per_month.values),
                              'Cancellations':list(cty_cancel_per_month.values)})


full_cancel_data = pd.concat([res_cancel_data, cty_cancel_data], ignore_index=True)

import sort_dataframeby_monthorweek as sd
res_cancel_data=sd.Sort_Dataframeby_Month(df=res_cancel_data, monthcolumnname='Month')
res_cancel_data

cty_cancel_data=sd.Sort_Dataframeby_Month(df=cty_cancel_data, monthcolumnname='Month')
cty_cancel_data

#Plotting barchart
plt.figure(figsize=(12,8))

trace=go.Bar(
    x=res_cancel_data['Month'],
    y=res_cancel_data['Cancellations'],
    name='Resort Booking Cancelled'

)

trace1=go.Bar(
    x=cty_cancel_data['Month'],
    y=cty_cancel_data['Cancellations'],
    name='City Booking Cancelled'

)

data5=[trace, trace1]
layout=go.Layout(
    title='Total Number of stays by Guest'
)

fig=go.Figure(data=data5, layout=layout)
pyo.plot(fig)
```

![hotel_booking_demand_visual_2](https://github.com/PhilipSada/hotel-booking-demand-eda/assets/55988995/6b657cd2-718e-4f37-9c43-88193e044a14)

**Observation:** Based on the chart, in City and Resort Hotels, the highest booking cancellations occur in August

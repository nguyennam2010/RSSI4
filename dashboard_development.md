# Dashboard Development

After [pushing MongoDB data to Dashboard](https://github.com/nguyennam2010/RSSIIY/blob/main/README.md#c-query-data-from-databases-to-wise-paas), this is how you develop the Dashboard.

## Method
Two main methods in Dashboard development: Direct (MongoDB - Dashboard) and indirect visualization (MongoDB - DataHub - SaaS Composer - Dashboard).

<img width="444" alt="13" src="https://github.com/nguyennam2010/RSSI4/assets/102983698/cfe86215-77f5-428d-b7b3-deb3cd3fdef2">


## Variables

Create variables for AP and floor.

![01](https://github.com/nguyennam2010/RSSI4/assets/102983698/327dab18-82d9-40c0-84e0-0922224657e0)



## AP information

- Query:

```
db.Controller_4.aggregate([
{"$sort":{"ts":-1, "Client":-1}},
{"$match": {"AP Name":"$ap", "ts":{ "$gte" : "$from"}}}, 
{"$project": {"_id":0,"AP Name":"$AP Name", "Client":"$Client","IP Address":"$IP address","MAC Address":"$MAC address","Model":"$Model","AP Group":"$AP_Group", "TW Time":"$DatetimeStr"}},
{"$limit":1}
            ])
```
Note: In this query, ```Controller_4``` is the collection of the AP database, which named ```Aruba_AP```. The query is same as you query data in your MongoDB. 

- Result:

![02](https://github.com/nguyennam2010/RSSI4/assets/102983698/8e718968-ab5e-487c-884d-a5fe8c7f9f98)



## Client number

- Query (2.4 GHz):

```
db.AP_List.aggregate([
{"$match": {"ap_name":"$ap","radio_band": 0, "ts":{ "$gte" : "$from", "$lt":"$to"}}}, 
{"$project": {"_id":0, "name":"2.4 GHz", "ap_name":"$ap", "value":"$sta_count","ts":"$ts"}}
            ])
```
Note: You need to Add Query to query 5GHz, then it can show in the map together as the pic below.

- Result:

![03](https://github.com/nguyennam2010/RSSI4/assets/102983698/93753f6b-1d9c-40bb-b679-eda13aa8d452)



## Noise floor
- Query (5 GHz):

```
db.AP_List.aggregate([
{"$match": {"ap_name":"IY_1F_AP01","radio_band":1,  "ts":{ "$gte" : "$from", "$lt":"$to"}}}, 
{"$project": {"_id":0,"name":"5 GHz", "value":"$noise_floor","ts":"$ts"}}
            ])
```

- Result:

![04](https://github.com/nguyennam2010/RSSI4/assets/102983698/6af37e1b-8f62-4716-82d2-2a6b06a946de)



## Channel utilization
- Query (2.4 GHz - tx time):

```
db.AP_List.aggregate([
{"$match": {"ap_name":"$ap","radio_band": 0, "ts":{ "$gte" : "$from", "$lt":"$to"}}}, 
{"$project": {"_id":0, "name":"tx time", "value":"$tx_time","ts":"$ts"}}
            ])
```
- Result:

![05](https://github.com/nguyennam2010/RSSI4/assets/102983698/d49e5bd0-eac8-48ae-8ef5-34f1481f6ab5)



## Client information

- Query:

```
db.Controller_4.aggregate([
{"$sort":{"ts":-1, "Client":-1}},
{"$match": {"ap_name":"$ap", "ts":{ "$gte" : "$from", "$lt":"$to"}}}, 
{"$project": {"_id":0,"Client User Name":"$client_user_name", "MAC Address":"$sta_mac_address","IP Address":"$client_ip_address","OS":"$client_dev_type","WLAN":"$ssid","AP Name":"$ap","Client Health":"$client_health", "Radio Band":"$radio_band","Channel":"$channel","SNR":"$snr","TW Time":"$DatetimeStr"}},
{"$limit":100}
])
```

- Result:

![06](https://github.com/nguyennam2010/RSSI4/assets/102983698/c629616a-1acc-4f84-976d-c3f5f7ec70ce)



## Client map

To visualize as map in Dashboard, you need to push your data to DataHub.

First, you need to download SDK code from [WISE-PaaS documentation](https://docs.wise-paas.advantech.com/en/Guides_and_API_References/Data_Acquisition/DataHub/py_Edge_SDK_Manual/v1.1.7)

Their code for random data generation:

![7](https://github.com/nguyennam2010/RSSI4/assets/102983698/a17040a7-63c1-4b82-9312-ca50a29a030d)


You need to adjust the above code to connect to DataHub. For example, push AP name and Client number from MongoDB:

![08](https://github.com/nguyennam2010/RSSI4/assets/102983698/9330f8da-46bb-40c5-9df2-6fdc8463869e)



Then you can push the data to DataHub follow this video: https://www.youtube.com/watch?v=IP46vcWuHhY

Now you edit Composer to bind the data to the map. Create appropriate assets and bind the corresponding data from DataHub:

![09](https://github.com/nguyennam2010/RSSI4/assets/102983698/8f9e2fd8-af80-4518-9336-5756203800df)



Use advanced setting to change color of the Client nodes:

![010](https://github.com/nguyennam2010/RSSI4/assets/102983698/542fcdc4-5d7c-47c8-994a-b38e2135f055)



```
function(value, oldValue, option){ 
    if(value>=10) return "symbols/builtIn/large transformer/red light.json";
    if(value<10 & value>2) return "symbols/builtIn/large transformer/yellow light.json";
    if(value<=2) {value=0;return "symbols/builtIn/large transformer/green light.json";}
    else return "symbols/builtIn/large transformer/green light.json";}
```

- Now the map can show number of clients and color corresponding to the data.
    - Green light: < 2 users (clients)
    - Yellow light: 2 < users < 10
    - Red light: > 10 users

In Dashboard, create a panel, choose SaaS Composer-Viewer and choose the SaaS Composer directory to the file setting:

![011](https://github.com/nguyennam2010/RSSI4/assets/102983698/9bfd0e2d-1a0d-4952-bbba-feb4339550d9)


## Interference map

Same as Client map, you import the IY building map to SaaS Composer and bind their (x, y) variables.

![012](https://github.com/nguyennam2010/RSSI4/assets/102983698/4d7e3f4b-342d-402c-a10d-eeae7e4c2946)


In Dashboard, create a panel for Interference Map and choose the directory same as Client Map.










# Sprint 01

## Signs with Smart Connectivity for Better Road Safety

## Team ID - PNT2022TMID35873

### Sprint Goal(s) :
1. Initialization of Various APIs reuqired for the Project

### Program Code :

#### [> weatherdata.py](./weatherdata.py)
-> Fetches weather report from OpenWeatherMap API Selectively
```python
# Python code

import requests
# from time import sleep

#Function Definition
def get(myLocation,APIKEY):
    apiURL = f"https://api.openweathermap.org/data/2.5/weather?q={myLocation}&appid={APIKEY}"
    responseJSON = (requests.get(apiURL)).json()
    returnObject = {
        "temperature" : responseJSON['main']['temp'] - 273.15,
        "weather" : [responseJSON['weather'][_]['main'].lower() for _ in range(len(responseJSON['weather']))],
        "visibility" : responseJSON['visibility']/100, # visibility in percentage where 10km is 100% and 0km is 0%
    }
    # print(responseJSON)
    if("rain" in responseJSON):
        returnObject["rain"] = [responseJSON["rain"][key] for key in responseJSON["rain"]]
    return(returnObject)
    
# #Testing
# while True:
#     print(get("Chennai","c132fedc6afa3e7ee042e29298f34013"))
#     sleep(5)
```

#### [> algo.py](./algo.py)
-> Implementation of Code Flow
```python
# Python code
import weatherdata
from datetime import datetime 
# from time import sleep
def mainfunc(myLocation,APIKEY,area):
    weatherData = weatherdata.get(myLocation,APIKEY)

    finalSpeed = area["usualSpeedLimit"] if "rain" not in weatherData else area["usualSpeedLimit"]/2
    finalSpeed = finalSpeed if weatherData["visibility"]>35 else finalSpeed/2

    if(area["hospitalZone"]):      # hospital zone
        noHonk = True
    else:
        if(area["schools"]["schoolZone"]==False):          # neither school nor hospital zone
            noHonk = False
        else:            # school zone
            now = [datetime.now().hour,datetime.now().minute]
            activeTime = [list(map(int,_.split(":"))) for _ in area["schools"]["activeTime"]]
            noHonk = activeTime[0][0]<=now[0]<=activeTime[1][0] and activeTime[0][1]<=now[1]<=activeTime[1][1]

    return({"speed" : finalSpeed,"noHonk" : noHonk})

# area = {
#     "schools" : {"schoolZone" : True,"activeTime" : ["7:00","17:30"]},
#     "hospitalZone" : False,
#     "usualSpeedLimit" : 40 # in km/hr
# }

# while True:
#     print(mainfunc("Chennai","c132fedc6afa3e7ee042e29298f34013",area))
#     sleep(5)
```

#### [> main.py](./main.py)
-> Microcontroller Code
```python
# Python code
#Main Microcontroller Code
from time import sleep
import algo

loc = "Chennai"
APIkey= "c132fedc6afa3e7ee042e29298f34013"
area = {
    "schools" : {"schoolZone" : True,"activeTime" : ["7:00","17:30"]},
    "hospitalZone" : False,
    "usualSpeedLimit" : 40 # in km/hr
}

while True:
    out=algo.mainfunc(loc,APIkey,area)
    print(out)
    sleep(5)

```

### Output :
```python
# Code Output
{'speed': 20, 'doNotHonk': False}
```

### Images :
![OutputImg](./outputImg.png)

### Completion of Sprint 1

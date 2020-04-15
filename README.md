# MMM-Vasttrafik-PublicTransport
[Magic Mirror](https://magicmirror.builders/) Module - Display public transport operated by Västtrafik for western part of Sweden, including Gothenburg. This module use the API's provided by [Västtrafik](https://www.vasttrafik.se).

## Prerequisites
### 1. Create application and obtain required client id and secret
You need to obtain your own application key and secret from Västtrafik by creating a new application.

1. Navigate to [https://developer.vasttrafik.se](https://developer.vasttrafik.se) and create your own developer account.
2. Create new application under [https://developer.vasttrafik.se/portal/#/applications](https://developer.vasttrafik.se/portal/#/applications)

### 2. Create application subscription 
You need to obtain application subscription for [Reseplaneraren v2](https://developer.vasttrafik.se/portal/#/api/Reseplaneraren/v2/landerss/direct)

1. Navigate to [https://developer.vasttrafik.se/portal/#/subscriptions](https://developer.vasttrafik.se/portal/#/subscriptions)
2. Create new subscription for your newly created application so that we can queary Reseplaneraren v2.

#### 2.1 Traffic situtaions (optional)
If you would like to get traffic situations for your stops, you also need to subscribe to [TrafficSituations v1](https://developer.vasttrafik.se/portal/#/api/TrafficSituations/v1/admin) and set ```trafficSituations``` in configs to true.

1. Navigate to [https://developer.vasttrafik.se/portal/#/subscriptions](https://developer.vasttrafik.se/portal/#/subscriptions)
2. Create new subscription for your application so that we can queary TrafficSituations v1.


### 3. Get stops that you want to track
Last thing todo is to find the required stop id:s that you would like to track. 

1. Navigate to the API consol for [Reseplaneraren v2](https://developer.vasttrafik.se/portal/#/api/Reseplaneraren/v2/landerss)
2. Use any of the location endpoints to obatin the stop id. 

Exampel request url ([access token] could be retrived from the developer portal - API console directly, https://developer.vasttrafik.se/portal/#/api/Reseplaneraren/v2/landerss, or by  following this guide: https://developer.vasttrafik.se/portal/#/guides/oauth2):
```
GET https://api.vasttrafik.se/bin/rest.exe/v2/location.name?input=centralstationen HTTP/1.1
Authorization: Bearer [access_token]
```
Response:
```
<?xml version="1.0" encoding="UTF-8"?>
<LocationList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="http://api.vasttrafik.se/v1/hafasRestLocation.xsd" servertime="20:36" serverdate="2018-09-16">
<StopLocation name="Centralstationen, Göteborg" lon="11.973740" lat="57.707898" id="9021014001950000" idx="1"/>
...
</LocationList>
```
## Install
1. Clone repository into ``../modules/`` inside your MagicMirror folder.
2. Run ``npm install`` inside ``../modules/MMM-Vasttrafik-PublicTransport/`` folder
3. Add the module to the MagicMirror config

## Update
1. Run ``git pull`` inside ``../modules/MMM-Vasttrafik-PublicTransport/`` folder.
2. Run ``npm install`` inside ``../modules/MMM-Vasttrafik-PublicTransport/`` folder

## Configuration
```
modules: [
    ...
    {
            module: "MMM-Vasttrafik-PublicTransport",
            position: "bottom_left",
            header: "Västtrafik",
            config: {
                     stopIds: ["your_stop_ids"],   // REQUIRED. An array of stop is's. Your are required to have at least one stop.
                                                   // see 3. Get stops that you want to track.
                     appKey: "your_app_key",       // REQUIRED. see 1. Create application and obtain required client id and secret.
                     appSecret: "your_app_secret", // REQUIRED. see 1. Create application and obtain required client id and secret.
                     debug: false,                 // Optional. Enable some extra output when debugging.
                     sortBy: "track",               // Optional. Sort your departure board by either "track", "direction", "line" or "type"
                                                   // default is "track".
                     refreshRate: "20000",          // Optional. Refresh rate int milliseconds, default is 60 seconds.
                     trafficSituations: true,      // Optional. Default is false, you need a subscription to TrafficSituations v1 API please see Prerequisites 2.1
                     board: {
                        destination: {
                            maxPxWidth: 150       // Optional. Force max width for destination names.
                        }, 
                    },
                    showTrackNumbers: false,     //Optional. Default is true, if set to false will hide the track column.
                    showStopHeader: false,       //Optional. Default is true, if set to false will hide the stop name header.
                    showDestinationName: false   //Optional. Default is true, if set to false will hide the direction/stop column. 
                    filterAttr: "track",           //Optional. Default is null, if set 'filterKey' also needs to be set. Allowed value: "track", "direction", "line" or "type"
                    filterKey: "A"             //Optional. Default is null, if set 'filterAttr' also needs to be set. Filter key is any value of the filtered attribute, see filtered board. 
            }
        },
    ...
]
```
### Filtered board
If you want to filter the departure board you could do that by setting configs for "filterAttr" and "filterKey". Both settings needs to be set if used. Default is null. You cant filter on multiple keys, so for example you cannot filter on track "A,B" but only on "B" or "A".  

#### Example A - filter on Track:
So in order to filter the board on Track A. You could do that by simply setting "filterAttr" to "track" and "filterKey" to "A". This will remove all other tracks from the board. The same goes for "direction", "line" or "type".

```
config: {
                    .....
                    filterAttr: "track",
                    filterKey: "A"
                    ....
        }
```

![Example of filter on track](https://github.com/bureus/MMM-Vasttrafik-PublicTransport/blob/master/docs/filterOnTrack.PNG)

#### Example B - filter on Line:
So in order to filter the board on Line 4. Change config settings "filterAttr" to "line" and "filterKey" to "4". This will remove all other lines from the board.

```
config: {
                    .....
                    filterAttr: "line",
                    filterKey: "4"
                    ....
        }
```

![Example of filter on track](https://github.com/bureus/MMM-Vasttrafik-PublicTransport/blob/master/docs/filterOnLine.PNG)

### Minimal layout
If you are after minimal width or design, you should set showTrackNumbers, showDestinationName and showStopHeader to false. This will render the board like this:

![Example of minimal board](https://github.com/bureus/MMM-Vasttrafik-PublicTransport/blob/master/docs/extendedConfigs.PNG)

## Supports multi stops and translations
You can add a list of stop ids if you want to track multiple stops. Current supported languages are Swedish (sv) and English (en) and set in MagicMirror configurations.

![Example of swedish translation and tracking two stops](https://github.com/bureus/MMM-Vasttrafik-PublicTransport/blob/master/docs/swedishAndMultistops.PNG)

## Get traffic situations for your stops
You can get ongoing traffic situations or planned maintenance (swedish only) for your stops by setting traffic situtation flag to true in configs. Please keep in mind that you need to subscribe to TrafficSituations v1 API. See Prerequisites 2.1.

![Example of traffic situations banner](https://github.com/bureus/MMM-Vasttrafik-PublicTransport/blob/master/docs/trafficSituations.gif)

## Screenshot

![Västtrafik PublicTransport Module](https://github.com/bureus/MMM-Vasttrafik-PublicTransport/blob/master/docs/screenshot.PNG)
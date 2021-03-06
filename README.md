![](https://raw.githubusercontent.com/CZWeatherKit/CZWeatherKit/master/Meta/czweatherkit_header.png)

[![Build Status](https://travis-ci.org/CZWeatherKit/CZWeatherKit.svg?branch=master)](https://travis-ci.org/CZWeatherKit/CZWeatherKit)
![License](http://img.shields.io/badge/license-BSD-red.svg)


CZWeatherKit is a simple, extensible weather library for iOS and OS X that allows for easy downloading of weather data from various weather services. CZWeatherKit is extremely lightweight and requires no external dependencies. 

CZWeatherKit started as way to simplify weather API requests for [Sol°](https://github.com/comyarzaheri/Sol), a simple open-source iOS weather app ([App Store Link](http://appstore.com/Sol°)). The README will be updated when CZWeatherKit has been integrated back into Sol° so you can see what it looks like in a completed app.

# Getting Started

### Cocoa Pods

Add the following to your Podfile:

```ruby
    platform :ios, '7.0'
    pod "CZWeatherKit"
```

and run `pod install`. If you're not using Cocoa Pods, add the `CZWeatherKit` directory to your project (but you should probably consider using Cocoa Pods).

### Requirements

CZWeatherKit has only been tested on iOS 7 and OS X 10.9, but the library avoids using newer Cocoa/Cocoa Touch API so it may work on older deployment targets. 

### Supported Weather Services

CZWeatherKit currently supports the following weather services:
  * [Weather Underground](http://www.wunderground.com/weather/api/)
  * [Open Weather Map](http://openweathermap.org/API)

Some weather services require an API key while others do not. Consult the documentation for the weather API you would like to use. Additional services can be added (somewhat) easily by adopting the `CZWeatherService` protocol. See the 'Adding New Services' section of the README.

### Climacons

`CZWeatherCondition` objects have a `climaconCharacter` property that contains an appropriate climacon character mapping for the weather condition description. The [Climacons Font](http://adamwhitcroft.com/climacons/font/) is a font set created by [Adam Whitcroft](http://adamwhitcroft.com/) featuring various weather-related icons. In order to use the `climaconCharacter` property, download the Climacons font and add it to your project. The following example illustrates how to use the character in a `UILabel`:

```objective-c
    ...

    CZWeatherCondition *condition = ... // Pretend we have this!

    UILabel *label = [[UILabel alloc]initWithFrame:<your_frame>];
    label.font = [UIFont fontWithName:CLIMACON_FONT size:<your_font_size>];
    label.text = [NSString stringWithFormat:@"%c", condition.climaconCharacter];
    
    ...
    
```

You can also check out the iOS example project to see a few more examples.


# Examples

## Wunderground

### Getting Current Conditions

```objective-c 
    CZWeatherRequest *request = [CZWeatherRequest requestWithType:CZCurrentConditionsRequestType];
    request.location[CZWeatherKitLocationName.StateCityName] = @"TX/Austin";
    request.service = [CZWundergroundService serviceWithKey:<API_KEY_HERE>];
    [request performRequestWithHandler:^(id data, NSError *error) {
        if (data) {
            CZWeatherCondition *current = (CZWeatherCondition *)data;
            // Do whatever you like with the data here
        }
    }];
```

### Getting Forecast

```objective-c 
    const CGFloat latitude  = 30.2500;
    const CGFloat longitude = -97.7500;
    CZWeatherRequest *request = [CZWeatherRequest requestWithType:CZForecastRequestType];
    request.location[CZWeatherKitLocationName.CoordinateName] = [NSValue valueWithCGPoint:CGPointMake(latitude, longitude)];
    request.service = [CZWundergroundService serviceWithKey:<API_KEY_HERE>];
    [request performRequestWithHandler:^(id data, NSError *error) {
        if (data) {
            NSArray *forecasts = (NSArray *)data;
            // Do whatever you like with the data here
        }
    }];
```

### Getting 10-day Forecast

```objective-c 
    CZWeatherRequest *request = [CZWeatherRequest requestWithType:CZForecastRequestType];
    request.detailLevel = CZWeatherRequestFullDetail;
    request.location[CZWeatherKitLocationName.CountryCityName] = @"Australia/Sydney";
    request.service = [CZWundergroundService serviceWithKey:<API_KEY_HERE>];
    [request performRequestWithHandler:^(id data, NSError *error) {
        if (data) {
            NSArray *forecasts = (NSArray *)data;
            // Do whatever you like with the data here
        }
    }];
```


## Open Weather Map 

### Getting Current Conditions

```objective-c 
    const CGFloat latitude  = 30.2500;
    const CGFloat longitude = -97.7500;
    CZWeatherRequest *request = [CZWeatherRequest requestWithType:CZCurrentConditionsRequestType];
    request.location[CZWeatherKitLocationName.CoordinateName] = [NSValue valueWithCGPoint:CGPointMake(latitude, longitude)];
    request.service = [CZOpenWeatherMapService serviceWithKey:<API_KEY_HERE>];
    [request performRequestWithHandler:^(id data, NSError *error) {
        if (data) {
            CZWeatherCondition *current = (CZWeatherCondition *)data;
            // Do whatever you like with the data here
        }
    }];
```

### Getting Hourly Forecast

```objective-c
    CZWeatherRequest *request = [CZWeatherRequest requestWithType:CZForecastRequestType];
    request.location[CZWeatherKitLocationName.CountryCityName] = @"London,UK";
    request.service = [CZOpenWeatherMapService serviceWithKey:<API_KEY_HERE>];
    [request performRequestWithHandler:^(id data, NSError *error) {
        if (data) {
            NSArray *forecasts = (NSArray *)data;
            // Do whatever you like with the data here
        }
    }];
```

### Getting Daily Forecast

```objective-c
    CZWeatherRequest *request = [CZWeatherRequest requestWithType:CZForecastRequestType];
    request.location[CZWeatherKitLocationName.StateCityName] = @"Austin,TX";
    request.service = [CZOpenWeatherMapService serviceWithKey:<API_KEY_HERE>];
    request.detailLevel = CZWeatherRequestFullDetail;
    [request performRequestWithHandler:^(id data, NSError *error) {
        if (data) {
            NSArray *forecasts = (NSArray *)data;
            // Do whatever you like with the data here
        }
    }];
```

# Architecture

### Classes and Protocols

| Classes                        | Description
|--------------------------------|:---------------
|`CZWeatherRequest`              | Handles requests to weather service API's. 
|`CZWeatherCondition`            | Represents the weather conditions at a specific moment in time.
|`CZWundergroundService`         | Service class for interacting with the Weather Underground API.
|`CZOpenWeatherMapService`       | Service class for interacting with the Open Weather Map API.

| Protocols                      | Description
|--------------------------------|:---------------
|`CZWeatherService`              | Declares an interface for weather service objects to implement
    
### Creating Requests    
    
`CZWeatherRequest` objects only have a few properties, but their values can vary widely depending on the weather service being used. For example,
if you want to get the current weather conditions in Sydney, Australia, you have to set the value for the key `CZWeatherKitLocationName.CountryCityName`
in the `location` dictionary. Weather services expect the value for the country and city name in varying formats. Wunderground, for example, expects the
country and city name in the following format: `<Country>/<City>`. Open Weather Map, however, expects the country and city name in the following format: 
`<City>,<Country>`. When setting the location for a request, consult the API reference for the weather service you are using.

Additionally, weather services differ in the variety of locations they support. For example, Wunderground allows you to query by City/State, City/Country, Zipcode, Latitude/Longitude, and IP address. Open Weather Map only allows you to query by City/State, City/Country, Latitude/Longitude. When performing requests
to a service, ensure that the query type is supported.

Finally, requests carry with them a detail level. A detail level loosely defines how much information you wish to retrieve for the request, but the meaning can vary for each service. For example, when requesting forecast data from Wunderground, a detail level of `CZWeatherRequestLightDetail` will retrieve a 3-day forecast and `CZWeatherRequestFullDetail` will retrieve a 10-day forecast. When requesting forecast data from Open Weather Map, a detail level of `CZWeatherRequestLightDetail` will retrieve an hourly forecast and `CZWeatherRequestFullDetail` will retrieve a daily forecast.

### Adding New Services

Services can be added somewhat painlessly to CZWeatherKit. To be a weather service, a class should adopt the `CZWeatherService` protocol. Weather service objects separate URL generation/response parsing from performing actual requests. This allows new weather services to be added without any changes to the rest of the CZWeatherKit's API. If you would like to contribute to this project by adding new weather services, please take a look at both `CZWundergroundService` and `CZOpenWeatherMapService`.

# Testing

The unit tests for the project aren't all-encompassing and are somewhat lacking. If you would like to add unit tests, that would be appreciated. 

The project is also being tested on the continuous integration service [Travis CI](http://travis-ci.org). You can check the build history [here](https://travis-ci.org/CZWeatherKit/CZWeatherKit).

# Contributing and Terms

If you would like to contribute to this project, please try to follow the coding style of the rest of the project. I have a somewhat particular (Ok, very particular...) coding style and would like new features to match. Also, I would appreciate if you add unit tests for anything you add (especially new services!).
Last but no least, these are the general terms:

* The project is under the BSD license, so uh take note of that, I guess.
* I will keep the project open-source and on GitHub as long as it's relevant (like, if 10 years from now Cocoa is dead then I'll probably take it down)
* If this project results in your production app exploding users' phones, sorry I'm not liable :( . Also, please post an issue if it does.
* If you make contributions, thanks! If that leads to any issues with licensing or whatever, just let me know and we'll (hopefully) work it out. 
* If you have any issues or suggestions for the general architecture of CZWeatherKit, I'd love to hear them! Please post an issue.

### Contributers

Comyar Zaheri, [@comyarzaheri](https://github.com/comyarzaheri)



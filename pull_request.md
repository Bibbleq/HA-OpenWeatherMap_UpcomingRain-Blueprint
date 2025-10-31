# Fix service name from weather_provider to openweathermap

Fixes the error: can't access property get_minute_forecast, t.services[e] is undefined

## Changes:
- Changed service call from weather_provider.get_minute_forecast to openweathermap.get_minute_forecast (line 83)
- Updated blueprint title to specify OpenWeatherMap
- Added integration: openweathermap to weather entity selector for better UX

The generic service name weather_provider.get_minute_forecast doesn't exist in Home Assistant. The correct service for OpenWeatherMap integration is openweathermap.get_minute_forecast.
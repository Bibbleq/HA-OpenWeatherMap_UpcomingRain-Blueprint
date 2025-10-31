# HA-OpenWeatherMap_UpcomingRain-Blueprint

A Home Assistant Blueprint that monitors minute-by-minute weather forecasts and updates helper entities with rain predictions.

## Features

- 🌧️ **Rain Detection**: Identifies if rain is expected within the next hour
- ⏱️ **Time to Rain**: Calculates minutes until rain starts
- 📊 **Precipitation Rate**: Tracks maximum mm/hr expected
- 🌡️ **Intensity Classification**: Categorizes rain as None, Light, Moderate, Heavy, or Violent

## Requirements

### Weather Provider
You need a weather integration that supports minute-level forecasts, such as:
- **OpenWeatherMap** (with OneCall API)
- Other providers with `weather_provider.get_minute_forecast` support

### Helper Entities
Before using this blueprint, create the following helper entities in Home Assistant.

**Quick Setup**: See [`example_helpers.yaml`](example_helpers.yaml) for ready-to-use configurations.

1. **Input Boolean**: Rain within hour indicator
   - Go to Settings → Devices & Services → Helpers
   - Add Helper → Toggle
   - Name: `Rain Within Hour` (or similar)
   - Entity ID example: `input_boolean.rain_within_hour`

2. **Input Number**: Minutes until rain
   - Add Helper → Number
   - Name: `Minutes Until Rain`
   - Minimum: 0
   - Maximum: 60
   - Step: 1
   - Unit: minutes
   - Entity ID example: `input_number.minutes_until_rain`

3. **Input Number**: Maximum mm per hour
   - Add Helper → Number
   - Name: `Max mm Within Hour`
   - Minimum: 0
   - Maximum: 200
   - Step: 0.1
   - Unit: mm
   - Entity ID example: `input_number.max_mm_within_hour`

4. **Input Select**: Rain intensity
   - Add Helper → Dropdown
   - Name: `Rain Intensity`
   - Options (must include exactly these values):
     - `None`
     - `Light`
     - `Moderate`
     - `Heavy`
     - `Violent`
   - Entity ID example: `input_select.rain_intensity`

## Installation

### Option 1: Direct Import (Easiest)

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FBibbleq%2FHA-OpenWeatherMap_UpcomingRain-Blueprint%2Fblob%2Fmain%2Frain_forecast_to_helpers.yaml)

### Option 2: Manual Installation

1. Copy the contents of `rain_forecast_to_helpers.yaml`
2. In Home Assistant, go to:
   - Settings → Automations & Scenes → Blueprints
   - Click "Import Blueprint"
   - Paste the URL: `https://github.com/Bibbleq/HA-OpenWeatherMap_UpcomingRain-Blueprint/blob/main/rain_forecast_to_helpers.yaml`

### Option 3: File System

1. Download `rain_forecast_to_helpers.yaml`
2. Place it in your Home Assistant `config/blueprints/automation/` directory
3. Create subdirectory if needed: `config/blueprints/automation/rain_forecast/`
4. Restart Home Assistant or reload automations

## Configuration

After importing the blueprint, create a new automation:

1. Go to Settings → Automations & Scenes
2. Click "Create Automation"
3. Select "Rain Within Hour (Weather Forecast → Helpers)"
4. Configure the following inputs:

   - **Weather Provider Entity**: Select your weather entity (e.g., `weather.home`)
   - **Poll Interval**: How often to check (default: 10 minutes)
   - **Rain Threshold**: Minimum mm/min to consider as rain (default: 0.05)
   - **Input Boolean - Rain Within Hour**: Select your toggle helper
   - **Input Number - Minutes Until Rain**: Select your minutes helper
   - **Input Number - Max mm/hr Within Hour**: Select your max precipitation helper
   - **Input Select - Rain Intensity**: Select your intensity dropdown helper

5. Save the automation

## Usage Examples

### Notification Automation

```yaml
alias: Notify when rain is coming
trigger:
  - platform: state
    entity_id: input_boolean.rain_within_hour
    to: "on"
action:
  - service: notify.mobile_app
    data:
      title: "Rain Alert"
      message: >
        Rain expected in {{ states('input_number.minutes_until_rain') }} minutes.
        Intensity: {{ states('input_select.rain_intensity') }}
        Max rate: {{ states('input_number.max_mm_within_hour') }} mm/hr
```

### Close Windows Automation

```yaml
alias: Close windows when rain is coming
trigger:
  - platform: state
    entity_id: input_boolean.rain_within_hour
    to: "on"
condition:
  - condition: numeric_state
    entity_id: input_number.minutes_until_rain
    below: 5
action:
  - service: cover.close_cover
    target:
      entity_id: cover.bedroom_window
```

### Dashboard Card Example

```yaml
type: entities
title: Rain Forecast
entities:
  - entity: input_boolean.rain_within_hour
    name: Rain Expected
  - entity: input_number.minutes_until_rain
    name: Minutes Until Rain
  - entity: input_number.max_mm_within_hour
    name: Max Precipitation
  - entity: input_select.rain_intensity
    name: Intensity
```

## Rain Intensity Classifications

The blueprint categorizes rain intensity based on precipitation rate:

| Intensity | mm/hr Range | Description |
|-----------|-------------|-------------|
| None      | < 0.1       | No rain |
| Light     | 0.1 - 2.5   | Light drizzle |
| Moderate  | 2.5 - 10    | Steady rain |
| Heavy     | 10 - 50     | Heavy rain |
| Violent   | > 50        | Extreme rainfall |

## Troubleshooting

### Blueprint not updating helpers

- Verify your weather entity supports `weather_provider.get_minute_forecast`
- Check the automation trace in Home Assistant for errors
- Ensure helper entities exist and are not disabled

### "None" values in helpers

- The weather provider may not be returning forecast data
- Check if your weather integration is properly configured
- Verify API key and location settings

### Wrong entity_id in forecast response

- The blueprint expects the response structure from `get_minute_forecast`
- Check Developer Tools → Templates to test: `{{ Minute_Weather }}`

## Contributing

Feel free to open issues or submit pull requests to improve this blueprint.

## License

MIT License - Feel free to use and modify as needed.

## Credits

Created for the Home Assistant community to make rain forecasting more accessible and actionable.
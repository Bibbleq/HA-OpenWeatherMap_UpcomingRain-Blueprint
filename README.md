# OpenWeatherMap Upcoming Rain Blueprint for Home Assistant

A Home Assistant blueprint that monitors OpenWeatherMap minute forecast data to detect rain within the next hour. It automatically updates helper entities with rain predictions including timing, intensity, and precipitation amounts.

## Features

- 🌧️ Detects rain within the next 60 minutes
- ⏰ Calculates minutes until rain starts
- 💧 Tracks maximum precipitation rate (mm/hr)
- 📊 Classifies rain intensity (Light, Moderate, Heavy, Violent)
- ⚙️ Fully configurable via Home Assistant UI
- 🔄 Automatic updates at your chosen interval

## Requirements

### 1. OpenWeatherMap Integration
You need the OpenWeatherMap integration configured with minute forecast support:
- Set up the [OpenWeatherMap integration](https://www.home-assistant.io/integrations/openweathermap/) in Home Assistant
- Ensure your API key supports minute forecast (available with One Call API 3.0)

### 2. Helper Entities
Create the following helper entities before using this blueprint:

#### Input Boolean
- **Name**: Rain Within Hour
- **Purpose**: Indicates if rain is expected within the hour
- Create via: Settings → Devices & Services → Helpers → Create Helper → Toggle

#### Input Numbers (create 2)
1. **Name**: Minutes Until Rain
   - **Minimum**: 0
   - **Maximum**: 60
   - **Step**: 1
   
2. **Name**: Max MM Within Hour
   - **Minimum**: 0
   - **Maximum**: 200
   - **Step**: 0.1

Create via: Settings → Devices & Services → Helpers → Create Helper → Number

#### Input Select
- **Name**: Rain Intensity
- **Options**: `None`, `Light`, `Moderate`, `Heavy`, `Violent`
- Create via: Settings → Devices & Services → Helpers → Create Helper → Dropdown

## Installation

### Method 1: Import via URL (Recommended)
1. In Home Assistant, go to **Settings** → **Automations & Scenes** → **Blueprints**
2. Click the **Import Blueprint** button (bottom right)
3. Paste this URL:
   ```
   https://github.com/Bibbleq/HA-OpenWeatherMap_UpcomingRain-Blueprint/blob/main/upcoming_rain_forecast.yaml
   ```
4. Click **Preview** → **Import Blueprint**

### Method 2: Manual Installation
1. Download `upcoming_rain_forecast.yaml` from this repository
2. Copy the file to your Home Assistant `config/blueprints/automation/` directory
3. Restart Home Assistant or reload automations

## Configuration

1. Go to **Settings** → **Automations & Scenes** → **Create Automation** → **Start with a Blueprint**
2. Select **"Rain Within Hour (OpenWeatherMap Minute Forecast)"**
3. Configure the inputs:
   - **Weather Provider Entity**: Select your OpenWeatherMap weather entity
   - **Rain Within Hour (Boolean Helper)**: Select your input_boolean
   - **Minutes Until Rain (Number Helper)**: Select your first input_number
   - **Max MM Within Hour (Number Helper)**: Select your second input_number
   - **Rain Intensity (Select Helper)**: Select your input_select
   - **Rain Threshold**: Minimum precipitation to consider as rain (default: 0.05 mm/min)

4. Click **Save** and give your automation a name

**Note**: The automation checks for rain updates every 10 minutes automatically.

## Usage

Once configured, the automation will:
1. Check the minute forecast at your specified interval
2. Analyze the next 60 minutes of precipitation data
3. Update the helper entities with:
   - Whether rain is expected (boolean)
   - How many minutes until it starts (number)
   - Maximum precipitation rate in mm/hr (number)
   - Rain intensity level (select)

### Example Dashboard Card

You can display the rain forecast in your dashboard:

```yaml
type: entities
title: Rain Forecast
entities:
  - entity: input_boolean.rain_within_hour
    name: Rain Expected
  - entity: input_number.minutes_until_rain
    name: Minutes Until Rain
  - entity: input_number.max_mm_within_hour
    name: Max Precipitation (mm/hr)
  - entity: input_select.rain_intensity
    name: Rain Intensity
```

### Use in Automations

You can trigger other automations based on these helpers:

```yaml
automation:
  - alias: Close Windows When Rain Detected
    trigger:
      - platform: state
        entity_id: input_boolean.rain_within_hour
        to: 'on'
    condition:
      - condition: numeric_state
        entity_id: input_number.minutes_until_rain
        below: 15
    action:
      - service: cover.close_cover
        target:
          entity_id: cover.bedroom_window
```

## Rain Intensity Classifications

- **None**: No rain or below threshold
- **Light**: < 2.5 mm/hr
- **Moderate**: 2.5 - 10 mm/hr
- **Heavy**: 10 - 50 mm/hr
- **Violent**: > 50 mm/hr

## Troubleshooting

### No Data / Blueprint Not Working
- Verify your OpenWeatherMap integration is working and shows minute forecast data
- Check that your API key supports One Call API 3.0
- Ensure all helper entities are created correctly
- Check Home Assistant logs for any errors

### Incorrect Entity IDs
- The weather entity key in the forecast response should match your entity ID
- If you encounter issues, check the automation's trace to see the data structure

### False Positives/Negatives
- Adjust the **Rain Threshold** parameter (default: 0.05 mm/min)
- Lower values = more sensitive to light precipitation
- Higher values = only detect heavier rain

## Contributing

Feel free to open issues or submit pull requests to improve this blueprint!

## License

This blueprint is provided as-is for use with Home Assistant. Feel free to modify and share.

## Credits

Created for the Home Assistant community to make weather-based automation easier.
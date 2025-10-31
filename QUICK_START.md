# Quick Start Guide

Get up and running with the Rain Forecast Blueprint in 5 minutes!

## Step 1: Create Helper Entities

In Home Assistant, go to **Settings → Devices & Services → Helpers** and create:

### 1. Toggle (Input Boolean)
- **Name**: Rain Within Hour
- **Icon**: `mdi:weather-rainy` (optional)

### 2. Number - Minutes Until Rain
- **Name**: Minutes Until Rain
- **Min**: 0
- **Max**: 60
- **Step**: 1
- **Unit**: minutes
- **Icon**: `mdi:timer-sand` (optional)

### 3. Number - Max Precipitation
- **Name**: Max mm Within Hour
- **Min**: 0
- **Max**: 200
- **Step**: 0.1
- **Unit**: mm
- **Icon**: `mdi:water` (optional)

### 4. Dropdown (Input Select)
- **Name**: Rain Intensity
- **Options**: 
  - None
  - Light
  - Moderate
  - Heavy
  - Violent
- **Icon**: `mdi:weather-pouring` (optional)

**Or** copy the contents of [`example_helpers.yaml`](example_helpers.yaml) to your `configuration.yaml`.

## Step 2: Import Blueprint

Click this button:

[![Import Blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FBibbleq%2FHA-OpenWeatherMap_UpcomingRain-Blueprint%2Fblob%2Fmain%2Frain_forecast_to_helpers.yaml)

**Or** manually:
1. Go to **Settings → Automations & Scenes → Blueprints**
2. Click **Import Blueprint**
3. Paste: `https://github.com/Bibbleq/HA-OpenWeatherMap_UpcomingRain-Blueprint/blob/main/rain_forecast_to_helpers.yaml`

## Step 3: Create Automation

1. Go to **Settings → Automations & Scenes**
2. Click **Create Automation → Use Blueprint**
3. Select **Rain Within Hour (Weather Forecast → Helpers)**
4. Configure:
   - **Weather Entity**: Your weather entity (e.g., `weather.home`)
   - **Poll Interval**: `/10` (every 10 minutes) - adjust as needed
   - **Rain Threshold**: `0.05` (default) - lower = more sensitive
   - **Helpers**: Select the helpers you created in Step 1
5. Click **Save**

## Step 4: Test

1. Wait for the next trigger (up to 10 minutes)
2. Check your helper entities in **Settings → Devices & Services → Helpers**
3. Verify they're being updated

## Time Pattern Examples

The **Poll Interval Pattern** accepts these formats:

| Pattern | Meaning |
|---------|---------|
| `/10` | Every 10 minutes |
| `/5` | Every 5 minutes |
| `/15` | Every 15 minutes |
| `0,30` | At :00 and :30 (twice per hour) |
| `0,15,30,45` | Four times per hour |
| `/1` | Every minute (not recommended - high API usage) |

## Troubleshooting

### Helpers not updating?
- Check your weather entity supports minute forecasts
- Go to **Settings → Automations & Scenes** and view the automation trace
- Verify your weather integration is configured correctly

### Wrong values?
- Adjust the **Rain Threshold** (lower = more sensitive)
- Check your weather provider's API status

### "Action not found" error?
- Requires Home Assistant 2024.2 or later
- Update Home Assistant if needed

## Next Steps

Now that your blueprint is working, you can:

1. **Create notifications** when rain is detected
2. **Automate devices** (close windows, retract awnings, etc.)
3. **Add to dashboard** to monitor rain status
4. **Create multiple automations** for different locations

See [README.md](README.md) for detailed examples!

## Support

- [Full Documentation](README.md)
- [Conversion Notes](CONVERSION_NOTES.md)
- [GitHub Issues](https://github.com/Bibbleq/HA-OpenWeatherMap_UpcomingRain-Blueprint/issues)

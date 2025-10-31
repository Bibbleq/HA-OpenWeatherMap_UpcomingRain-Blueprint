# Automation to Blueprint Conversion Notes

This document explains how the original Home Assistant automation was converted into a reusable Blueprint.

## Key Changes

### 1. Blueprint Metadata Added

The blueprint wrapper was added with:
- **name**: User-friendly name shown in HA UI
- **description**: Detailed explanation of functionality and requirements
- **domain**: Set to `automation`
- **source_url**: GitHub repository link for updates
- **input**: Definitions for all configurable parameters

### 2. Hardcoded Values → Blueprint Inputs

| Original | Blueprint Input | Purpose |
|----------|----------------|---------|
| `minutes: /10` | `!input poll_interval` | Configurable polling frequency |
| `weather.placeholder_provider` | `!input weather_entity` | User-selected weather entity |
| `rain_threshold: 0.05` | `!input rain_threshold` | Adjustable rain detection threshold |
| `input_boolean.placeholder_rain_within_hour` | `!input boolean_rain_within_hour` | User's boolean helper |
| `input_number.placeholder_minutes_until_rain` | `!input number_minutes_until_rain` | User's number helper |
| `input_number.placeholder_max_mm_within_hour` | `!input number_max_mm_within_hour` | User's number helper |
| `input_select.placeholder_rain_intensity` | `!input select_rain_intensity` | User's select helper |

### 3. Input Selectors

Each input was given an appropriate selector type:

- **Entity selectors**: Used for weather and helper entities with domain filters
  ```yaml
  weather_entity:
    selector:
      entity:
        domain: weather
  ```

- **Number selectors**: Used for poll interval and rain threshold with ranges
  ```yaml
  poll_interval:
    selector:
      number:
        min: 1
        max: 60
        unit_of_measurement: minutes
  ```

### 4. Template Variable Fix

The original automation had:
```yaml
{% set forecast = src.get('weather.placeholder_provider') %}
```

This was converted to:
```yaml
weather_entity_id: !input weather_entity
# ... then in template:
{% set forecast = src.get(weather_entity_id) %}
```

This ensures the weather entity ID is properly accessible within Jinja2 templates.

### 5. Features Preserved

All original functionality was maintained:
- ✅ Time pattern trigger every N minutes
- ✅ Minute-level forecast retrieval
- ✅ Rain threshold detection
- ✅ Precipitation calculations (max mm/min, max mm/hr, total)
- ✅ Minutes until rain calculation
- ✅ Rain intensity classification
- ✅ Helper entity updates (boolean, numbers, select)
- ✅ Choose/default logic for rain vs. no-rain scenarios
- ✅ Single execution mode

## Blueprint Benefits

1. **Reusability**: One blueprint, multiple automations with different settings
2. **User-friendly**: No YAML editing required after import
3. **Configurable**: All parameters adjustable through UI
4. **Documented**: Built-in descriptions guide users
5. **Shareable**: Easy to distribute and import
6. **Maintainable**: Single source for updates

## Usage Patterns

### Multiple Weather Entities
Create separate automations from the same blueprint for different locations:
- Home weather → Home helpers
- Office weather → Office helpers
- Vacation home → Vacation helpers

### Different Thresholds
Use different rain thresholds for various use cases:
- Low threshold (0.01) for sensitive outdoor equipment
- Standard threshold (0.05) for general alerts
- High threshold (0.10) for serious rain only

### Variable Polling
Adjust polling frequency based on needs:
- Every 5 minutes during rain season
- Every 15 minutes during dry season
- Every minute when rain is imminent (via separate automation)

## Testing Recommendations

1. **Validate helper entities exist** before creating automation
2. **Check weather entity** supports `get_minute_forecast`
3. **Monitor automation traces** for errors in first 24 hours
4. **Verify helper updates** by checking entity states
5. **Test rain threshold** by temporarily lowering value

## Compatibility Notes

- Requires Home Assistant 2023.4 or later (for blueprint features)
- Weather entity must support `weather_provider.get_minute_forecast` action
- All four helper entity types must be created before use
- Input Select must contain exact option names: None, Light, Moderate, Heavy, Violent

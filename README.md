# SKIPAU's Home Assistant Aqua Temp & Hi Temp Heat Pumps

Forked - This is Home Assistant's an integration for PHINX Aqua Temp / HiTemp compatible heat pumps.
It allows controlling temperature, HVAC mode (heat, cool, auto, off) and fan mode (low, auto).
It also reports all parameters available in sensors and sensors.

AquaTemp's cloud protocol is based on [aquatemp](https://github.com/dst6se/aquatemp) project with additional functionality.

[Changelog](https://github.com/radical-squared/aquatemp/blob/master/CHANGELOG.md)

## How to

#### Requirements

Account for AquaTemp / HiTemp

##### Shared device to avoid token expiration when using the App parallel to Integration

Aqua Temp / Hi Temp introduced in v1.5.9 single login "feature" by default, meaning, only one device can be logged in,
Following a recommendation from [@dodg3r](https://github.com/@dodg3r) & [@kopierschnitte](https://github.com/@kopierschnitte), you can avoid it by creating additional account and sharing the device with that new account.

#### Switching from 1.x/2.x to 3.x

Requires complete deletion of integration and reinstall after restart HA

#### Installations via HACS [![hacs_badge](https://img.shields.io/badge/HACS-Custom-41BDF5.svg)](https://github.com/hacs/integration)

- In HACS, look for "Aqua Temp" and install and restart
- If integration was not found, please add custom repository `radical-squared/aquatemp` as integration
- In Settings --> Devices & Services - (Lower Right) "Add Integration"

#### Setup

To add integration use Configuration -> Integrations -> Add `Aqua Temp`
Integration supports **multiple** accounts and devices

| Fields name | Type     | Required | Default             | Description                                                                                        |
| ----------- | -------- | -------- | ------------------- | -------------------------------------------------------------------------------------------------- |
| Username    | Textbox  | +        | -                   | Username of account                                                                                |
| Password    | Textbox  | +        | -                   | Password of account                                                                                |
| API Type    | Dropdown | +        | Aqua Temp (=<1.5.8) | API type (where the account was created), available values: Aqua Temp, HiTemp, Aqua Temp (=<1.5.8) |

###### Validation errors

| Errors                    |
| ------------------------- |
| Invalid credentials (403) |

## HA Device

Device scheme is according to data from the API

```json
{
  "identifiers": ["aqua_temp", "{DEVICE ID}"],
  "name": "{DEVICE NICK NAME or DEVICE CODE}",
  "device_type": "{DEVICE TYPE}"
}
```

## HA Components

Per device the following components will be generated by default:

| Entity Name                       | Parameter        | Platform      | Protocol Code? |
| --------------------------------- | ---------------- | ------------- | -------------- |
| {HA Device Name} Temperature Unit | temperature_unit | select        | False          |
| {HA Device Name} Fault            | is_fault         | binary_sensor | False          |
| {HA Device Name} Status           | device_status    | binary_sensor | False          |
| {HA Device Name} API Status       | api_status       | binary_sensor | False          |

### Per product ID

Additional components are according to following mapping:

| Device's Product ID          | Parameter                                                                                                                       |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| Default                      | [JSON](https://github.com/radical-squared/aquatemp/blob/master/custom_components/aqua_temp/parameters/default.json)             |
| Hitemp 1245226668902080512   | [JSON](https://github.com/radical-squared/aquatemp/blob/master/custom_components/aqua_temp/parameters/1245226668902080512.json) |
| AquaTemp 1442284873216843776 | [JSON](https://github.com/radical-squared/aquatemp/blob/master/custom_components/aqua_temp/parameters/1442284873216843776.json) |

## Run API over CLI

### Requirements

- Python 3.10
- Python virtual environment
- Install all dependencies, using `pip install -r requirements.txt` command

### Environment variables

| Environment Variable | Type    | Default | Description                                                                                                               |
| -------------------- | ------- | ------- | ------------------------------------------------------------------------------------------------------------------------- |
| Username             | String  | -       | Username used for Aqua Temp                                                                                               |
| Password             | String  | -       | Password used for Aqua Temp                                                                                               |
| DEBUG                | Boolean | False   | Setting to True will present DEBUG log level message while testing the code, False will set the minimum log level to INFO |

### Execution

Run file located in `tests/main.py`

## Troubleshooting

### Logs

Before opening an issue, please provide logs related to the issue,
For debug log level, please add the following to your config.yaml

```yaml
logger:
  default: warning
  logs:
    custom_components.aqua_temp: debug
```

Or use the HA capability in device page:
Settings -> Devices & Services -> Aqua Temp -> 3 dots menu -> Enable debug logging
When done and would like to extract the log:
Settings -> Devices & Services -> Aqua Temp -> 3 dots menu -> Disable debug logging

Please attach also diagnostic details of the integration, available in:
Settings -> Devices & Services -> Aqua Temp -> 3 dots menu -> Download diagnostics

### Data from mobile app

If mapping for your device are incorrect, please follow the steps below before reporting an issue:

1. Install on your mobile device packet sniffer (I'm using for Android - NetCapture)
2. Activate the packet sniffer just for the app
3. Open the app
4. Login
5. Open relevant device
6. Open menu
7. Click on `Parameter Settings`
8. First cycle, use password for user parameters, code: `022`
9. Take screenshot of each screen (letter)
10. Repeat steps 8 & 9 with code `066` - factory parameters
11. Open packet sniffer app and extract requests (and responses) of `login`, `deviceList`, `getDataByCode`

Attach the following details to issue:

- Mobile App name and version
- Attach screenshots and logs as zip file or link for download
- Attach diagnostic details from HA integration

### Invalid Token

In case you have referenced to that section, something went wrong with the encryption key,
Encryption key should be located in `.storage/aqua_temp.config.json` file under `data.key` property,
below are the steps to solve that issue.

#### File not exists or File exists, data.key is not

Please report as issue

#### File exists, data.key is available

Example:

```json
{
  "version": 1,
  "minor_version": 1,
  "key": "aqua_temp.config.json",
  "data": {
    "key": "ox-qQsAiHb67Kz3ypxY19uU2_YwVcSjvdbaBVHZJQFY="
  }
}
```

OR

```json
{
  "version": 1,
  "minor_version": 1,
  "key": "aqua_temp.config.json",
  "data": {
    "key": "ox-qQsAiHb67Kz3ypxY19uU2_YwVcSjvdbaBVHZJQFY="
  }
}
```

1. Remove the integration
2. Delete the file
3. Restart HA
4. Try to re-add the integration
5. If still happens - report as issue

#### File exists, key is available under one of the entry configurations

Example:

```json
{
  "version": 1,
  "minor_version": 1,
  "key": "aqua_temp.config.json",
  "data": {
    "b8fa11c50331d2647b8aa7e37935efeb": {
      "key": "ox-qQsAiHb67Kz3ypxY19uU2_YwVcSjvdbaBVHZJQFY="
    }
  }
}
```

1. Move the `key` to the root of the JSON
2. Restart HA
3. Try to re-add the integration
4. If still happens - follow instructions of section #1 (_i._)

## Discord

Join our community in [Discord](https://discord.gg/6B6fakaK)

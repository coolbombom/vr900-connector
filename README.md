# vr900-connector

[![Build Status](https://travis-ci.com/thomasgermain/vr900-connector.svg?branch=develop)](https://travis-ci.com/thomasgermain/vr900-connector)

<b>Please note that the project is still in beta state, it means  I may do some (un)intentional breaking changes</b>

Python 3.5+

For now, the connector is ony able to read data from the system (but it's planned to be able to alter system as well), it only handles only one heating system (one serial number). I cannot test more than that since I only have heating system at home.

## Install
```bash
[sudo] pip install vr900-connector 
```

## Tests
You can run tests with
```bash
pytest
```

## Usages

### Command line
TODO


### Programmatically
 
The connector is separate in two layers:

#### 1. ApiConnector
This is the low level connector using the vaillant API and returning raw data directly coming from the API. The connector is handling the login and session.
The connector able to reuse an already existing session (cookies). Two files are saved (cookies and serial number of your installation) on the file system. Default location is:
```python
tempfile.gettempdir() + "/vaillant_vr900_files"
```
but it can be overridden. File are named .vr900-vaillant.cookies and .vr900-vaillant.serial.


Here is an example how to use it:
```python
from vr900connector.api import ApiConnector
   
connector = ApiConnector('user', 'pass')
connector.get_facilities() 
```
to get some information about your installation, this returns the raw response, something like this:
```json
{
    "body": {
        "facilitiesList": [
            {
                "serialNumber": "1234567891234567891234567890",
                "name": "Name",
                "responsibleCountryCode": "BE",
                "supportedBrand": "GREEN_BRAND_COMPATIBLE",
                "capabilities": [
                    "ROOM_BY_ROOM",
                    "SYSTEMCONTROL_MULTIMATIC"
                ],
                "networkInformation": {
                    "macAddressEthernet": "12:34:56:78:9A:BC",
                    "macAddressWifiAccessPoint": "34:56:78:9A:BC:DE",
                    "macAddressWifiClient": "56:78:9A:BC:DE:F0"
                },
                "firmwareVersion": "1.1.1"
            }
        ]
    },
    "meta": {}
}
```

Basically, you can use 
```python
from vr900connector.api import ApiConnector
   
connector = ApiConnector('user', 'pass')
connector.get('') 
```
with constants from
```python
vr900connector.api.constants
``` 

I recommend using this layer if you only want to retrieve basic data (outdoor temperature, current temperature, etc.)

#### 2. SystemManager
This layer allows you to interact in a more friendly way with the system.
The underlying connector is hidden and raw responses are mapped to more useful object.

For now, the only function is:
```python
from vr900connector.systemmanager import SystemManager
   
manager = SystemManager('user', 'pass')
system = manager.get_system() 
```
Basically, the connector is returning quite the same information as the android mobile app can display.

The main object to manipulate is 
 ```python
 vr900connector.model.system
 ```

## Todos
* Create scripts to get specific data, something like "python vaillant.py username password rooms" to get all rooms
* Add some doc for pypi
* Proper way to deploy to pypi and create tag
* Add updates method (alter system config)
* find a more fancy name

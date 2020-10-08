# domoticz_quick_access_mobile
A Domoticz Custom User Interface, accessible from any browser, as a responsive, mobile-first front-end. 

## Purpose
To develop for personal use only, a Custom Web Frontend for the [Domoticz Home Automation System](https://domoticz.com/):
* control frequently used functions, like lights, thermostats, shutter/blinds, climate & key dates information.
* simple to use user interface with limited components, i.e. buttons, dropdowns, modal info pages.
* mainly accessed from mobile devices running Android or iOS.
* located on a Raspberry Pi running Domoticz 2020.2.
* created with HTML, JavaScript modules, CSS styles.
* interacting with Domoticz using JSON via [HTTP API requests](https://www.domoticz.com/wiki/Domoticz_API/JSON_URL%27s).
* flexible in maintaining the Domoticz devices for the various functions by using Domoticz roomplans & device properties to setup the HTML interface.

![android-0](https://user-images.githubusercontent.com/47274144/95470037-9a0ded80-0980-11eb-9877-340d5004e3fb.png)

This application is called Domoticz Quick Access Mobile **QAM**.

## Background
The application has been developed, based on a request to enable simple access to Domoticz from any device in a local network.
Meant by simple access, is a user interface with limited components on a mobile device screen having large sized components, like text, buttons, dropdowns etc..
Functions are grouped, separated by horizontal rules. Each function having similar layout depending its required components. The screen must be scrollable to select a function.

A function is used to 
* control dedicated devices, like lights, thermostats, shutter/blinds
* inform about climate & key dates

## Solution
The following libraries & styles are used and stored locally (instead accessing a Content Delivery Network CDN):
* [jQuery](https://jquery.com/) v3.5.1 - Copyright JS Foundation and other contributors - [License](http://jquery.org/license).
* [Bootstrap](https://getbootstrap.com/) v4.3.1 - Copyright 2011-2019 The Bootstrap Authors - [License](https://github.com/twbs/bootstrap/blob/master/LICENSE).
* [Font Awesome Free](https://fontawesome.com/) 5.14.0 - @fontawesome - [License](https://fontawesome.com/license/free) (Icons: CC BY 4.0, Fonts: SIL OFL 1.1, Code: MIT License).
* Versions are subject to change

**The UI is based upon the HTML Bootrap Grid.**
Each control function has several rows with grids depending its features. The inform functions make use of modal dialogs with lists.
The devices used for a function are populated from the related Domoticz roomplan.
The communication between the UI and Domoticz is via HTTP API requests to the Domoticz engine. The Domoticz engine to action / response accordingly.
Depending the function selected, an Domoticz Automation Event, dzVents Lua, is triggered.
JavaScrip jQuery.getJSON function is handling the HTTP API requests.

The main file is "index.html". It has sections:

**HEAD**
* Include the third party styles and libraries (stored locally) plus some additional styles.

**BODY**
* _HTML_ for the user interface, based on the Bootstrap Grid, with selectors.
* _JavaScript_ to interact, via HTTP API requests, with Domoticz triggered by HTML components and to set data in HTML selectors from HTTP API responses, but also to change Domoticz devices.

**Note**
The UI focus is on mobile devices, but it is simple to create other type of user interfaces because the JavaScript section contains all the Domoticz interaction logic.

The usage of roomplans provides flexibility for the devices used. There is no hardcoding required to get or set data in the user interface.
Whenever a roomplan or device changes, i.e. device added or removed, device name change this is handled by the user interface - either load or reload if already open in a browser.

## Get Started
See file "GETSTARTED.md".

## Concepts
Recommend to read the source files "basic.html" and "index.html".
More information see "domoticz_quick_access_mobile.pdf".

### Roomplan Data Request
The data is gathered from the Domoticz HTTP API request for a given idx of a plan.
The HTTP API request returns a JSON string with an array of devices.
Important is the key status to ensure the data is properly fetched.
Below an extract of the key:value pairs. The key result is the array containing all devices with its properties.
The device properties are device dependend, but for all the DQAM functions Data, Name, PlanID and idx are used (the name is case sensitive).
The data property is used to display device value.

_Exception_
The data used for the function Climate, device Wind extracts the windspeed and direction from the data property.

### Roomplan HTTP API Request & Response Devices
The plan idx=23, which is Klima ("Climate") used in a modal information dialog with close button.

**Request**
```
http://domoticz-ip:port/json.htm?type=devices&plan=23
```
**Response**
```
{
	"ActTime" : 1600949265,
	...
	"app_version" : "2020.2",
	"result" : 
	[
		{
			"Data" : "24-09 07:11 - 19:14 (12:03)",
			"Name" : "Tageslicht (Std)",
			"PlanID" : "23",
			"idx" : "121"
		},
		..
		{
			"Data" : "18.7 C, 62 %",
			"Name" : "Aussen",
			"PlanID" : "23",
			"idx" : "344",
		}
	],
	"status" : "OK",
	"title" : "Devices"
}
```

### Roomplan HTTP API Request & Response List
As the roomplan is core to create the content of the user interface, this is the request to get all the roomplans name and idx.
```
http://domoticz-ip:port/json.htm?type=plans&order=name&used=true
```
The data is used to set f.e. the heading in an information dialog, i.e. "Klima" and "Termine". 

The other roomplans are used to control devices via a dropdown component, i.e. 
Lights (Hardware Philips Hue), Shutter/Blinds (Somfy via RFXCOM), Heating (Thermostats Homematic IP via HTTP XMLAPI requests).
```
{
	"result" : 
	[
		{"Devices" : 7,"Name" : "Somfy","Order" : "1", "idx" : "24"},
		{"Devices" : 9,"Name" : "Hue","Order" : "17","idx" : "20"},
		{"Devices" : 7,"Name" : "Heizung","Order" : "19","idx" : "21"},
		{"Devices" : 11,"Name" : "Klima","Order" : "21","idx" : "23"},
		{"Devices" : 7,"Name" : "Termine","Order" : "22","idx" : "4"},
	],
	"status" : "OK","title" : "Plans"
}
```

## Control Requests
As mentioned, all requests are via the Domoticz HTTP engine.
For each of the functions, the request URL's are defined with placeholder.
The placeholders in the URL are replaced depending the selected device.
The URL is submitted using buttons or a slider (example set brightness Hue light bulb).

**Examples URL**
```
// Set the domoticz system base url - TODO: Checkout why 127.0.0.1 is giving cors error
const URL_DOMOTICZ = "http://domoticz-ip:port/json.htm?";
// CMD = Somfy switch command Off, Stop, On
var	  URL_SWITCH_SOMFY = URL_DOMOTICZ + "type=command&param=switchlight&idx=#IDX#&switchcmd=#SWITCHCMD#";
// CMD = Somfy Group switch command level 10 (Open), 20 (Stop), 30 (Close)
var	  URL_SWITCH_SOMFY_GROUP = URL_DOMOTICZ + "type=command&param=switchlight&idx=#IDX#&switchcmd=Set%20Level&level=#LEVEL#";
// CMD = Hue switch command Off, Set%20Level=NN, On
var	  URL_SWITCH_HUE = URL_DOMOTICZ + "type=command&param=switchlight&idx=#IDX#&switchcmd=#SWITCHCMD#";
// CMD = Thermostat switch state ON or OFF handled by customevent raspmatic_thermostat_onoff. JSON data key : {"idx":123, "state":0|1}
var	  URL_THERMOSTAT_ONOFF = URL_DOMOTICZ + "type=command&param=customevent&event=raspmatic_thermostat_onoff&data=#DATA#";
``` 

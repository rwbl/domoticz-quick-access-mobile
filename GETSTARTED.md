# domoticz_quick_access_mobile
A Domoticz Custom User Interface, accessible from any browser, as a responsive, mobile-first front-end. 

## Get Started
To get started, recommend to use the basic example with HTML file "basic.html".
The advanced example is "index.html". Read the source to explore how to use - but same concept as "basic.html".

## Step 1
Create a folder on the Domoticz system:
```
<domoticz-path>/www/qam
```
Example Raspberry Pi: 
```
/home/pi/domoticz/www/qam
```

## Step 2
Extract the archive “qam.zip” to the previous created folder, resulting in folder structure (example Raspberry Pi):
```
/home/pi/domoticz/www/qam/css
/home/pi/domoticz/www/qam/js
/home/pi/domoticz/www/qam/webfonts
/home/pi/domoticz/www/qam/basic.html
/home/pi/domoticz/www/qam/index.html
```

## Step 3
Create or Select Domoticz room plan(s) and note the Idx of the room plan(s).
Recommend to start with 2 room plans:
* one to control switches using buttons and
* the other to display information via burger menu.

Get the Domoticz IP address:port of the Domoticz system to access, i.e. 192.168.1.179:8080.

## Step 4
Define the Domoticz IP address:port and the room plans in the HTML page "basic.html".
Change the JavaScript constants:
```
const URL_DOMOTICZ = "http://domoticz-ip:port/json.htm?";
const IDX_ROOMPLAN_QAM_INFORMATION = 4;
const IDX_ROOMPLAN_QAM_SWITCHES = 5;
```
_Note_
If also changing the constant names, ensure to set those on the "document.ready" function as well.
```
// Set the name of the various burger menu items
setPlanName(URL_GET_PLANS, "nav-item-information", IDX_ROOMPLAN_QAM_INFORMATION);
// Define the select options
setSelectOptions(IDX_ROOMPLAN_QAM_SWITCHES, "selectswitch");
```

## Step 5
Open the URL in a webbrowser, start from the development device.
```
http://domoticz-ip:port/qam/basic.html
```
In the webbrowser, press F12 and checkout the console for informations/errors.
_Note_
The debug information is set by the flag DEBUG in the JavaScript section:
```
const DEBUG = true;	// true|false;
```

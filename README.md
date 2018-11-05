# WVC Micro Inverter Flow for Node Red

Node red flows to control and collect data from the WVC series of micro inverters. The inverter is sold under various brand names around the world. [Manufacturer website.](http://www.kaidengdg.com/en/pvsystem.html)

Features available in these flows:

* Set up multiple micro inverters.
* Display realtime data from each inverter.
* Collect energy production data from each inverter.
* Limit energy production to prevent grid backfeed (requires external energy meter input).
* Compile data from all inverters into a graphical format.
* Generates MQTT events from inverter data.

### Prerequisites

* WVC Micro Inverter
* WVC Wireless Modem
* Computer with USB or serial port running [Node Red](https://nodered.org)
* [MongoDB](https://www.mongodb.com) installed on the same computer.
* Access to an MQTT broker if you want to make use of the MQTT messages.

### Node Red Nodes

The flows depend on the following additional nodes that need to be added:

* node-red-contrib-bigmongo
* node-red-contrib-objectid
* node-red-dashboard
* node-red-node-serialport
* node-red-contrib-persist

### Installing

If MongoDB is not installed on your system, follow operating system specific instructions to install it. By default the flow uses a mongodb server on localhost on the default port with no authentication. Go through all MongoDB nodes and change that if needed.

Install required nodes from the node red sandwich menu -> 'Manage Palette'.

Download each flow and copy to clipboard using any text editor. Then choose 'Import -> Clipboard' from the node red sandwich menu, paste the flow into the text box and click 'Import'. Repeat for each flow.

By default MQTT messages will be sent to an MQTT broker on the default port on localhost. Go through all MQTT nodes and change that if needed.

Setup the correct serial port in the serial nodes in the "Query Inverters" flow. By default /dev/ttyUSB0 is used.

Connect the wireless modem to your computer's USB or serial port. Make sure both the inverter and the modem are powered.

Make a note of the four character hex coded inverter id found on the label on the inverter. Go to the Node Red dashboard and open up the "WVC Inverter Control tab". Type in the inverter id, choose the correct model and click "Add".

Note that the inverter transmits no data if it's not generating electricity or it's not connected to the grid.

## MQTT messages

Input

```
grid/power #Grid power flow expressed as a negative number for power export to the grid and a positive number for power Import. Unit: kWh
grid/power_in #A positive number expressing power import from grid (for meters only capable of reporting positive numbers). Unit: kWh
grid/power_out #A positive number expressinf power export to the grid (for meters only capable of reporting positive numbers). Unit: kWh
solar/energy_metered #External meter reporting power flow from all inverters. Unit: kWh
powerLimit #Set maximum power to be generated by the whole system. Unit: W
```

Output

Variable in brackets represent hex id for modem (4 bytes) and inverter (2 bytes).
```
modem/id #Modem id as reported by the modem. Format: 4-byte HEX as a string.
modemConnection/id #Successful connection test from modem with id. Format: 4-byte HEX as a string.
modems/{modemId}/inverters/{inverterId}/powerGrade #Maximum output power was set for inverter. Format: Number 1-100 (percentage).
modems/{modemId}/inverters/{inverterId}/powered #Inverter was powered on or off. Format: on = "1", off = "0" (string).
modems/{modemId}/inverters/{inverterId}/dcCurrent #Inverter real time DC current. Unit: A
modems/{modemId}/inverters/{inverterId}/dcVoltage #Inverter real time DC voltage. Unit: V
modems/{modemId}/inverters/{inverterId}/temperature #Inverter temperature. Unit: degrees Celsius
modems/{modemId}/inverters/{inverterId}/dcPower #Inverter real time DC power. Unit: W
modems/{modemId}/inverters/{inverterId}/acCurrent #Inverter real time AC current. Unit: A
modems/{modemId}/inverters/{inverterId}/acVoltage #Inverter real time AC voltage. Unit: V
modems/{modemId}/inverters/{inverterId}/acPower #Inverter real time AC power. Unit: W
modems/{modemId}/inverters/{inverterId}/pf #Inverter real time AC power factor.
modems/{modemId}/inverters/{inverterId}/acApparentPower #Inverter real time Apparent AC power. Unit: W
modems/{modemId}/inverters/{inverterId}/fiveMinuteEnergy #Sum of energy generated by inverter during the last 5 minutes. Unit: kWh
```
## Notes

The Solar gauge in the Instant Power tab shows the sum of the real time DC power generated by all inverters multiplied by the efficiency (0.9). The inverter does also report real time AC current and voltage. The manufacturer provides a lookup table for the power factor in relation to the generated current, but since the actual power factor will vary widely depending on the load this will not be accurate enough. Empiric tests suggest that DC power multiplied by efficiency provides a more realistic number for the power being generated.


## Built With

* [Node Red](https://nodered.org)


## Versioning

* 1.0 - First Release

## Authors

* **Frey Mansikkaniemi** - [InviteFrey](https://github.com/invite-frey)

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments

* Thanks to KaiDeng Energy Technology Co. Ltd for providing the communication protocol used by the WVC wireless modem.

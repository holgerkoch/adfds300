# adfds300
alternate display for CBE DS300

Goal of the project is, to create a cheap but functional alternate display for the ELB CBE DS300. At the moment, the following characteristics are planed:

* visualization of all information from the DS 300. For example: water, dump water, connection between the batteries (simulated D+), voltage of B1 and B2, temperature in/out/cooler/freezer
* time and date from GPS, corrected by the timezone information for the actual position
* position tracking with http://owntracks.org/ incl. geovisualization on a website 
* visualization of weather informationen for the actual position, incl. forecast for the next 2 days
* temperature of the fridge and freezer
* Name of the next town, residents of the town, distance to the town centre 

All of this should be realized with low power consumption and low cost. Targeted is for less than 100€.

Hardware:
* SIM808 Modul GSM GPRS GPS Entwicklungsboard IPX SMA mit GPS antenne
https://de.aliexpress.com/item/SIM908-Module-GSM-GPRS-GPS-Development-Board-IPX-SMA-with-GPS-Antenna-for-Raspberry-Pi/32267381754.html
16€
* ESP I2E WeMos D1 WiFi uno basierend ESP8266 
https://de.aliexpress.com/item/Free-Shipping-1pcs-Smart-Electronics-ESP-12E-WeMos-D1-WiFi-uno-based-ESP8266-shield-for-arduino/32533099213.html
4€
* Waveshare 4.3inch e-Paper 800x600 Resolution
https://www.amazon.de/dp/B00VV5IMN0/ref=cm_sw_em_r_mt_dp_31FBybD1SSTB0
52€

Software:
* for tracking the position http://jpmens.net/2016/04/16/a-tiny-esp8266-based-gps-tracker/
https://github.com/jpmens/pico


To reach the low power consumption goal, there are two operation states.

1. the engine of the camper goes on
* activate the GPS / GSM modul
* send the actual position per mqtt and owntracks.org to a owntracks server.
* once the minute, the informationen on the epaper will be refreshed

2. the engine of the camper goes off
* get the weather information for the actual position (http://api.openweathermap.org/data/2.5/forecast/daily?lat=50.984768&lon=11.02988&units=metric&&lang=de&cnt=3&APPID=...)
* get the timezone information for the actual position
* mark the actual position on the map
* deactivate the GPS / GSM modul
* once the minute, the informationen on the epaper will be refreshed. After this, the wemos and the epaper enter a sleep state.

Thoughts on data consumption:
* the weather information cost 1k per request. For 4 requests a day (2 scheduled, maybe 12:00 and 0:00, and 2 after a stop), this are 120KB per month.
* the tracking of the camper cost about 50 Byte per position. The Pico (owntracks for ESP8266) publishes (or serializes to the file system if it’s offline) a location when it detects it’s moved mindist meters (100 by default) and 5 seconds interval have elapsed. Depending on speed and distance, you will need max. 720 plots per hour, 36 kByte per hour. If you drive 2 hours a day, you need about 2,5 MByte per month.
* update the timezone information after each engine stop. For 4 stops a day you need 60KB per month. 
* At the moment, with a sim card from http://datamobile.ag/wp-content/uploads/2015/08/tarifblatt.pdf, you pay 20 cents per MByte in whole europe. So it cost 60 cent plus 1€ monthly maintenance fee (only when card has been used).

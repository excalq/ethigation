# Ethigation: Irrigation with Smart Contracts

This software infrastructure exists to explore decentralized methods of controlling irrigation of the author's orchard.

Blockchain platforms such as Ethereum promise a zero-downtime, trustless computation model. Downtime is the enemy of irrigation in hundred degree (Fahrenheit) weather. In mere hours, decades of hard work can be killed by severe conditions such as network interruptions - something the author knows from experience in IoT-powered irrigation control.

# In a Nutshell (or Peach Pit)

Local irrigation control using simple electronics and mechanical valves is greatly enhanced with intelligent decisions made of which zones are activated when, and for how long. Rather than naively doing this with a static timer, we can achieve a much better result with a set of key inputs and logic to output the boolean state for a given zone at a given time.

The author's orchard is also off-grid and running with a limited Solar Power system, which adds constraints for power usage and time of day.

For example, early and late in the day, solar power is weak and can pump drip zones requiring low pressure, but will not perform well when running throw sprinklers, as these require greater pressure. 

The pumping system uses two pumps - one running directly from solar, hence the variable power output, and another 120 volt AC pump, running from the orchard cabin's battery bank. This can provide powerful output, but will drain the battery bank if active for too long.

Weather events such as wildfire smoke and freezes may require special actions as well.

# System Inputs

The irrigation controllers and pumps require a boolean input of whether a given zone should activate. Here are some variables which are used to feed this decision.

* Time since zone has been last irrigated
* Soil moisture sensor readings for the zone
* Zone type: sprinkler vs. drip emitters
* Current Date and Time
* Solar azimuth and altitude
* Cloudiness or Smoke
* Solar irradiance
* Maintenance lock-outs
* Freezing conditions
* Solar battery bank state of charge
* Irrigation system pressure

The data for these inputs will come from mixed sources: local sensors, local MQTT/InfluxDB, and online weather APIs.
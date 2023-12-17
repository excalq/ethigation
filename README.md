# Ethigation: Irrigation with Smart Contracts

This software infrastructure exists to explore decentralized methods of controlling irrigation of the author's orchard.

Blockchain platforms such as Ethereum promise a zero-downtime, trustless computation model. Downtime is the enemy of irrigation in hundred degree (Fahrenheit) weather. In mere hours, decades of hard work can be killed by severe conditions such as network interruptions - something the author knows from experience in IoT-powered irrigation control.

Additionally, this enables locally distributed decision making. Complicated controller logic and current state do not need synchronization within the local network's potentially unreliable and resource constrained devices. No databases are necessary either locally at the Orchard, or in a cloud infrastructure provider.

Finally, it becomes easy to grant open read-only access to the irrigation system's state, but keeping permissioned control for registered owners.

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

# Persisted State

State should be kept to a minimum, and should be used to accelerate response times, store configuration values, and cache oracle data results.

1. Zone Schedule Configuration: Set by the user, giving a per-zone, per-pump default schedule to follow.
2. Zone Sensor mapping: Which sensors relate to which zones (or multiple/all)
3. Oracle values: Typically weather data or random number pool
4. Weather API values: For expensive or slow API lookups, cache results and update as configured

# Control Loops

Let's compile a set of input-response loops, which are rules for use in the irrigation control contract.

1. If the battery bank is low, turn of the AC pump.
2. If frost is expected now/overnight, turn all pumps off, open the winterization valve.
3. If a maintenance lock-out is set on a device (pump or valve), prevent output change triggers.

# Configuration Methods

1. **registerZone(Str zoneName, ZoneConfig conf) -> Zone**: Creates a zone with a configuration, persists to state.
1. **deleteZone(Zone zone) -> bool**: Deletes a saved zone configuration.
1. **registerPump(Str pumpName, PumpConfig pump) -> Pump**: Creates a pump with a configuration, persists to state.
1. **deletePump(Pump pump) -> bool**: Deletes a saved pump configuration.
1. **registerOwner(EthAddress addr) -> bool**: Creates a record of an EOA address which will have write-access to this contract.
1. **deleteOwner(EthAddress addr) -> bool**: Deletes addr from ownership mapping. Validates at least one owner exists.

# State Methods

1. **activateZone(Zone zone) -> (bool, str)**: Sets the zone's state as active. Returns true any may a warning string.
1. **activatePump(Pump pump) -> (bool, str)**:  Sets the pump's state as active. Returns true and may issue a warning string.

# Query Methods

1. **zoneReady(Zone zone) -> (bool, str)**: Is the zone ready to activate? Returns boolean with optional reason (usually why the zone cannot be activated).
1. **zoneMutex(Zone zone, Pump pump) -> list[Zone]**: Returns a list of zones which are mutually exclusive with the given zone and pump.
1. **getTime(Optional[Str isoDatetime]) -> datetime**: Return a `datetime.datetime` with the `isoDatetime` if given. Otherwise, use `block.timestamp`. Allows testing with arbitrary date-times.
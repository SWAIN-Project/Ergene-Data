# Table of Contents

- [Relationship Between Clustering and End-devices](#relationship)
- [Parameters Used in Lorawan Gateway Location Detection](#clustering-params)
- [Parameters Used in NS-3 Network Simulator](#ns3-params)
- [Results Obtained from NS-3 Network Simulator](#ns3-results)
- [Limitations](#limitations)
- [References](#references)

## Result file summary
- 75 locations indicate original sensor locations around Ergene river
- 25 locations indicate the combination of sensor and processing facilities taken from government agency
- 11 locations indicate the sensor locations from the government data

## TODOs

- Keeping clustering parameter ranges more restricted to fasten simulation times and obtain more results.
- Determining whether the Path-Loss parameters taken from the referenced paper should be changed according to Ergene environment or not.
- Changing parameters according to future requests as well obtaining better hardware/lorawan energy settings.

# <a name=relationship></a>Relationship Between Clustering and End-devices
There are three different result .csv files provided. 

- gateway_locations.csv

    - gw_key: represents the unique identifier for a gateway for the cluster parameters this gateway is located with.
    - distance_threshold, outlier_threshold, remove_intersections: these three parameters are unique for each clustering process
    - lat/lon/altitude: represents the location of the gateway.

Therefore, in this file [gw_key, distance_threshold, outlier_threshold, remove_intersections] represents a unique combination.

- gateway_sensor_relations.csv
    - gw_key: represents the unique identifier for a gateway for the cluster parameters this gateway is located with.
    - sensor_key: represents the unique identifier for the clustering parameters this sensor is used on.
    - distance_threshold, outlier_threshold, remove_intersections: these three parameters are unique for each clustering process
    - sensor_latitude,sensor_longitude,sensor_altitude: represents the location of a sensor.

In short terms, within the unique combination of [gw_key, distance_threshold, outlier_threshold, remove_intersections], each sensor is assigned to a gateway.

- sensor_results.csv
    - sensor_key: represents the unique identifier for the clustering parameters this sensor is used on.
    - distance_threshold,outlier_threshold,remove_intersections: these three parameters are unique for each clustering process
    - Results will be explained in the following sections

In short, [sensor_key, distance_threshold, outlier_threshold, remove_intersections] is a unique combination in this file.

**EXTREMELY IMPORTANT** DO NOT assume a one-to-one relationship between gateways and sensors. While clustering tries to create sets according to the provided parameters, it does not mean that sensors do not communicate with the gateways of other clusters. Given different path-loss values, sensors WILL communicate with other gateways from other clusters. Therefore, while processing the NS-3 results, looking at it at individual sensor level will be more appropriate. Do not try to group up the NS-3 results on gateway level.

# <a name="clustering-params"></a>Parameters Used in Lorawan Gateway Location Detection
Locations are as precise as possible in terms of floating numbers. Which is the reason why there are so many floating point numbers in lat/lon values.

## distance_threshold (meters)
Determines the expected distance for the maximum LoRaWAN signal range. A sensor cannot be further away from a gateway with an error margin of 100 meters.

Range = [500, 10000] 

## outlier_threshold
Determines the value which the clusters will be cut at. If a cluster has equal or less than the number of sensors from this value, that cluster will not be assigned a gateway. For examle, 0 treshold means all clusters will be taken, 1 means clusters with 1 sensors will be removed.

Range = [0, 1, 2, 3]

## remove_intersections
Determines if the sensors which are within the range of more than one gateway unit should be removed or not. 1 means remove, 0 means keep them.

Range = [False, True]

## latitude,longitude,altitude
Determines the output location of gateways.

# <a name="ns3-params"></a>Parameters Used in NS-3 Network Simulator

Following table shows which values are taken for which parameter during simulations. After that each parameter is explained separately.

|             Path Loss and LoRa Parameters             | Value |
|:-----------------------------------------------------:|:-----:|
| Path Loss Exponent                                    |   2.2 |
| Reference Distance  (meters)                          |   100 |
| Reference Loss (dB)                                   |    78 |
| Simulation Time  (seconds)                            | 86400 |
| LoRa time-slot duration for a single packet (seconds) |     5 |
| LoRa duty-cycle duration (seconds)                    |   135 |

Path loss exponent value, reference distance which identifies at what distance the loss values are calculated for, and reference loss which is the loss at that reference distance is taken from [1]. For more detail, check the log distance path-loss model. If there is any issue on the usage of these parameters from the references please contact me as I am not a network expert.

Simulation time is set to a 1-day period for now, for a single clustering parameter combination, longest experiment execution takes around 45 minutes. Therefore increasing this period will also increase the experiment execution time, something to keep in mind.

LoRa time-slot is the expected time in which, a sensor sends a packet and receives the ACK back. **More about this is mentioned in the limitations section.** Since only a single frequency and spreading factor is utilized, in each cluster, only a single sensor can be actively transmitting at any point. Otherwise signals will interfere with each other.

Duty-cycle is the duration which, each sensor has to wait for after transmitting a packet. This applies for the retransmissions on failures as well. To make it more solid, if a sensor sends a packet at second 300s, then it has to wait for 135s to send the next one, which will be around 435s.


|             Sensor Hardware Details            | Value |
|:----------------------------------------------:|:-----:|
| Sensor Hardware Supply Voltage (V)             |   5.5 |
| Sensor Hardware Active Mode Current (A)        |   0.1 |
| Sensor Hardware Awakening Current (A)          |  0.15 |
| Sensor Hardware Measurement Current (A)        |  0.12 |
| Sensor Hardware Shut-down Current (A)          |  0.08 |
| Sensor Hardware Sleep Mode Current (A)         |  0.05 |
| Sensor Hardware Awakening Duration (seconds)   |    11 |
| Sensor Hardware Measurement Duration (seconds) |     3 |
| Sensor Hardware Shut-down Duration (seconds)   |     4 |

These are temporary catalog values for the sensor hardware taken from internet for raspberry Pi4 as well as sleepy Pi. These devices require a lot of energy input when the sleeping/awakening schedules oscillates too much, therefore it is better to keep the device in an awake state all the time **for these values**. However in the future, if less energy consuming currents as well as faster processes are provided, device can be put into the sleeping mode between transmissions or measurements.

| LoRaWAN Energy Consumption Details |   Value   |
|:----------------------------------:|:---------:|
| Energy Supply Voltage (V)          |         5 |
| Standby Current (A)                |    0.0014 |
| TX Current (A)                     |     0.028 |
| Sleep Mode Current (A)             | 0.0000015 |
| RX Current (A)                     |    0.0112 |

In the simulation tool, sensor hardware is completely simulated by customly written code. However, LoRaWAN module energy consumption is simulated by the NS-3 LoRaWAN module itself. Therefore the network module requires custom energy consumption inputs. These values were given by the module as examples, and more realistic values are required.

# <a name="ns3-results"></a>Results Obtained from NS-3 Network Simulator

### Packet Attempts (Retransmission Included)
Total number of packet transmission attempts for the end-device. This includes the retransmission of packets as well.

### Successful Transmission Count
Number of successfull transmissions. Indicating that these many number of packets are eventually transmitted and received ACK.

### Transmission Count
Number of transmission attempts. This indicates the number of unique packet transmission attempts.

### Failed Packet Attempts
Number of failed packet transmissions.

**Packet summary:** To give a solid example, if the above 4 values are like this: [658.0, 132.0, 197.0, 526.0], for 197 unique packets, a total of 658 transmission attempts occured. Out of these 658 attempts, 132 unique packets were able to receive ACK eventually. However, due to interferences, losses etc. for the remaining 65 (197-132) packets, as well as the retransmissions for the 132 packets, 526 failed transmissions occured. Since these values are given as the average of N experiments, it might not be as easy to read them from the results file.

### Time Spent for Successful Transmissions (seconds)
Transmission time is calculated for packets which received ACK. This time starts from the initial creation of the packet transmission order, until the time the sensor receives ACK. This value is calculated for each packet and summed up.

### Sent Data Size (byte)
Sum of the byte size of all successful packets.

### Total Energy Consumption (J)
Energy consumption of hardware plus the lorawan module.

### Hardware Sleep Mode Energy Consumption (J)
Energy consumption of hardware while it is sleeping. (low-energy consumption mode)

### Hardware Awakening Mode Energy Consumption (J)
Energy consumption of hardware while it is booting up from sleeping mode into the active mode.

### Hardware Active Mode Energy Consumption (J)
Energy consumption of hardware while it is using the active mode current.

### Hardware Collection Mode Energy Consumption (J)
Energy consumption of hardware while it is collecting measurement data from environment.

### Hardware Shutdown Mode Energy Consumption (J)
Energy consumption of hardware while it is phasing into the sleeping state.

### Lorawan Module Energy Consumption (J)
Energy consumption of the lorawan module.

# <a name="limitations"></a>Limitations
Following are the limitations for both clustering and the network simulation process.

- Network utilizes only a single frequency channel and a single spreading factor.
- Transmission scheduling is done according to a non-realistic time-slot distribution over duty-cycle limitations. This approach lessens the number of signal interference. However, it is not realistic because in real-world such precise timers will probably will not be available. There is also the issue of possible external inteferences which will delay the packets more and mess with timers as well.
- No buildings are included in the simulation (trees, hills, protection for sensor etc.), tree loss is simulated using the path-loss values taken from [1]. In the referenced study, different path loss parameters are calculated for different tree densities, as well as structures.
- Energy consumption currents and durations for the hardware are taken from expensive sensor catalogs, indicating that they are there just for the sake of showing an example. Introducing low-cost sensor parameters will reduce the energy consumption results by a huge margin. 
- Since sensors are always within the range of gateways, they always run in the active current mode, meaning that they do not sleep between each packet transmission or environment measurement attempts.
- **EXTREMELY IMPORTANT** DO NOT assume a one-to-one relationship between gateways and sensors. While clustering tries to create sets according to the provided parameters, it does not mean that sensors do not communicate with the gateways of other clusters. Given different path-loss values, sensors WILL communicate with other gateways from other clusters. Therefore, while processing the NS-3 results, looking at it at individual sensor level will be more appropriate.
- While clustering, there are many different potential gateway points for the sensors in the same cluster set, in order to not introduce randomness and keep things in a static manner, gateway location is taken as the average of the locations of these potential points. Basically sum lat/lon and take average.
- From what I have seen, even with random number seed, lorawan results appears to be same for same parameters. This is something to keep in mind.
- A trivial information is that during clustering shape of earth is taken as sphere not ellipsoid, and distance between lat/lon degrees are both taken as 111km.

# <a name="references"></a>References
[1] Phaiboon, S., and S. Somkuarnpanit. "Mobile path loss characteristics for low base station antenna height in different forest densities." 2006 1st International Symposium on Wireless Pervasive Computing. IEEE, 2006.
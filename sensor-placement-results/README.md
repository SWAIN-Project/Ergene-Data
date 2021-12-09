The output of sensor placement model contianing an ordered list of **30** optimal locaitons is provided in the sensor_placements.csv.

- sensor_placements.csv

    - sensor_key: unique identifier for the sensor (same as used in the gateway placements).
    - sensor_num: each sensor_key is mapped to an integer value [0-74].
    - cost: sensor cost associated with each of 75 locations [0-1]. More details about this parameter are provied below.
    - score: float value signifying the importance of each sensor locaiton; higher the score, more important the location in terms of reconstruction and satisfying the cost constraint. The equation for calculating score is given below: 

         <img src="https://render.githubusercontent.com/render/math?math=score = reconstruction\_score * (1 - cost)" display="block" margin-left="auto" margin-right="auto" width="50%">

## Calculating "sensor cost"

Data corresponding to a unique configurataion (i.e. distance_threshold=6500.0, outlier_threshold=1, remove_intersectoin=0) from sensor_result.csv is used for calculating the sensor cost which is basically the "rate of successful transmission". If a sensor is able to transmit all packets successfully then the cost associated with this sensor would be zero. And one if all transmissions result in failures (no packet transmitted). 

<img src="https://render.githubusercontent.com/render/math?math=cost(sensor\_key) = \frac{(packets \ attempts - successfull \ transmissions)}{packet \ attempts}" display="block" margin-left="auto" margin-right="auto" width="50%">


**NOTE:** The cost of a sensor that does not belong to any cluster is also set to 1.

## Sensor Placement Model

The sensor placement model consists of QR decomposition model which finds the optimal locations using the reconstruction and sensor cost. The training data consists of the measurements (both traditional parameters and micropollutants) from 75 locations and cost calculated above. The model outputs a ranked list of optimal sensor locations in descending order of importance (sensor_key= t1-2 is ranked higher than the rest of 29 locations). 

- Input: A matrix containing measurements from 75 locations and cost vector of size 75.
- Output: Ranked list of 30 optimal sensor locations and their corresponding scores. 

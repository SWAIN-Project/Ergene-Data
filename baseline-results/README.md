The sensor placements results from the proposed (MSPQR) and four baseline algorithms (QR, GP-EN, GP-MI, RAND). 
ModelName_placements.csv contains a list of **30** sensor locations selected by a particualr model whereas, "results_all_methods.csv" 
contains results from all five methods, bascially basically results from all five methods are merged in this single file.

Each file consists of 30 rows (same as the number of sensors selected by the models) and the format of each file is as follows:

ModelName_s_key, ModelName_s_num, ModelName_poll_count, ModelName_detected_polls.

    - ModelName_s_key: unique identifier for each sensor.
    - ModelName_s_num: unique number assigned to each sensor.
    - ModelName_poll_count:  number of pollutants detected at each particular sensor location.
    - ModelName_detected_polls: list of detected pollutants at particular sensor location.


The total number of micropollutants detected from placements consisting of 30 sensors selected by each model are given below:

MSPQR = 1209
QR = 1253
GP-MI = 1113
GP-EN = 1025
RAND = 996
number of pollutants (ground truth) = 1502

This information can be used to calculate different metrics such as accuracy to compare the proposed method with the baselines.

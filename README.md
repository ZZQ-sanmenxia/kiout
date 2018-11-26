## Kalman-IOU Tracker
Python implementation of the Kalman-IOU Tracker.

This tracker is a modified version based on the original [IOU Tracker](https://github.com/bochinski/iou-tracker). Please consider citing their work:

```
@INPROCEEDINGS{1517Bochinski2017,
	AUTHOR = {Erik Bochinski and Volker Eiselein and Thomas Sikora},
	TITLE = {High-Speed Tracking-by-Detection Without Using Image Information},
	BOOKTITLE = {International Workshop on Traffic and Street Surveillance for Safety and Security at IEEE AVSS 2017},
	YEAR = {2017},
	MONTH = aug,
	ADDRESS = {Lecce, Italy},
	URL = {http://elvera.nue.tu-berlin.de/files/1517Bochinski2017.pdf},
	}
```


## Demo
Several demo scripts are included to reproduce the reported results on the [UA-DETRAC](http://detrac-db.rit.albany.edu/)
and the [MOT](https://motchallenge.net/) 16/17 benchmarks.

Basic demo script:
```
$ ./demo.py -h
usage: demo.py [-h] -d DETECTION_PATH -o OUTPUT_PATH [-sl SIGMA_L]
               [-sh SIGMA_H] [-si SIGMA_IOU] [-tm T_MIN]

IOU Tracker demo script

optional arguments:
  -h, --help            show this help message and exit
  -d DETECTION_PATH, --detection_path DETECTION_PATH
                        full path to CSV file containing the detections
  -o OUTPUT_PATH, --output_path OUTPUT_PATH
                        output path to store the tracking results (MOT
                        challenge devkit compatible format)
  -sl SIGMA_L, --sigma_l SIGMA_L
                        low detection threshold
  -sh SIGMA_H, --sigma_h SIGMA_H
                        high detection threshold
  -si SIGMA_IOU, --sigma_iou SIGMA_IOU
                        intersection-over-union threshold
  -tm T_MIN, --t_min T_MIN
                        minimum track length
```

Example for the MOT17-04 sequence (detections can be downloaded [here](https://motchallenge.net/data/MOT17/)):
```
./demo.py -d ../mot17/train/MOT17-04-SDP/det/det.txt -o res/iou-tracker/MOT17-04-SDP.txt
```

### Results from DETRAC Dataset
To reproduce the reported results, download and extract the [DETRAC-toolkit](http://detrac-db.rit.albany.edu/download)
and the detections you want to evaluate. Download links for the EB detections are provided below.
Clone this repository into "DETRAC-MOT-toolkit/trackers/".
Follow the instructions to configure the toolkit for tracking evaluation and set the tracker name in "DETRAC_experiment.m":

```
tracker.trackerName = 'iou-tracker';
```

and run the script.

Note that you still need a working python environment with numpy installed.
You should obtain something like the following results for the 'DETRAC-Train' set:

##### DETRAC-Train Results with EB Detector
| Tracker | Frames | PR-MT | PR-PT  | PR-ML | PR-FP   | PR-FN   | PR-IDs| PR-FM | PR-MOTA | PR-MOTP | PR-MOTAL |
| -------- | ----- | ----- | ------ | ----- | ------- | ------- | ----- | ----- | ------- | ------- | -------- |
| IOU | All        |32.34  |12.88   |20.93  |7958.82  |163739.85|4129.40|4221.89|35.77    |40.81    |36.48     |
|KIOU | All        |37.4   |7.3     |21.5   |8427.5   |148393.9 |422.7  |605.4  | 39.0     |40.7     |39.1      |
|KIOU | 1/2        |34.5   |9.5     |22.1   |6803.5   |155556 |472.8  |599.6  | 38.0     |40.9     |38.1      |
|KIOU | 1/3 | 31.9 | 10.9 | 23.4 | 7611.4 | 160566.9 | 483.2 | 630.9 | 37.0 | 40.9 | 37.1 |
|KIOU | 1/4 | 20.8 | 12.6 | 32.8 | 11163.4 | 192857.4 | 628.1 | 711.1 | 30.8 | 40.9 | 30.9 |


##### DETRAC-Test (Overall) Results
The reference results are taken from the [UA-DETRAC results](http://detrac-db.rit.albany.edu/TraRet) site. Only the best tracker / detector
combination is displayed for each reference method.

| Tracker       | Detector | PR-MOTA | PR-MOTP     | PR-MT     | PR-ML     | PR-IDs   | PR-FM    | PR-FP      | PR-FN      | Speed          |
| ------------- | -------- | ------- | ----------- | --------- | --------- | -------- | -------- | ---------- | ---------- | -------------- |
|CEM            | CompACT  | 5.1\%     |35.2\%     |3.0\%      |35.3\%     |**267.9** |**352.3** |**12341.2** |260390.4    |4.62 fps        |
|CMOT           | CompACT  | 12.6\%    |36.1\%     |16.1\%     |18.6\%     |285.3     |1516.8    |57885.9     | 167110.8   | 3.79 fps     |
|GOG            | CompACT  | 14.2\%    |37.0\%     |13.9\%     |19.9\%     |3334.6    |3172.4    |32092.9     |180183.8    |390 fps         |
|DCT            | R-CNN    | 11.7\%    |38.0\%     |10.1\%     |22.8\%     |758.7     |742.9     |336561.2    |210855.6    |0.71 fps        |
|H<sup>2</sup>T | CompACT  | 12.4\%    |35.7\%     |14.8\%     |19.4\%     |852.2     |1117.2    |51765.7     |173899.8    | 3.02 fps       |
|IHTLS          | CompACT  | 11.1\%    |36.8\%     |13.8\%     |19.9\%     |953.6     |3556.9    |53922.3     |180422.3    |19.79 fps       |
|IOU            | R-CNN    |16.0\%     |**38.3\%** |13.8\%     |20.7\%     |5029.4    |5795.7    |22535.1     |193041.9    |**100,840 fps** |
|IOU            | EB       |19.4\%     |28.9\%     |17.7\%     |18.4\%     |2311.3    |2445.9    |14796.5	  |171806.8    |6,902 fps       |
|**KIOU**       | EB       | **21.1\%** | 28.6\%   | **21.9\%** | **17.6\%** | 462.2  | 712.1    | 19046.8    | **159178.3** |  - |

##### EB detections
The public detections of [EB](http://zyb.im/research/EB/) are not available on the
DETRAC training set and miss some low scoring detections. The EB detections we used for the tables above and our
publication are available here:

* [EB Train](https://tubcloud.tu-berlin.de/s/EtC6cFEYsAU0gFQ/download)
* [EB Test](https://tubcloud.tu-berlin.de/s/oKM3dYhJbMFl1dY/download)


## Contact
If you have any questions or encounter problems regarding the method/code feel free to contact me
at bochinski@nue.tu-berlin.de

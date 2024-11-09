# Anomaly Detection
In this work, we replicate the DCASE challenge, which is well-known in the signal processing community, as it deals with "Detection and Classification of Acoustic Scenes and Events".

> Quoting from the official DCASE challenge website (this is the 2020 edition, the 2024 edition is similar):
>
> Anomalous sound detection (ASD) is the task to identify whether the sound emitted from a target machine is normal or anomalous. Automatically detecting mechanical failure is an essential technology in the fourth industrial revolution, including artificial intelligence (AI)-based factory automation. Prompt detection of machine anomaly by observing its sounds may be useful for machine condition monitoring.
>
> The main challenge of this task is to detect unknown anomalous sounds under the condition that only normal sound samples have been provided as training data. In real-world factories, actual anomalous sounds rarely occur and are highly diverse. Therefore, exhaustive patterns of anomalous sounds are impossible to deliberately make and/or collect. This means we have to detect unknown anomalous sounds that were not observed in the given training data. This point is one of the major differences in premise between ASD for industrial equipment and the past supervised DCASE tasks for detecting defined anomalous sounds such as gunshots or a baby crying.

### Data Description

From the official DCASE challenge website:

> The data used for this task comprises parts of ToyADMOS and the MIMII Dataset consisting of the normal/anomalous operating sounds of six types of toy/real machines. Each recording is a single-channel (approximately) 10-sec length audio that includes both a target machine's operating sound and environmental noise. The following six types of toy/real machines are used in this task:
>
> - Toy-car (ToyADMOS)
> - Toy-conveyor (ToyADMOS)
> - Valve (MIMII Dataset)
> - Pump (MIMII Dataset)
> - Fan (MIMII Dataset)
> - Slide rail (MIMII Dataset)

To make this challenge suitable for the scale of the AML course, we will work on only one machine type (i.e., Slide rail).

### Recording Procedure

The ToyADMOS consists of normal/anomalous operating sounds of miniature machines (toys) collected with four microphones, and the MIMII dataset consists of those of real machines collected with eight microphones. Anomalous sounds in these datasets were collected by deliberately damaging target machines. For simplifying the task, we used only the first channel of multi-channel recordings; all recordings are regarded as single-channel recordings of a fixed microphone. The sampling rate of all signals has been downsampled to 16 kHz. From ToyADMOS, we used only IND-type data that contain the operating sounds of the entire operation (i.e., from start to stop) in a recording. We mixed a target machine sound with environmental noise, and only noisy recordings are provided as training/test data. The environmental noise samples were recorded in several real factory environments. For the details of the recording procedure, please refer to the papers of ToyADMOS and MIMII Dataset.

### Development and Evaluation Datasets

We first define two important terms in this task: **Machine Type** and **Machine ID**. Machine Type means the kind of machine, which in this task can be one of six: toy-car, toy-conveyor, valve, pump, fan, and slide rail. Machine ID is the identifier of each individual of the same type of machine, which in the training dataset can be of three or four.

The figure below shows an overview of our dataset, which consists of a development dataset, an additional training dataset, and an evaluation dataset.

![Dataset Overview](example.png)

- **Development dataset**: Each Machine Type has three or four Machine IDs. Each machine ID's dataset consists of (i) around 1,000 samples of normal sounds for training and (ii) 100-200 samples each of normal and anomalous sounds for the test. The normal and anomalous sound samples in (ii) are only for checking performance therefore the sound samples in (ii) shall not be used for training.
- **Evaluation dataset**: This dataset consists of the same Machine Types' test samples as the development dataset. The number of test samples for each Machine ID is around 400, none of which have a condition label (i.e., normal or anomaly). Note that the Machine IDs of the evaluation dataset are different from those of the development dataset.
- **Additional training dataset**: This dataset includes around 1,000 normal samples for each Machine Type and Machine ID used in the evaluation dataset. The participants can also use this dataset for training.

### Description of the DCASE Data

Note that the Machine IDs used in the development dataset are different from those used in the evaluation dataset to avoid leaking label information during the training. The Development dataset is used to develop a model and tune hyper-parameters. Then, the model should be re-trained on the Additional training dataset before making predictions on the Evaluation dataset.

## Metrics
Participants will calculate anomaly scores for each chosen test sample, instead of a boolean decision result (anomalous, not anomalous). The anomaly score takes a large value when the input signal is detected to be anomalous, and vice versa.
Ideally, we strongly suggest to use as a metric for the evaluation of this competition the Area Under Curve (AUC). 
# Motion-Compensated-Pulse-Rate-Estimation
### **Introduction**

A core feature that many users expect from their wearable devices is pulse rate estimation. Continuous pulse rate estimation can be informative for many aspects of a wearer's health. Pulse rate during exercise can be a measure of workout intensity and resting heart rate is sometimes used as an overall measure of cardiovascular fitness. In this project a pulse rate estimation algorithm was implemented for a wrist-wearable device.

**Background**

#### **Physiological Mechanics of Pulse Rate Estimation**

Pulse rate is typically estimated by using the PPG sensor. When the ventricles contract, the capillaries in the wrist fill with blood. The (typically green) light emitted by the PPG sensor is absorbed by red blood cells in these capillaries and the photodetector will see the drop in reflected light. When the blood returns to the heart, fewer red blood cells in the wrist absorb the light and the photodetector sees an increase in reflected light. The period of this oscillating waveform is the pulse rate.

![](ppg.png)

PPG Sensor on Blood Flow

However, the heart beating is not the only phenomenon that modulates the PPG signal. Blood in the wrist is fluid, and arm movement will cause the blood to move correspondingly. During exercise, like walking or running, we see another periodic signal in the PPG due to this arm motion. Our pulse rate estimator has to be careful not to confuse this periodic signal with the pulse rate.

We can use the accelerometer signal of our wearable device to help us keep track of which periodic signal is caused by motion. Because the accelerometer is only sensing arm motion, any periodic signal in the accelerometer is likely not due to the heart beating, and only due to the arm motion. If our pulse rate estimator is picking a frequency that's strong in the accelerometer, it may be making a mistake.

All estimators will have some amount of error. How much error is tolerable depends on the application. If we were using these pulse rate estimates to compute long term trends over months, then we may be more robust to higher error variance. However, if we wanted to give information back to the user about a specific workout or night of sleep, we would require a much lower error.

**Code Description**

Python libraries were used: glob, NumPy, scipy. The code provided below estimates the pulse rate from the PPG signal and a 3-axis accelerometer. As an output, we get an aggregate error metric based on confidence estimates. All signals are filtered by the band pass algorithm, the 40-240BPM range was used for creating the pass band. When the dominant accelerometer frequency equals the PPG, we pick the second-strongest one.

**Data Description**

The dataset comes from a biomedical paper: Z. Zhang, Z. Pi, B. Liu, TROIKA: A general framework for heart rate monitoring using wrist-type photoplethysmographic signals during intensive physical exercise.

PPG signals were recorded from wristband tracker among people from 18 to 35 years old. The acceleration signal was also recorded from the wrist by a three-axis accelerometer during different physical activities: resting, walking, and running. All signals were sampled at 125Hz and sent via Bluetooth to the computer.

Possible shortcomings: As all participants were males, so there could be a hidden bias. In addition the data were obtained only from young people, to be more confident in our algorithm it should be tested on a wider range including old people and kids/teenagers.

**Algorithm Description**

The algorithm estimates the pulse rate from the PPG signal. The loaded signal values are filtered using the Bandpass algorithm. The 40-240BPM range was used for creating a pass band, in addition, Fourier transformation was applied. We compute the largest FFT coefficient, in case the dominant accelerometer frequency is equal to the PPG, we pick the second strongest frequency as the best estimate. To be precise: after getting predicion we compare it with acc signals to be confident that this value doesn't come from the noise (movement).

As an output of the model, we are getting the MAE (mean absolute error) of the prediction and ground-truth heart rate array and array of confidence scores. A measure of confidence is based on how much energy in the frequency spectrum is concentrated near an estimation of pulse rate. Based on the confidence level, we can evaluate whether the estimated pulse rate is accurate.

Common failure models: Shouldn't be applied without fine-tuning to the other datasets that are not very similar to the given one.

**Algorithm Performance**

Algorithm performs quite well: MAE for training is 15.72, on the test: 9.13 It performed even better on test data than on the train, but this is not common in general, potentially there data in the test dataset was too simple compared to the train data.

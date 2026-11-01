# Spread and six-pin deviation calculations in 9.8.65

## Primary spread values

CURRENT SPREAD = highest of the six currents minus lowest of the six currents.
AVG SPREAD = arithmetic mean of those individual sample gaps since Start/Reset.
MAX SPREAD = largest individual sample gap since Start/Reset.

The highest and lowest pins are found again at every sample. Average spread
is the average of the gaps, not the gap between session-averaged pin currents.
Normal readings arrive roughly once per second; missing samples are not invented.

The main cards, live graph, selected replay-point spread, saved-log average
and summary exports all use this highest-minus-lowest definition. Top 5 peaks,
event averages, ranking, qualification, replay windows, alarms, limits and
hysteresis retain their established gap calculations.

## Small deviation captions

The small grey captions describe all six pin currents:

| Card | Small caption | Meaning |
| --- | --- | --- |
| CURRENT SPREAD | Current deviation: 0.021 A | This sample's six-pin mean absolute deviation |
| AVG SPREAD | Average deviation: 0.021 A | Mean of valid per-sample deviations since Start/Reset |
| MAX SPREAD | Max deviation: 0.021 A | Highest valid per-sample deviation since Start/Reset |

For each sample, compute the six-pin mean absolute deviation: find the average current
m, find each pin's absolute distance from m, and average the six distances.

    deviation = (|I1-m| + |I2-m| + |I3-m| + |I4-m| + |I5-m| + |I6-m|) / 6

These values are in amperes, not percentages or average pin currents. The
primary spread headlines remain pin gaps. Deviation summarises distances
from the mean using every pin, while a gap uses only the two extremes.

The session average is the arithmetic mean of individual valid sample
deviations, rather than the deviation of six time-averaged pin currents.
Changing which pins are high cannot cancel earlier imbalance. A zero counts;
an invalid deviation is excluded. Missing samples are not invented. Reset
clears the mean, sample count and maximum. The aggregate retains all samples
in the session, including those no longer present in the live graph.

Maximum deviation is tracked independently from Max spread. For example,
6/8/8/8/8/10 A has gap 4 A and deviation 0.667 A; 7/7/7/9/9/9 A has gap 2 A
and deviation 1.000 A. Their largest gap and largest deviation occur in
different samples. No Top 5 ranking or alarm rule uses the new captions.

## Recording compatibility

JSON spread/average_spread/peak_spread fields keep their original gap meanings.
The v9.8.61+ CSV column Spread A (6-pin mean absolute deviation) remains an
explicitly named per-sample secondary value to keep the existing schema stable.
Pin Gap A retains the original gap and column position. The PSU port column
introduced in v9.8.62 is retained. Old recordings remain readable; analysis
recomputes sample gaps from their six raw currents without changing the file.
Summary CSV Average spread A now means session average gap again.

| Currents in A | Current spread | Current deviation |
|---|---|---|
| 8, 8, 8, 8, 8, 8 | 0.000 A | 0.000 A |
| 7, 8, 8, 8, 8, 9 | 2.000 A | 0.333 A |
| 7, 7, 7, 9, 9, 9 | 2.000 A | 1.000 A |

These are descriptions of measured current sharing, not calibrated connector
health or temperature measurements.

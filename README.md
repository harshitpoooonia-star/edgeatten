# EdgeAtten

Semester 5 IoT project at Bennett University: a study of multi-stage attention for securing condition-based monitoring in Industrial IoT, run at the edge.

**Status: design only.** This repo holds the project design and presentation. There is no implementation yet, and the numbers below are the base paper's, not mine.

## The problem

IIoT sensors on a plant report pressure, flow, temperature and vibration so faults get caught before a breakdown. Those readings can be spoofed, biased or replayed. A diagnosis model fed bad data raises false alarms or misses real faults. Sending raw process data to the cloud for heavier checks adds latency and leaks proprietary plant data.

## The approach

One compact attention model with three heads, running on-premise:

1. **Detect** whether a sensor stream is under attack
2. **Correct** the compromised channels by reconstructing trustworthy values
3. **Diagnose** the condition of the cooler, valve, pump and accumulator from the cleaned signal

Temporal attention finds when tampering or degradation starts. Cross-sensor attention uses the physics of the rig (pressure, flow and motor power have to agree) so a spoofed channel gets a low weight instead of polluting the output.

Training uses a joint loss:

```
L = λ1 · BCE(detect) + λ2 · MSE(correct) + λ3 · CrossEntropy(diagnose)
```

## Planned pipeline

| Step | What happens |
|---|---|
| Data | ZeMA hydraulic rig: 17 sensors, 2,205 load cycles of 60 s, labels from `profile.txt` |
| Preprocessing | Resample 100, 10 and 1 Hz channels to one rate, sliding windows, z-score |
| Attack simulation | Bias injection, noise and spikes, replay of stale segments |
| Training | Temporal plus cross-sensor attention, three heads, joint loss |
| Evaluation | Accuracy, precision, recall, F1 on clean and attacked test sets |
| Deployment | Inference on a Raspberry Pi or Jetson-class board |

## Base paper results

Reported in the base paper, not reproduced here:

| Setting | Accuracy |
|---|---|
| EdgeAtten, clean data | 96% |
| EdgeAtten, under attack | above 92% |
| Traditional methods, under attack | below 70% |

## Dataset

T. Schneider, S. Klein, M. Bastuck, "Condition monitoring of hydraulic systems Data Set at ZeMA," Zenodo, 2018, doi:10.5281/zenodo.1323611 (CC BY 4.0).

## References

1. EdgeAtten: Multi-Stage Attention for IIoT Anomaly Detection, Correction, and Diagnosis, IEEE Internet of Things Journal.
2. Schneider, Klein, Bastuck, ZeMA hydraulic dataset, Zenodo 2018.
3. Helwig, Pignanelli, Schütze, "Condition Monitoring of a Complex Hydraulic System Using Multivariate Statistics," IEEE I2MTC 2015.
4. Helwig, Schütze, "Detecting and compensating sensor faults in a hydraulic condition monitoring system," SENSOR 2015.
5. Dong et al., "Real-time fault detection for IIoT facilities using GA-Att-LSTM based on edge-cloud collaboration," Frontiers in Neurorobotics, 2024.
6. "DyEdgeGAT: Dynamic Edge via Graph Attention for Early Fault Detection in IIoT Systems."
7. Vaswani et al., "Attention Is All You Need," NeurIPS 2017.

The full deck is in `docs/EdgeAtten_presentation.pptx`.

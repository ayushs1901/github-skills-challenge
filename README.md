# GitHub Challenge

<img src="https://octodex.github.com/images/Professortocat_v2.png" align="right" height="200px" />

Hey there!

Your challenge is ready.
Follow the instructions provided for this challenge and complete the required tasks in this repository.

Make sure your work is committed and pushed to your repository before submission.

Good luck!


---

&copy; 2025 GitHub &bull; [Code of Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/code_of_conduct.md) &bull; [MIT License](https://gh.io/mit)

## Task 1 - 


There a payment service is monitored, which handles customer transactions and is expected to remain responsive and stable under normal load. The operational problem being addressed is degraded performance and service disruption caused by abnormal latency, timeout errors, and high CPU and memory usage during payment processing. These symptoms can lead to failed transactions and reduced customer trust.


AIOps is helping us to moniter the whole  payment system and looking for any system failure in case of a payment being failed.



## Task 2 -


`response_time_ms`, `cpu_percent`, and `memory_percent` represents metrics. 


`log_level` and `message` represents log fields.


Timestamps are used to get the information about when the error or the fault happened in our service. They are spaced one minute apart. so the records can be reviewed chronologically to identify stable, degraded, and recovering periods.



The observations with "log_level"= "INFO" appear to represent normal behaviour. The observations from 10:00-10:04 and 10:07-10:09 appear normal. They have `INFO` logs stating that payment requests were processed successfully, response times of 120-150 ms, CPU usage of 42-50%, and memory usage of 51-57%.


The observations with "log_level"= "ERROR" appear to represent unusual behaviour. The observations at 10:05 and 10:06 appear unusual. Response time increases to 610 ms and 640 ms, CPU usage rises to 75% and 94%, and memory usage rises to 70% and 91%. Both records have `ERROR` logs reporting a payment service timeout and a database connection timeout. Together, these observations indicate a short period of service degradation or a dependency problem during payment processing.

## Task 3 - Identify Anomalies

The provided `AnomalyDetector` was used with its configured thresholds: response time above 500 ms, CPU above 80%, and memory above 80%. The detector also treats an `ERROR` log level as an anomaly reason. The workflow processed all 10 records and produced two readable anomaly events:

- `2026-09-20T10:05:00`: `payment-service` was flagged for high response time (610 ms) and an error log, `Payment service timeout`. CPU was 75% and memory was 70%, so neither exceeded its threshold.
- `2026-09-20T10:06:00`: `payment-service` was flagged for high response time (640 ms), high CPU utilization (94%), high memory utilization (91%), and an error log, `Database connection timeout`.

No expected anomaly was missed: both `ERROR` observations were detected. No normal event was incorrectly flagged: all eight `INFO` observations returned no anomaly. One limitation is that the detector uses fixed thresholds, so a gradual change in the service baseline or a service-specific workload could be missed or could require different thresholds.

## Task 4 - Verify the AIOps Event Flow

The provided pipeline was executed with `python3 src/aiops_pipeline.py`. The result was `Records processed: 10`, `Anomalies detected: 2`, and `Events consumed: 2`.

The complete flow is:

1. The `AnomalyDetector` examines each operational record and creates an anomaly event for the records at 10:05 and 10:06.
2. The `EventProducer` receives each event and publishes it.
3. The producer publishes to the in-memory `EventTopic` named `service-events`.
4. The `EventConsumer` reads both events from that same topic.
5. The consumer processes the received message list by returning it from `consume()`.
6. The pipeline receives those consumed events as its downstream AIOps result and prints their service, timestamp, type, and reasons.

This verifies that an anomaly event travels through the detector, producer, topic, consumer, and downstream pipeline without being lost.

## Task 5 - Investigate and Correct the Workflow

The original workflow contained two issues:

1. **AnomalyDetector:** the log rule checked for `WARNING`, but the operational data uses `ERROR` for concerning events. The rule was corrected to check `log_level == "ERROR"`. Rerunning the detector produced two anomaly events, at 10:05 and 10:06.
2. **EventConsumer and EventTopic:** the producer published to `service-events`, while the consumer was connected to a separate `anomaly-events` topic. The consumer was corrected to use the producer's `service-events` topic. Rerunning the producer and consumer delivered both events, with their contents preserved.

These corrections use the existing detector, producer, topic, and consumer components. Focused verification confirmed `detector_events=2`, `consumer_received=2`, and `event_identity_preserved=True`.

## Task 6 - Execute the End-to-End Pipeline

The corrected pipeline was executed with `python3 src/aiops_pipeline.py` and produced:

```text
Records processed: 10
Anomalies detected: 2
Events consumed: 2
```

Stage-by-stage verification confirmed:

- Operational data processed: `10` records.
- Anomalous behaviour detected: records at 10:05 and 10:06.
- Anomaly events generated: `2` events with type `ANOMALY`.
- Events published: `2` events published to `service-events`.
- Events consumed: `2` events received by the consumer.
- Events processed successfully: consumed event payloads matched the generated events.
- Final AIOps output: `payment-service` timeout and database connection timeout issues were reported with their metric and log reasons.

The verified flow is `Operational Data -> Anomaly Detection -> Event -> Producer -> Topic -> Consumer -> AIOps`.

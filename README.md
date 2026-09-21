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

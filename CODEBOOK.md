Below is a **complete, ready-to-drop-in markdown code-book** for the two sample files you shared.
Feel free to rename sections or add real summary statistics once the full datasets are in the public repo.

---

# PROCEED Code Book

# Dynamic Resource-Usage & Scheduler-Activity Datasets

| Field                      | Value                                                                          |
| -------------------------- | ------------------------------------------------------------------------------ |
| **Study title**            | *PROCEED – Predictive Resource-Usage Characterisation of Extreme-Edge Devices* |
| **Principal Investigator** | Ruslan Kain (Queen’s University)                                               |
| **Contact**                | [r.kain@queensu.ca](mailto:r.kain@queensu.ca)                                  |
| **Version / date**         | v 0.1 · 2025-07-02                                                             |
| **Citation**               | See README → BibTeX                                                            |
| **Licence**                | Code = MIT · Data = CC-BY-4.0                                                  |
| **DOI**                    | *TBA (Borealis deposit)*                                                       |

---

## Table of Contents

1. Methodology overview
2. **Dataset A** Resource-usage sample (`*_res_usage_data_*.csv`)

   * Variable dictionary
3. **Dataset B** Scheduler-activity sample (`*_activity_data_tasks_*.csv`)

   * Variable dictionary
4. Missing-data conventions
5. Change log

---

## 1 Methodology overview  *(abridged)*

Data were generated with **DRUDGE**, which automates a sequence of foreground
applications (Doom, YouTube, AR image processing, Duino-coin mining, Idle) on
RPi 4B and Jetson Nano devices.
Every `5 s` the **Usage** thread records \~60 resource metrics via *psutil* and
custom NVML probes.  Ground-truth **state** is the currently active
application.  Heterogeneity is induced by DVFS (600–1800 MHz) and
bandwidth-capping (`tc`) per device.

---

## 2 Dataset A – Resource-usage file

`worker1-rpi8_1800_12000_res_usage_data_pattern_long-chunks_108000sec.csv`

| Var name                                                   | Label / description                           | Level †         | Unit / format          | Values / labels                          | Notes                            |
| ---------------------------------------------------------- | --------------------------------------------- | --------------- | ---------------------- | ---------------------------------------- | -------------------------------- |
| `time_stamp`                                               | Wall-clock timestamp                          | String          | `DD-MM-YYYY HH:MM:SS`  | —                                        | Parsed to UTC                    |
| `time`                                                     | UNIX epoch (float)                            | Ratio           | Seconds                | —                                        | Sync w/ `time.time()`            |
| `cpu_freq`                                                 | CPU clock                                     | Ratio           | MHz                    | —                                        | From `/sys/.../scaling_cur_freq` |
| `cpu`                                                      | CPU utilisation                               | Ratio           | %                      | 0–100                                    | 1-sec window                     |
| `cpu_user_time`                                            | CPU time in user mode                         | Ratio           | Seconds                | —                                        | Cumulative                       |
| `cpu_system_time`                                          | CPU time in kernel                            | Ratio           | Seconds                | —                                        | Cumulative                       |
| `cpu_idle_time`                                            | CPU idle time                                 | Ratio           | Seconds                | —                                        | Cumulative                       |
| `memory`                                                   | RAM utilisation                               | Ratio           | %                      | 0–100                                    | `psutil.virtual_memory()`        |
| `swap_memory`                                              | Swap utilisation                              | Ratio           | %                      | 0–100                                    | 0 on Pi (no swap)                |
| `net_sent`                                                 | Bytes sent                                    | Ratio           | Bytes                  | —                                        | Cumulative interface total       |
| `net_recv`                                                 | Bytes received                                | Ratio           | Bytes                  | —                                        |                                  |
| `net_upload_rate`                                          | Upload rate                                   | Ratio           | Bytes / sec            | —                                        | Δ over sampling‐interval         |
| `net_download_rate`                                        | Download rate                                 | Ratio           | Bytes / sec            | —                                        |                                  |
| `1-min_avg_load`                                           | Load-avg 1 min                                | Ratio           | Runnable+blocked tasks | —                                        | Linux `os.getloadavg()`          |
| `5-min_avg_load`                                           | Load-avg 5 min                                | Ratio           | —                      | —                                        |                                  |
| `15-min_avg_load`                                          | Load-avg 15 min                               | Ratio           | —                      | —                                        |                                  |
| `disk`                                                     | Disk busy % (overall)                         | Ratio           | %                      | 0–100                                    | Aggregate                        |
| `disk_read_bytes`                                          | Disk read                                     | Ratio           | Bytes                  | —                                        | Cumulative                       |
| `disk_write_bytes`                                         | Disk write                                    | Ratio           | Bytes                  | —                                        | Cumulative                       |
| `disk_read_time`                                           | Disk read ms                                  | Ratio           | ms                     | —                                        | From `/proc/diskstats`           |
| `disk_write_time`                                          | Disk write ms                                 | Ratio           | ms                     | —                                        |                                  |
| `disk_busy_time`                                           | Disk busy ms                                  | Ratio           | ms                     | —                                        |                                  |
| `temp`                                                     | CPU temperature                               | Ratio           | °C                     | —                                        | `vcgencmd` / `tegra`             |
| `wifi_freq`                                                | Wi-Fi freq                                    | Nominal         | MHz                    | e.g. `433.3`                             | 0 on Ethernet                    |
| `bit_rate`                                                 | Wi-Fi bit-rate                                | Ratio           | Mb / s                 | —                                        |                                  |
| `link_quality`                                             | Wi-Fi link quality                            | Ratio           | —                      | 0–`link_quality_max`                     |                                  |
| `link_quality_max`                                         | Wi-Fi link quality max                        | Ratio           | —                      | —                                        |                                  |
| `signal_level`                                             | RSSI                                          | Interval        | dBm                    | —                                        |                                  |
| `gpu`                                                      | GPU util                                      | Ratio           | %                      | 0–100                                    | 0 on RPi without VC4             |
| `gpu_temp`                                                 | GPU temperature                               | Ratio           | °C                     | —                                        | Jetson only                      |
| `state`                                                    | Ground-truth usage state                      | Nominal         | String                 | *idle*, *game*, *stream*, *ar*, *mining* | Key target variable              |
| `cpu_percent_state` … `io_counters_write_chars_state_diff` | **State-isolated metrics** (prefixed columns) | Ratio           | unit as base metric    | —                                        | Only resources of target PID(s)  |
| `task`                                                     | Placeholder for task-ID                       | Nominal         | String                 | `net`, `benchmark`, `task`, …            | Filled during scheduling         |
| *(remaining columns)*                                      | See header                                    | Ratio / Nominal | —                      | —                                        | All raw psutil attributes        |

† *Level*: **Nominal** = unordered categories; **Ordinal** = ordered categories; **Interval/Ratio** = continuous (here labelled as “Ratio” if true zero exists).

> **Summary statistics** (min, max, mean, SD, N) can be generated with
> `python scripts/describe.py data/*.csv` once the full files are uploaded.

---

## 3 Dataset B – Scheduler-activity file

`worker_activity_data_tasks_min_qos_reactive_avg_log_reg_…csv`

| Var name                                              | Label                                                                                 | Level         | Unit        | Values / labels                    | Notes                            |
| ----------------------------------------------------- | ------------------------------------------------------------------------------------- | ------------- | ----------- | ---------------------------------- | -------------------------------- |
| `ip`                                                  | Worker IP address                                                                     | Nominal       | IPv4        | —                                  |                                  |
| `worker_name`                                         | Device identifier                                                                     | Nominal       | String      | `worker12-rpi4`                    | `<host>-<model>`                 |
| `times`                                               | Heart-beat timeseries                                                                 | Repeat / list | UNIX epochs | —                                  | JSON array                       |
| `time_processing_diff`                                | Time from HB to scheduler                                                             | Ratio         | Seconds     | —                                  | Latency                          |
| `time_sent`, `time_received`                          | Time stamps                                                                           | Ratio         | Seconds     | —                                  | UNIX                             |
| `time_decision`                                       | Scheduling latency                                                                    | Ratio         | Seconds     | —                                  | Decision phase                   |
| `time_exec`                                           | Scheduler decision time                                                               | Ratio         | Seconds     | —                                  | When task assigned               |
| `time_allocation_diff`                                | Δ since previous allocation                                                           | Ratio         | Seconds     | —                                  |                                  |
| `actual_label`                                        | Observed state on worker                                                              | Nominal       | 0–4         | see mapping ↓                      | Encoded                          |
| `predicted_label`                                     | Predicted state                                                                       | Nominal       | 0–4         | —                                  | From LR or HSMM                  |
| `QoS`                                                 | Task runtime                                                                          | Ratio         | Seconds     | —                                  | Target metric                    |
| `estimated_QoS`, `lower_bound_QoS`, `upper_bound_QoS` | Reactive window                                                                       | Ratio         | Seconds     | —                                  | Scaled from benchmarks           |
| `allocated_task_type`                                 | Benchmark / real task class                                                           | Nominal       | String      | `cpu_mm`, `mem`, `net`, `mixed`, … |                                  |
| `allocated_tasks`                                     | Count of task slices                                                                  | Ratio         | Integer     | —                                  | For embarrassingly parallel jobs |
| *(all `actual_*` & `predicted_*` resource columns)*   | Same definition as in Dataset A but **only for the process group executing the task** | Ratio         | —           | —                                  |                                  |

### State encoding table

| Code | Label                 |
| ---- | --------------------- |
| `0`  | Idle                  |
| `1`  | Game (Doom)           |
| `2`  | Stream (YouTube)      |
| `3`  | Mining (Duino-coin)   |
| `4`  | AR (image homography) |

---

## 4 Missing-data conventions

| Symbol                   | Meaning                                              |
| ------------------------ | ---------------------------------------------------- |
| blank / empty cell       | Not collected at this time slice (e.g., GPU on RPi)  |
| `0` in \*\_state columns | Metric not applicable (e.g., swap on Pi)             |
| `-9999` *(reserved)*     | Sensor read error (none in current sample)           |
| `NaN`                    | Introduced after filtering or join; treat as missing |

---

## 5 Change log

| Date       | Version | Change                                      |
| ---------- | ------- | ------------------------------------------- |
| 2025-07-02 | 0.1     | Initial code-book drafted from sample files |

---

### Notes

* Measurement level tags follow the **DDI-Codebook** 2.5 conventions.
* All timestamps are recorded in the device’s local time (default = UTC); convert as needed.
* Weighting variables are *not* used—each row is an equally-spaced 5 s observation.

---


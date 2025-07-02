<!--
 ██████╗ ██████╗  ██████╗ ███████╗███████╗██████╗ ███████╗██████╗
██╔════╝ ██╔══██╗██╔════╝ ██╔════╝██╔════╝██╔══██╗██╔════╝██╔══██╗
██║  ███╗██████╔╝██║  ███╗█████╗  █████╗  ██████╔╝█████╗  ██████╔╝
██║   ██║██╔══██╗██║   ██║██╔══╝  ██╔══╝  ██╔══██╗██╔══╝  ██╔══██╗
╚██████╔╝██║  ██║╚██████╔╝███████╗███████╗██║  ██║███████╗██║  ██║
 ╚═════╝ ╚═╝  ╚═╝ ╚═════╝ ╚══════╝╚══════╝╚═╝  ╚═╝╚══════╝╚═╝  ╚═╝
-->

# PROCEED  
**P**redictive **R**esource-Usage **O**ptimisation & **Ch**aracterisation of **E**xtreme-**E**dge **D**evices

> **Reproducible emulator + dataset + scheduler** for bursty workloads on Raspberry Pi / Jetson Nano clusters.  
> Forecasts multi-step resource states (HDP-HSMM ∣ log-reg) and allocates tasks *dynamically* & *reactively*.

---

## ✨ Features
* **DRUDGE** – pythonic engine that plays Doom, streams YouTube, mines Duino-coin & more to generate labelled usage traces.  
* **5-step predictors** – HDP-HSMM (non-Markovian) and lightweight multinomial logistic regression.  
* **Static / Dynamic / Reactive** task allocators with benchmark-guided QoS windows.  
* **Dashboard** – real-time charts & knobs, built with Plotly Dash.  
* **444 MB dataset** – 768 h of 5 s-granularity resource metrics on heterogeneous Pi 4Bs & Jetson Nanos.  

---

## 🚀 Quick-start

```bash
# ❶ Clone (public repo)
git clone https://github.com/<your-user>/proceed.git
cd proceed

# ❷ Resolve deps (Python 3.8 via conda)
conda env create -f environment.yml
conda activate proceed

# ❸ Run a minimal demo (pattern workload on localhost)
python examples/run_drudge.py --config configs/rpi_pattern.yaml
```
---
<details> <summary> Or via Docker </summary>

docker build -t axon-worker:latest \
             -f Dockerfile.worker \
             --build-arg JETSON_NANO=true .
docker run -it --rm --net=host axon-worker:latest
</details>
---

## Planned repository layout (subject to change when code is pushed)
```shell
proceed/
├── environment.yml              # Conda env (Python 3.8)
├── requirements.txt             # pip extras
├── Dockerfile.worker            # RPi / Jetson support
├── src/
│   ├── drudge/                  # Dynamic usage generator
│   ├── models/                  # HDP-HSMM, log-reg, HBLED (DL)
│   └── scheduler/               # Allocators & dashboard backend
├── configs/                     # YAML / JSON presets
├── data/                        # <small demo CSVs go here>
├── examples/                    # End-to-end notebooks & scripts
└── docs/                        # Figures & API docs
```
### Heads-up: The private tree you saw earlier will be slimmed, cleaned and copied here.
### Update the snippet above once the final structure is stable.

## ⚙️ Installation notes

| Item             | Value                                                                                        |
| ---------------- | -------------------------------------------------------------------------------------------- |
| Python           | 3.8 (miniforge 3)                                                                            |
| OS               | Ubuntu 20.04 / Debian; tested on ARM (aarch64) & x86-64                                      |
| Key APT packages | `xvfb`, `wireless-tools`, `cpufrequtils`, `hdf5`, `chocolate-doom`, `ffmpeg`, …              |
| Jetson Nano      | JetPack 5.x + CUDA 11; TensorFlow 2.12 + nv23.06 wheel                                       |
| Optional extras  | `pybasicbayes`, `pyhsmm` (c-extension build instructions in `installation_instructions.txt`) |

## 🔧 Configuration
### Minimal YAML (RPi pattern demo)
### configs/rpi_pattern.yaml
```
device: raspberrypi
states: [idle, game, stream, ar, mining]
interval: 5            # seconds between samples
total_length: 108000   # 30 h run
cpu_freq: [600, 1800]  # DVFS sweep (MHz)
bandwidth: 12000       # kbit/s cap via tc
sequence_type: pattern # or "random"
dashboard: true
```
### All JSON config variants live in worker_parameters_config*.json; run python drudge.py --help for every flag.

## 📊 Dataset overview

| Property | Value                                         |
| -------- | --------------------------------------------- |
| Files    | 74 CSVs (444 MB)                              |
| Duration | 768 h (≈ 550 k rows)                          |
| Devices  | 4× RPi 4B (2-8 GB, 1.2-1.8 GHz) & Jetson Nano |
| States   | Idle, Game, Stream, Augmented-Reality, Mining |
| Sampling | 5 s                                           |

## Data dictionary – raw usage CSV (*_res_usage_data_*.csv)

| Column          | Type              | Unit                  | Description               |
| --------------- | ----------------- | --------------------- | ------------------------- |
| `time_stamp`    | string → datetime | `DD-MM-YYYY HH:MM:SS` | Wall-clock                |
| `time`          | float             | s                     | `time.time()`             |
| `cpu_freq`      | float             | MHz                   |                           |
| `cpu`           | float             | %                     | Aggregate CPU utilisation |
| `cpu_user_time` | float             | s                     |                           |
| …               | …                 | …                     |                           |
| `state`         | enum              | label                 | {idle, game, …}           |

## 🖥 Dashboard
### Launch locally with
```shell
python src/scheduler/dashboard_app.py
```
## 📜Citation 
```shell
@dataset{proceed2025,
  author  = {Kain, Ruslan},
  title   = {PROCEED Dynamic Resource-Usage Dataset},
  year    = {2025},
  doi     = {10.XXXXX/zenodo.XXXXXXX}
}
```
## 🤝 Contributing
> Pull requests and issues welcome!
> See CONTRIBUTING.md for coding style, branch naming and pre-commit hooks

## 🗒️ Acknowledgements
* Queen’s University


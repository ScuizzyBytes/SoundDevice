# 🔊 SoundDevice Telemetry Module

A lightweight, non-blocking Python module designed for capturing audio telemetry, monitoring active system sound output levels, and querying sound card interfaces. Built for seamless integration into real-time dashboards and HUD interfaces without causing UI micro-stutters.

## 📐 Key Capabilities

* **Real-Time Volume Sampling:** Inspect active output volume levels (peak meters) across default system channels.
* **Audio Device Enumeration:** Query available input/output hardware interfaces (microphones, speakers, Virtual Audio Cables).
* **Async & Thread-Safe:** Optimized to run inside background workers, feeding telemetry queues to CustomTkinter / PySide interfaces.

## 📁 Repository Structure

```
SoundDevice/
├── .gitignore
├── README.md
└── SoundDevice.py      # Core audio monitoring module
```

## ⚡ Data Payload Example

When queried inside a background thread loop, `SoundDevice.py` yields structured metrics ready for UI dispatch:

```json
{
  "active_device": "Realtek High Definition Audio",
  "volume_db": -12.4,
  "volume_peak_percent": 68.0,
  "is_muted": false,
  "status": "ONLINE"
}
```

## 🛠️ Prerequisites & Installation

Ensure you have the required audio interface bindings installed:

```bash
pip install sounddevice numpy
```

> **Note:** On Linux systems, PortAudio headers are required:
> ```bash
> sudo apt-get install libportaudio2
> ```

## 🔄 Integration Architecture

```
[ OS Audio Subsystem / PortAudio ]
               │
               ▼
       [ SoundDevice.py ]  <--- (Polling Peak Volume / Active Sink)
               │
               ▼
      [ Background Worker ]  --->  [ UI Queue / Callback ]  --->  [ Cyberpunk HUD ]
```

1. Instantiate `SoundDevice` during background worker initialization.
2. Poll peak levels on every background tick.
3. Dispatch telemetry to the main UI thread via `self.after(0, callback, data)` or `queue.Queue`.

## 🚀 Usage Example

```python
from SoundDevice import AudioMonitor

monitor = AudioMonitor()
active_metrics = monitor.get_audio_telemetry()
print(active_metrics)
```

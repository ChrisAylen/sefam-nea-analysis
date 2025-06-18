# CPAP Session Viewer - README

## Overview

The CPAP Session Viewer is a standalone browser-based visualisation tool designed to read and graph nightly pressure, leak, and event data from Sefam CPAP machines (e.g., Sefam Néa Auto) using files extracted from the machine's SD card.

It requires no installation or internet connection and runs entirely in the browser.

---

## Usage

1. Insert the CPAP SD card into your computer.
2. Copy the folder to your computer.
3. Open the `viewer.html` file in your browser.
4. Click **"Choose Folder"** and select the root folder containing the Sefam data (e.g., `SEFAM/` or your mounted SD directory).
5. Use the dropdown to select a logical night.
6. View:

   * Pressure chart
   * Leak chart
   * Events per hour
6. Use the **Smoothing** dropdown to change the averaging period (raw, 1 min, 5 min, 1 hour).

---

## Folder and File Format (Sefam Devices)

Sefam CPAP machines store data in folders named like:

```
/DATA_00001
/DATA_00002
```

Each folder typically includes the following files:

| File Extension | Description            | Used For        |
| -------------- | ---------------------- | --------------- |
| `.PRE`         | Pressure data (binary) | Pressure graph  |
| `.LK`          | Leak data (binary)     | Leak graph      |
| `.STS`         | Event data (optional)  | Events per hour |

### Interpretation:

* `.PRE`: Byte values (scaled 10:1) representing pressure in cmH2O.
* `.LK`: Byte values representing leak in L/min.
* `.STS`: Byte values, some of which represent event codes (e.g., apnoea, hypopnoea).

---

## File Parsing Logic

### Pressure (.PRE):

* Each byte is divided by 10 to convert to cmH2O.
* Values outside configured limits (default: 4–15) are nullified.
* Sample rate assumed: **5 Hz**

### Leak (.LK):

* Values taken directly as L/min.
* Sample rate assumed: **1 Hz**

### Events (.STS):

* Treated as sampled every second.
* Event detection logic assumes transition from non-event to known event codes (1, 2, 3).
* Hourly count normalised to events/hour.

---

## Session Validation

A session is only included if:

* `.PRE` and `.LK` files exist
* Duration > 90 samples
* Standard deviation of valid pressures > 1.5
* Not more than 85% of pressures cluster around 14.0–14.5 cmH2O (indicates constant value)

---

## Output

* **Interactive Charts** powered by Plotly.js
* Charts for Pressure, Leak, and Events per Hour
* Sessions merged by logical night (based on timestamp)

---

## Limitations / Notes

* Requires a modern browser
* Assumes all files are valid and from the same device
* `.STS` interpretation is approximate due to lack of official documentation
* Plotly.js v1.58.5 is bundled for compatibility; consider updating to a newer version manually

---

## Roadmap Ideas

* Dashboard summary (highest pressure, average leak over week/month)
* Export to CSV or PDF
* Event categorisation breakdown
* Mobile UI improvements

---

## Credits

Created as a proof-of-concept for offline CPAP session visualisation using browser-native technologies.

---

## Disclaimer

This viewer is not a medical tool. For clinical insights, consult your healthcare provider or official software from Sefam.  The results may not be right.  Use at your own risk.

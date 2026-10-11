
# IoT Temperature Sensor Analysis

Exploratory time series analysis of temperature readings from indoor and outdoor IoT sensors, done in Python (pandas) on Google Colab. This is my first dataset taken from raw file to findings.

(daily_mean_in_vs_out.png)
<img width="865" height="528" alt="Screenshot 2026-10-08 144113" src="https://github.com/user-attachments/assets/b63ab97b-665e-4f43-a459-8a0ad9285546" />
<img width="1372" height="548" alt="Screenshot 2026-10-08 132524" src="https://github.com/user-attachments/assets/16d318e2-347b-4957-bced-aa2844e957fe" />

## Dataset

- **Source:** [Temperature Readings: IOT Devices](https://www.kaggle.com/datasets/atulanandjha/temperature-readings-iot-devices) on Kaggle (CSV).
- **Content:** temperature readings from devices placed inside and outside one room.
- **Columns:** `id`, `room_id/id`, `noted_date`, `temp`, `out/in`.
- **Size:** 97,606 rows in the downloaded file, 97,605 after removing one duplicated reading.
- **Period:** 28 Jul 2018 to 8 Dec 2018 (133 days), but readings exist on only 86 of those days.
- **Notes from the dataset description:** it is a sample of a larger dataset, recorded at random intervals, and contains some mis-readings. The unit of `temp` is not stated.

The CSV is not included in this repository. Download it from the Kaggle link above.

## Questions

1. How do outdoor readings relate to indoor readings?
2. How does temperature vary across the day?
3. Is the relationship stable over the whole period?

## Method

1. **Load** the CSV and check shape, columns and types (`info()`, `describe()`).
2. **Clean:**
   - convert `noted_date` from text to datetime with an explicit day-month-year format;
   - inspect and drop the one duplicated reading;
   - drop `room_id/id` (a single value, `Room Admin`, in every row);
   - rename `out/in` to `location`.
3. **Aggregate** with `groupby` per day, per hour and per sensor location.
4. **Visualize** daily means as dots (not lines), so missing days are not drawn as data.
5. **Question the result:** check how many readings each day contributes, compare averaging methods, and split the data before and after a sudden shift.

## Key findings

- **Readings are uneven.** 17 Oct 2018 alone holds 20,564 readings (about 21% of the file). The 10 busiest days hold about 68%. A plain average over all readings is therefore dominated by a few days.
- **The averaging method changes the answer.** The daily peak of outdoor temperature falls at about 06:00 when averaging per reading, and at about 08:00 to 11:00 when averaging per day.
- **A sudden shift in the outdoor sensor.** The outdoor daily mean rose from about 30 to about 41.6 between 15 and 16 Sep 2018, a change of about 11.3. The next largest day-to-day change was about 4.4.
- **A data gap surrounds the shift.** The last outdoor reading on 15 Sep is at hour 20 and the first on 16 Sep at hour 14, so about 18 hours have no data. I cannot tell whether the change was abrupt or gradual.
- **Before and after.** Before 16 Sep, indoor and outdoor means were close. After it, outdoor readings were higher at every hour (roughly 11 to 13 degrees, read from the plot), with a wider daily cycle. Indoor readings changed less.
- **Cause: unknown.** The dataset description does not mention any change to the sensors.

## Limitations

- The cause of the shift is not explained by the data or by the description.
- Readings are irregular and many days are missing, so averages rest on uneven data.
- The temperature unit is not stated, so no unit is reported.
- Sample counts before the shift are smaller (23 days per sensor) than after it (51 to 61 days), so the "before" curves are less stable.

## How to run

1. Download the CSV from the Kaggle link and name it `iot_telemetry_data.csv`.
2. Open `iot_temperature_analysis.ipynb` in Google Colab.
3. Upload the CSV through the Files panel (uploaded files are deleted when the session ends).
4. Run all cells (Runtime, then Run all).

Setup cell used at the top of the notebook:

```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv("iot_telemetry_data.csv")
df["noted_date"] = pd.to_datetime(df["noted_date"], format="%d-%m-%Y %H:%M")
df = df.drop_duplicates()
df = df.drop(columns=["room_id/id"])
df = df.rename(columns={"out/in": "location"})
df["hour"] = df["noted_date"].dt.hour
df["date"] = df["noted_date"].dt.date
```

## Tools

Python, pandas, matplotlib, Google Colab.

## What I practiced

`read_csv`, `info`, `describe`, `to_datetime`, `duplicated` / `drop_duplicates`, `.dt.date` / `.dt.hour`, `groupby` with `mean` and `unstack`, `diff`, and plotting with `.plot(style=".")`.

## Author

muzan hassan mahmoud · www.linkedin.com/in/muzan-eltinay


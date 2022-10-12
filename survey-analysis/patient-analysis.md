# Patient Analysis

This notebook is responsible for analyzing the "patient" survey responses that have been collected from this [survey](https://forms.gle/axv3s55uio7RmGZT6)

## Questions that we want to answer

- We want to know how likely it is that users will use a pickup box for prescriptions based on their age range
  - We want to know if our proposed solution will have a high customer acceptance rate
- We want to know what patients believe is the mean acceptable amount of time to wait to pick up prescriptions
  - We want to have a benchmark by which we can determine if our solution meets the needs of customers.

Import dependencies

```python
# import dependencies
import numpy as np
import matplotlib.pyplot as plt
import scipy.optimize as sci
import pandas as pd
from pathlib import Path
import uuid
from IPython.display import display, HTML

# Some formating options
%config InlineBackend.figure_formats = ['svg']

def disp(df):
    """
    Displays a dataframe as HTML

    Args:
        df (dataframe): Dataframe
    """
    display(HTML(df.to_html()))



```

Load patient survery data into dataframe

```python
file_path = "./output/patient-survey-data.csv"
df = pd.read_csv(file_path)

display_all_data = False

if display_all_data:
    disp(df)
```

## We want to know how likely it is that users will use a pickup box for prescriptions based on their age range

```python
ages = ["Under 21", "21 - 35", "35 - 40", "40 - 64", "65 +"]
col_name = "How likely are you to use an automated pick-up locker to pick up your prescriptions?"

df_counts = pd.DataFrame()

for idx, age in enumerate(ages):
    frame = df.loc[df["What is your age range?"] == age]
    fig = plt.figure()
    counts = frame[col_name].value_counts()
    counts = counts.to_frame()
    counts.plot.barh()
    plt.suptitle(str("Count of Respondants " + age))
    plt.xlabel("Count of Respondants")
    plt.ylabel("liklihood")
    counts.rename(
        columns={col_name: age},
        inplace=True,
    )
    if idx == 0:
        df_counts = counts

    else:
        df_counts[age] = counts[age]
        df_counts.fillna(0)


df_counts = df_counts.fillna(0)
disp(df_counts)
plt.figure()
df_counts.plot.barh(figsize=(10, 6))
plt.suptitle("Total Count of Liklihood Responses by Age")
plt.xlabel("Count of Respondants")
plt.ylabel("liklihood")

```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Under 21</th>
      <th>21 - 35</th>
      <th>35 - 40</th>
      <th>40 - 64</th>
      <th>65 +</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Very likely</th>
      <td>60</td>
      <td>425</td>
      <td>162</td>
      <td>57</td>
      <td>3</td>
    </tr>
    <tr>
      <th>Somewhat likely</th>
      <td>48</td>
      <td>430</td>
      <td>226</td>
      <td>66</td>
      <td>7</td>
    </tr>
    <tr>
      <th>Likely</th>
      <td>44</td>
      <td>446</td>
      <td>261</td>
      <td>84</td>
      <td>6</td>
    </tr>
    <tr>
      <th>Not very likely</th>
      <td>35</td>
      <td>198</td>
      <td>58</td>
      <td>41</td>
      <td>2</td>
    </tr>
    <tr>
      <th>Never</th>
      <td>10</td>
      <td>33</td>
      <td>10</td>
      <td>13</td>
      <td>5</td>
    </tr>
  </tbody>
</table>

    Text(0, 0.5, 'liklihood')




    <Figure size 432x288 with 0 Axes>

![svg](./charts/output_7_3.svg)

    <Figure size 432x288 with 0 Axes>

![svg](./charts/output_7_5.svg)

    <Figure size 432x288 with 0 Axes>

![svg](./charts/output_7_7.svg)

    <Figure size 432x288 with 0 Axes>

![svg](./charts/output_7_9.svg)

    <Figure size 432x288 with 0 Axes>

![svg](./charts/output_7_11.svg)

    <Figure size 432x288 with 0 Axes>

![svg](./charts/output_7_13.svg)

```python
df_probs = df_counts
tot_counts = df[col_name].value_counts()
tot_counts = tot_counts.to_frame()

df_probs["All"] = tot_counts[col_name]

df_probs["All"] = df_probs["All"] / np.sum(df_probs["All"])

for age in ages:
    sum = np.sum(df_probs[age])
    df_probs[age] = df_probs[age] / sum

disp(df_probs)
plt.figure()
ax = df_probs.plot.bar(figsize=(10, 6))
plt.suptitle("Liklihood Responses by Age")
plt.xlabel("Probability")
plt.ylabel("liklihood")
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Under 21</th>
      <th>21 - 35</th>
      <th>35 - 40</th>
      <th>40 - 64</th>
      <th>65 +</th>
      <th>All</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Very likely</th>
      <td>0.304569</td>
      <td>0.277415</td>
      <td>0.225941</td>
      <td>0.218391</td>
      <td>0.130435</td>
      <td>0.259151</td>
    </tr>
    <tr>
      <th>Somewhat likely</th>
      <td>0.243655</td>
      <td>0.280679</td>
      <td>0.315202</td>
      <td>0.252874</td>
      <td>0.304348</td>
      <td>0.284407</td>
    </tr>
    <tr>
      <th>Likely</th>
      <td>0.223350</td>
      <td>0.291123</td>
      <td>0.364017</td>
      <td>0.321839</td>
      <td>0.260870</td>
      <td>0.308199</td>
    </tr>
    <tr>
      <th>Not very likely</th>
      <td>0.177665</td>
      <td>0.129243</td>
      <td>0.080893</td>
      <td>0.157088</td>
      <td>0.086957</td>
      <td>0.122255</td>
    </tr>
    <tr>
      <th>Never</th>
      <td>0.050761</td>
      <td>0.021540</td>
      <td>0.013947</td>
      <td>0.049808</td>
      <td>0.217391</td>
      <td>0.025988</td>
    </tr>
  </tbody>
</table>

    Text(0, 0.5, 'liklihood')




    <Figure size 432x288 with 0 Axes>

![svg](./charts/output_8_3.svg)

```python
plt.figure()
ax = df_probs.plot.density(figsize=(10,6))
plt.suptitle("Density of Response Probability by Age")
plt.xlabel("Probability")
plt.ylabel("Density")
```

    Text(0, 0.5, 'Density')




    <Figure size 432x288 with 0 Axes>

![svg](./charts/output_9_2.svg)

## We want to know what patients believe is the mean acceptable amount of time to wait to pick up prescriptions

So we can have a benchmark by which we can determine if our solution meets the needs of customers.

Correlate time ranges to numerical values

```python
df_times = pd.DataFrame()
target_ideal = "What do you think is an acceptable amount of time to wait for services at your pharmacy?"
target_true = "How long do you usually wait at the pharmacy?"
min_ideal_wait_times_val = "Min ideal reported wait times"
min_actual_wait_times_val = "Min actual reported wait times"
max_ideal_wait_times_val = "Max ideal reported wait times"
max_actual_wait_times_val = "Max actual reported wait times"
df_times[min_ideal_wait_times_val] = df[target_ideal]
df_times[min_actual_wait_times_val] = df[target_true]
df_times[max_ideal_wait_times_val] = df[target_ideal]
df_times[max_actual_wait_times_val] = df[target_true]
time_ranges = [
    ["0-3 minutes", 0, 3],
    ["4-6 minutes", 4, 6],
    ["7-9 minutes", 7, 9],
    ["11-15 minutes", 11, 15],
    ["more than 15 minutes", 16, 20],
    ["More than 15 minutes", 16, 20],
    [">16 minutes", 16, 20],
]

for range in time_ranges:
    df_times.loc[
        df_times[min_ideal_wait_times_val] == range[0], min_ideal_wait_times_val
    ] = range[1]
    df_times.loc[
        df_times[min_actual_wait_times_val] == range[0], min_actual_wait_times_val
    ] = range[1]
    df_times.loc[
        df_times[max_ideal_wait_times_val] == range[0], max_ideal_wait_times_val
    ] = range[2]
    df_times.loc[
        df_times[max_actual_wait_times_val] == range[0], max_actual_wait_times_val
    ] = range[2]

mean_min_ideal = np.round(np.mean(df_times[min_ideal_wait_times_val]), 3)
mean_min_actual = np.round(np.mean(df_times[min_actual_wait_times_val]), 3)
mean_max_ideal = np.round(np.mean(df_times[max_ideal_wait_times_val]), 3)
mean_max_actual = np.round(np.mean(df_times[max_actual_wait_times_val]), 3)
stddev_mean_min_ideal = np.round(np.std(df_times[min_ideal_wait_times_val]), 3)
diff_min = abs(mean_min_ideal - mean_min_actual)
diff_max = abs(mean_max_ideal - mean_max_actual)


df_mean_times = pd.DataFrame(
    [
        [mean_min_ideal, mean_max_ideal],
        [mean_min_actual, mean_max_actual],
        [diff_min, diff_max],
    ],
    columns=["Mean Minimum (min)", "Mean Max (min)"],
    index=["Ideal Reported Wait Times", "Actual Reported Wait times", "Difference"],
)
```

Show Table with `min` and `max` desired times

```python
disp(df_mean_times)
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Mean Minimum (min)</th>
      <th>Mean Max (min)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Ideal Reported Wait Times</th>
      <td>6.598</td>
      <td>9.198</td>
    </tr>
    <tr>
      <th>Actual Reported Wait times</th>
      <td>7.767</td>
      <td>10.578</td>
    </tr>
    <tr>
      <th>Difference</th>
      <td>1.169</td>
      <td>1.380</td>
    </tr>
  </tbody>
</table>

Investigate the desity of responses

```python
df_times = df_times.astype(int)

df_times.plot.kde(figsize=(10, 6), subplots=True)


plt.figure()
df_times.plot.hist(alpha=0.5, figsize=(10, 6))

plt.figure()
df_times.plot.box(figsize=(12, 6))
```

    <AxesSubplot: >

![svg](./charts/output_16_1.svg)

    <Figure size 432x288 with 0 Axes>

![svg](./charts/output_16_3.svg)

    <Figure size 432x288 with 0 Axes>

![svg](./charts/output_16_5.svg)

```python

```

```python

```

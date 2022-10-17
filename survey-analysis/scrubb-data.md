## Scrubbing User Data

We want to ensure that the data that we analyze is scrubbed so that no trace-able information is related to individual data points.


Import Dependencies



```python
# import dependencies
import numpy as np
import matplotlib.pyplot as plt
import scipy.optimize as sci
import pandas as pd
from pathlib import Path
import uuid
from IPython.display import display, HTML


def disp(df):
    display(HTML(df.to_html()))
```

Load our raw data in the form of `CSV` into data frames. Raw data should be located in `./raw-data/`.



```python
file_name = input("What is the name of the csv file?")
file_path = "./raw-data/" + file_name
df = pd.read_csv(file_path)
```

Prune duplicate emails from the dataset. This is an easy way to know if someone has submitted multiple responses



```python
col_name = (
    "(Optional) Provide your email for a chance to win a $20.00 Tim Horton's gift card"
)

df.drop_duplicates(
    subset=[col_name],
    keep="last",
)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Timestamp</th>
      <th>What do you think is an acceptable amount of time to wait for services at your pharmacy?</th>
      <th>How long do you usually wait at the pharmacy?</th>
      <th>Have you used Amazon Lockers or a similar pick-up Lockers for before?</th>
      <th>How likely are you to use an automated pick-up locker to pick up your prescriptions?</th>
      <th>Email Address</th>
      <th>What is your age range?</th>
      <th>(Optional) Provide your email for a chance to win a $20.00 Tim Horton's gift card</th>
      <th>How often do you usually visit pharmacies in a given year</th>
      <th>Think about the last time you went to the pharmacy. How would you best describe your experience?</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2</th>
      <td>10/5/2022 18:48:45</td>
      <td>11-15 minutes</td>
      <td>0-3 minutes</td>
      <td>No</td>
      <td>Somewhat likely</td>
      <td>NaN</td>
      <td>Under 21</td>
      <td>zoe.cushman@protonmail.com</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>10/5/2022 19:10:21</td>
      <td>4-6 minutes</td>
      <td>more than 15 minutes</td>
      <td>No</td>
      <td>Very likely</td>
      <td>NaN</td>
      <td>21 - 35</td>
      <td>nbudatho@uwaterloo.ca</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>10/5/2022 19:20:25</td>
      <td>4-6 minutes</td>
      <td>7-9 minutes</td>
      <td>No</td>
      <td>Not very likely</td>
      <td>NaN</td>
      <td>21 - 35</td>
      <td>glmdenney17@gmail.com</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>5</th>
      <td>10/5/2022 19:25:40</td>
      <td>4-6 minutes</td>
      <td>0-3 minutes</td>
      <td>No</td>
      <td>Likely</td>
      <td>NaN</td>
      <td>21 - 35</td>
      <td>m.balghonaim@gmail.com</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>6</th>
      <td>10/5/2022 19:32:27</td>
      <td>4-6 minutes</td>
      <td>7-9 minutes</td>
      <td>No</td>
      <td>Very likely</td>
      <td>NaN</td>
      <td>21 - 35</td>
      <td>jjwilkin@uwaterloo.ca</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2831</th>
      <td>10/17/2022 1:07:53</td>
      <td>7-9 minutes</td>
      <td>11-15 minutes</td>
      <td>Yes</td>
      <td>Likely</td>
      <td>NaN</td>
      <td>40 - 64</td>
      <td>anunley661@gmail.com</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2832</th>
      <td>10/17/2022 1:08:06</td>
      <td>7-9 minutes</td>
      <td>11-15 minutes</td>
      <td>Yes</td>
      <td>Likely</td>
      <td>NaN</td>
      <td>40 - 64</td>
      <td>alantheisen106@gmail.com</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2833</th>
      <td>10/17/2022 1:09:47</td>
      <td>4-6 minutes</td>
      <td>7-9 minutes</td>
      <td>Yes</td>
      <td>Likely</td>
      <td>NaN</td>
      <td>35 - 40</td>
      <td>rjudson123@gmail.com</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2834</th>
      <td>10/17/2022 1:10:11</td>
      <td>4-6 minutes</td>
      <td>7-9 minutes</td>
      <td>Yes</td>
      <td>Likely</td>
      <td>NaN</td>
      <td>35 - 40</td>
      <td>hsylvester837@gmail.com</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2835</th>
      <td>10/17/2022 1:11:43</td>
      <td>4-6 minutes</td>
      <td>7-9 minutes</td>
      <td>Yes</td>
      <td>Likely</td>
      <td>NaN</td>
      <td>35 - 40</td>
      <td>ddominy87@gmail.com</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>2706 rows × 10 columns</p>
</div>



Collect the emails and store them separate from data



```python
emails = pd.DataFrame(df[col_name])

# rename columns for sanity
emails.rename(
    columns={col_name: "email"},
    inplace=True,
)
# drop duplicates
emails.drop_duplicates()

# drop blanks
emails = emails.dropna()

# scamble emails so that are randomly out of order
emails = emails.sample(frac=1).reset_index(drop=True)

# write file to .csv
email_output_file_name = "emails_for_reward.csv"
filepath = Path("./output/" + email_output_file_name)
filepath.parent.mkdir(parents=True, exist_ok=True)
emails.to_csv(filepath)
```

With our emails saved, we can now scrubb our data and clean it up so we can use it for analysis



```python
# shuffle the rows and re-index
df = df.sample(frac=1).reset_index(drop=True)

# drop columns containing tracable information
df = df.drop(
    columns=[
        col_name,
        "Email Address",
        "Timestamp",
        "How often do you usually visit pharmacies in a given year",
        "Think about the last time you went to the pharmacy. How would you best describe your experience?",
    ]
)


# add a UUID identifier to each row
df.insert(0, "id", "")
df["id"] = [uuid.uuid4() for _ in range(len(df.index))]
```

Save the data to `/output` as `patient-survey-data.csv`


```python
# write file to .csv
data_file_name = "patient-survey-data.csv"
filepath = Path("./output/" + data_file_name)
filepath.parent.mkdir(parents=True, exist_ok=True)
df.to_csv(filepath)
```

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
col_name = "(Optional) Provide your email to be contacted to further help us understand pharmacists' pain points and needs."

df.drop_duplicates(
    subset=[col_name],
    keep="last",
)
```

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
email_output_file_name = "emails_available_for_contact.csv"
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
    ]
)


# add a UUID identifier to each row
df.insert(0, "id", "")
df["id"] = [uuid.uuid4() for _ in range(len(df.index))]
```

Save the data to `/output` as `patient-survey-data.csv`


```python
# write file to .csv
data_file_name = "pharmacy_survey_data.csv"
filepath = Path("./output/" + data_file_name)
filepath.parent.mkdir(parents=True, exist_ok=True)
df.to_csv(filepath)
```

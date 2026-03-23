# UC-Berkeley-AI-course-Amazon-coupons
This project is related to UC-Berkeley-AI-course-Amazon-coupons
Under Module5 of this course UC-Berkeley AI course, we need to create Amazon coupon project
# Dataset quality assessment


Investigate the dataset for missing or problematic data

Exploring the notebook to identify the dataset and how it's loaded, then analyzing missing and problematic values.




Running analysis on `coupons.csv` and adding investigation code to the notebook's empty cell.




Adding investigation code to the notebook's empty cell for problem 2.




Replacing the numeric loop with `describe()` for clearer output.




Here’s what the **coupons** dataset (`data/coupons.csv`) shows after loading it with your notebook.

### Missing data
| Column | Missing | % of rows |
|--------|---------|-----------|
| **car** | 12,576 | **99.15%** |
| CoffeeHouse | 217 | 1.71% |
| Restaurant20To50 | 189 | 1.49% |
| CarryAway | 151 | 1.19% |
| RestaurantLessThan20 | 130 | 1.02% |
| Bar | 107 | 0.84% |

Only **8 rows** have a non-null `car` value (e.g. “Scooter and motorcycle”, “do not drive”, etc.). The behavioral frequency columns (`Bar`, `CoffeeHouse`, …) use NaN where that habit wasn’t recorded—much smaller gaps than `car`.

Core fields used for the scenario (destination, weather, coupon type, `Y`, etc.) have **no** missing values. **`Y`** is clean: only 0 and 1 (7,210 accepted, 5,474 not).

### Other issues
- **74 fully duplicate rows** (~0.58%); 148 rows are part of duplicate groups.
- **Schema / docs:** the column is spelled **`passanger`** (typo). **`time`** has five levels (7AM, 10AM, 2PM, 6PM, 10PM), while the written description only mentions three times—data is consistent internally, not an error.
- **Numeric fields:** `temperature` is only 30, 55, 80; binary columns are 0/1 as expected.

Investigation code for prompt **2** is in the notebook cell under *“Investigate the dataset for missing or problematic data”* so you can re-run it and paste outputs into your report. For **3** (what to do), typical choices are: **drop or ignore `car`** for most analyses, **impute or drop rows** for the frequency columns depending on whether you need those features, and optionally **drop duplicate rows** if you want one observation per scenario.

3. Decide what to do about your missing data -- drop, replace, other...

Checking the notebook structure for problem 3 and how `data` is used in later prompts.




Having reviewed the notebook: Problem 3’s code cell is empty; later steps use `data` for proportions and plots, and the Bar-coupon section requires the `Bar` column for comparisons. Creating a plan that ties decisions to those uses.


4. What proportion of the total observations chose to accept the coupon?

Here’s the answer (12,684 rows, before any cleaning in step 3).

### Proportion who accepted the coupon (`Y == 1`)

- **7210** observations accepted → **7210 ÷ 12684 ≈ 0.5684**, i.e. **about 56.8%** (or **56.84%** if you round to two decimals).

Equivalently, **5474** did not accept (**≈ 43.2%**).

### Code for the notebook (problem 4 cell)

Use whatever dataframe you analyze after step 3 (often still called `data`):

```python
data["Y"].mean()  # proportion of 1s
# or explicitly:
(data["Y"] == 1).mean()
```

If you **dropped duplicate rows** or changed rows in step 3, run this on the **cleaned** `data` so the proportion matches your reported sample size.

---

**Plan mode note:** I didn’t change your notebook. If you want this pasted into the problem 4 code cell for you, say so and we can apply it after plan mode is off (or you can paste it yourself).

5. Use a bar plot to visualize the `coupon` column.

For **problem 5**, you want **counts of each category** in `coupon` shown as bars.

### Approach

Use **`value_counts()`** then a bar plot, or **`seaborn.countplot`**. Your notebook already imports `matplotlib`, `seaborn`, and `pandas`.

### Option A — pandas

```python
order = data["coupon"].value_counts().index
data["coupon"].value_counts().reindex(order).plot(kind="bar")
plt.xlabel("coupon")
plt.ylabel("count")
plt.title("Coupon type frequency")
plt.xticks(rotation=45, ha="right")
plt.tight_layout()
plt.show()
```

### Option B — seaborn

```python
plt.figure(figsize=(8, 4))
sns.countplot(data=data, x="coupon", order=data["coupon"].value_counts().index)
plt.xlabel("coupon")
plt.ylabel("count")
plt.title("Coupon type frequency")
plt.xticks(rotation=45, ha="right")
plt.tight_layout()
plt.show()
```

There are **five** coupon types (e.g. Coffee House, Bar, …); the bars show how often each appears in your (possibly cleaned) `data`.

---

**Plan mode:** I’m not editing your notebook here. If you want this inserted into the problem 5 cell automatically, say **execute the plan** or ask to **fill in the notebook** after plan mode is cleared.

6. Use a histogram to visualize the temperature column.


For **problem 6**, plot the **distribution of `temperature`**. In this dataset it only takes **30, 55, and 80**, so the plot is really three peaks (or three bars if you use narrow bins).

### Histogram (matplotlib)

```python
plt.figure(figsize=(7, 4))
plt.hist(data["temperature"], bins=[30, 42.5, 67.5, 80], edgecolor="black")
plt.xlabel("temperature (°F)")
plt.ylabel("count")
plt.title("Distribution of temperature")
plt.show()
```

Using **`sns.histplot`** with explicit bins:

```python
plt.figure(figsize=(7, 4))
sns.histplot(data=data, x="temperature", bins=[30, 42.5, 67.5, 80], edgecolor="black")
plt.xlabel("temperature (°F)")
plt.title("Distribution of temperature")
plt.show()
```

The bin edges split the three values so each falls in its own bin. Alternatively, **`bins=3`** and **`binrange=(30, 80)`** can work; you can tune if bars look uneven.

### If the assignment expects a “classic” histogram

```python
data["temperature"].hist(bins=20, edgecolor="black")
plt.xlabel("temperature (°F)")
plt.ylabel("count")
plt.show()
```

With only three distinct values, you’ll still see just three spikes, which is correct.

---

**Plan mode:** I’m not editing your notebook. To have this dropped into the problem 6 cell for you, ask to **execute the plan** or to **update the notebook** once plan mode is off.


**Investigating the Bar Coupons**

Now, we will lead you through an exploration of just the bar related coupons.  

1. Create a new `DataFrame` that contains just the bar coupons.


Checking the exact label for bar coupons in the dataset.




Bar scenarios are rows where the **`coupon`** column equals **`Bar`** (exact string, as in the CSV).

### Create the subset

```python
bar_coupons = data[data["coupon"] == "Bar"].copy()
```

Using `.copy()` avoids **SettingWithCopy** warnings if you add columns or change values later.

### Quick checks

```python
bar_coupons.shape
bar_coupons["coupon"].value_counts()
```

You should see only **`Bar`** in `coupon` for this frame.

---

**Note:** This is separate from the **`Bar`** column (how often the respondent goes to a bar). The filter for “bar coupons” uses **`coupon == "Bar"`**.

---

**Plan mode:** I didn’t change your notebook. Say if you want this inserted into the bar-coupon section cell after plan mode is off.


2. What proportion of bar coupons were accepted?


Computing the acceptance rate for bar coupons from the dataset.




Using **`data/coupons.csv`** as loaded (no extra cleaning):

- **2,017** rows with `coupon == "Bar"`
- **827** with `Y == 1` (accepted)

**Proportion accepted ≈ 0.410** → **about 41.0%** (827 / 2017).

### In your notebook (after `bar_coupons` is defined)

```python
bar_coupons["Y"].mean()
```

or, equivalently:

```python
(bar_coupons["Y"] == 1).mean()
```

If you **dropped duplicates** or changed rows in step 3, recompute on your cleaned `data` so the proportion matches the sample you analyze.


Compare the acceptance rate between those who went to a bar 3 or fewer times a month to those who went more.



Checking exact values in the `Bar` column so we can split "3 or fewer" vs "more".




Here’s a clean way to do it and what the **raw** `coupons.csv` shows.

### How to split `Bar`

For **bar-coupon** rows, `Bar` is how often they go to a bar. Treat **“3 or fewer”** as:

- `never`, `less1`, `1~3`

Treat **“more than 3”** as:

- `4~8`, `gt8`

(21 rows have **missing** `Bar`; exclude them from this comparison or report them separately.)

### Code (`bar_coupons` = `data[data["coupon"] == "Bar"]`)

```python
fewer = ["never", "less1", "1~3"]
more = ["4~8", "gt8"]

bar_fewer = bar_coupons[bar_coupons["Bar"].isin(fewer)]
bar_more = bar_coupons[bar_coupons["Bar"].isin(more)]

print("3 or fewer / month — n:", len(bar_fewer), "acceptance:", bar_fewer["Y"].mean())
print("More than 3 / month — n:", len(bar_more), "acceptance:", bar_more["Y"].mean())
```

You can also use `groupby` with a small helper column.

### Results on the full file (before your cleaning)

| Group | *n* | Acceptance rate (`Y` mean) |
|--------|-----|----------------------------|
| 3 or fewer (`never`, `less1`, `1~3`) | 1,797 | **≈ 0.371** (~37.1%) |
| More (`4~8`, `gt8`) | 199 | **≈ 0.769** (~76.9%) |

So among these bar-coupon scenarios, **people who go to bars more often accept the coupon much more often**; the gap is on the order of **40 percentage points** on this split.

**Write-up tip:** State the exact bins you used (`never` / `less1` / `1~3` vs `4~8` / `gt8`) and that rows with missing `Bar` were dropped (or filled) for this comparison.

Compare the acceptance rate between drivers who go to a bar more than once a month and are over the age of 25 to the all others.  Is there a difference?


Checking `age` column values and computing both groups for bar-coupon rows.




Here’s a concrete way to define the groups, what the **raw** bar-coupon rows show, and how to code it.

### Definitions

**1. Bar coupon rows**  
`bar_coupons = data[data["coupon"] == "Bar"]`

**2. “Over the age of 25”**  
`age` is categorical. Treat **over 25** as anything **not** `below21` or `21` (those brackets are under/at 25):

```python
over_25 = ["26", "31", "36", "41", "46", "50plus"]
```

**3. “Go to a bar more than once a month”**  
The `Bar` field is binned: `never`, `less1`, `1~3`, `4~8`, `gt8`.

- **Usual assignment reading:** “more than once a month” = **not** the lowest-frequency bins. So **exclude** `never` and `less1`, and **include** `1~3`, `4~8`, `gt8`.  
  (If you want to be stricter and only count **clearly** high frequency, you can use only `4~8` and `gt8`; numbers below include both so you can choose.)

### Code

```python
bar_more_than_once = ["1~3", "4~8", "gt8"]  # exclude never, less1
over_25 = ["26", "31", "36", "41", "46", "50plus"]

mask_target = bar_coupons["Bar"].isin(bar_more_than_once) & bar_coupons["age"].isin(over_25)
target = bar_coupons[mask_target]
others = bar_coupons[~mask_target]

print("Target — n:", len(target), "acceptance:", target["Y"].mean())
print("All others — n:", len(others), "acceptance:", others["Y"].mean())
```

Drop or impute rows with missing `Bar` first if you use `fillna("Unknown")` in cleaning:

```python
bc = bar_coupons.dropna(subset=["Bar"])
mask_target = bc["Bar"].isin(bar_more_than_once) & bc["age"].isin(over_25)
```

### Results on full `coupons.csv` (bar coupons only)

Using **`1~3` / `4~8` / `gt8`** and **`over_25`** as above:

| Group | *n* | Acceptance rate |
|--------|-----|-------------------|
| **Bar > once/mo (loose) & over 25** | 420 | **≈ 0.695** (~69.5%) |
| **All others** | 1,597 | **≈ 0.335** (~33.5%) |

**Yes, there is a large difference:** acceptance is **about 36 percentage points** higher in the target group than in everyone else (~0.36 on the probability scale).

If you use only **`4~8` and `gt8`** for “more than once,” the pattern is similar: target **≈ 77%** (*n* = 136) vs others **≈ 38%** (*n* = 1,881), still a **large** gap.

State your rule for “more than once” (exclude `never`/`less1` vs only `4~8`/`gt8`) and that “over 25” uses the `age` bins above. Mention any rows dropped for missing `Bar`.


Use the same process to compare the acceptance rate between drivers who go to bars more than once a month and had passengers that were not a kid and had occupations other than farming, fishing, or forestry.


Checking exact `passanger` and `occupation` values in the data for bar-coupon rows.




Here is the **same logic** as before, extended with **passenger** and **occupation**, using the exact strings in `coupons.csv`.

### Definitions

- **Bar coupon rows:** `coupon == "Bar"`.
- **More than once a month (same as before):** `Bar` in `["1~3", "4~8", "gt8"]` (exclude `never` and `less1`).
- **Passenger was not a kid:** `passanger != "Kid(s)"` (column name is spelled `passanger` in the file). Valid values: `Alone`, `Partner`, `Friend(s)`, `Kid(s)`.
- **Occupation not farming/fishing/forestry:** `occupation != "Farming Fishing & Forestry"` 

```python
bar_more_than_once = ["1~3", "4~8", "gt8"]

mask = (
    bar_coupons["Bar"].isin(bar_more_than_once)
    & (bar_coupons["passanger"] != "Kid(s)")
    & (bar_coupons["occupation"] != "Farming Fishing & Forestry")
)

target = bar_coupons[mask]
others = bar_coupons[~mask]

print("Target — n:", len(target), "acceptance:", target["Y"].mean())
print("All others — n:", len(others), "acceptance:", others["Y"].mean())
```

If you want to exclude missing `Bar` before comparing:

```python
bc = bar_coupons.dropna(subset=["Bar"])
# same mask on bc
```

### Results on full `coupons.csv` (bar coupons only)

Using the definitions above:

| Group | *n* | Acceptance rate |
|--------|-----|-------------------|
| **Target** (bar > once/mo, not kid passenger, not farming/fishing/forestry) | 551 | **≈ 0.713** (~71.3%) |
| **All others** | 1,466 | **≈ 0.296** (~29.6%) |

**Difference:** about **0.417** (~**42** percentage points) higher acceptance in the target group.

So yes: **there is a strong difference** in the same direction as the age/over-25 comparison—this combined slice is more likely to accept the bar coupon than everyone else in the bar-coupon sample.

**Report note:** Spell out the three filters (`Bar` bins, `passanger != Kid(s)`, `occupation != Farming Fishing & Forestry`) and mention any rows dropped for missing `Bar`.

Compare the acceptance rates between those drivers who:

- go to bars more than once a month, had passengers that were not a kid, and were not widowed *OR*
- go to bars more than once a month and are under the age of 30 *OR*
- go to cheap restaurants more than 4 times a month and income is less than 50K.




Checking exact values for `maritalStatus`, `RestaurantLessThan20`, `age` (under 30), and `income` in the dataset.




Fixing PowerShell `$` expansion in our test and recomputing.




Here is a clear definition of the three arms, the **exact** field values in `coupons.csv`, and the acceptance rates on the **full** dataset (all coupon types). The analysis is on **all rows** because condition 3 uses cheap-restaurant behavior and income, not only bar coupons.

### How each part is coded

**Bar “more than once a month”** (same as before):  
`Bar` in `["1~3", "4~8", "gt8"]` (exclude `never`, `less1`).

**Passenger not a kid:**  
`passanger != "Kid(s)"`.

**Not widowed:**  
`maritalStatus != "Widowed"` (exact value in the file).

**Under 30:**  
`age` in `["below21", "21", "26"]` (survey bins through 26–30; `31` and above are 30+).

**Cheap restaurants “more than 4 times a month”:**  
`RestaurantLessThan20` in `["4~8", "gt8"]` (bins 4–8 and greater than 8 per month; `1~3` is not “more than 4”).

**Income less than \$50K:**  
`income` in:

- `"Less than $12500"`
- `"$12500 - $24999"`
- `"$25000 - $37499"`
- `"$37500 - $49999"`

**Three groups (OR):**

1. `cond1` = bar often ∧ not kid ∧ not widowed  
2. `cond2` = bar often ∧ under 30  
3. `cond3` = cheap restaurant often ∧ income &lt; 50K  

**Target** = `cond1 | cond2 | cond3`  
**Others** = everyone else.

### Code (e.g. Jupyter; normal `$` in strings is fine)

```python
bar_often = ["1~3", "4~8", "gt8"]
under_30 = ["below21", "21", "26"]
cheap_gt4 = ["4~8", "gt8"]
income_lt_50k = [
    "Less than $12500",
    "$12500 - $24999",
    "$25000 - $37499",
    "$37500 - $49999",
]

cond1 = (
    data["Bar"].isin(bar_often)
    & (data["passanger"] != "Kid(s)")
    & (data["maritalStatus"] != "Widowed")
)
cond2 = data["Bar"].isin(bar_often) & data["age"].isin(under_30)
cond3 = data["RestaurantLessThan20"].isin(cheap_gt4) & data["income"].isin(income_lt_50k)

mask = cond1 | cond2 | cond3
target = data[mask]
others = data[~mask]

print("Target — n:", len(target), "acceptance:", target["Y"].mean())
print("Others — n:", len(others), "acceptance:", others["Y"].mean())
```

### Results on full `coupons.csv` (12,684 rows)

| Group | *n* | Acceptance rate (`Y` mean) |
|--------|-----|----------------------------|
| **Target** (any of the three OR conditions) | 5,152 | **≈ 0.605** (~60.5%) |
| **All others** | 7,532 | **≈ 0.543** (~54.3%) |

**Difference:** about **0.062** (~**6.2** percentage points) higher acceptance in the target group.

Rough counts for each condition (before OR; people can appear in more than one):  
`cond1` ≈ 3,696, `cond2` ≈ 2,272, `cond3` ≈ 2,279; **union** is 5,152 (not the sum of the three).

### Interpretation

**Yes, there is a difference:** the combined “OR” group accepts coupons at a **higher** rate than the rest, but the gap is **smaller** than the bar-only slices you used earlier (e.g. bar + over 25), because this target includes many rows from **cond3** (cheap restaurants + lower income) and overlaps across conditions.

### Optional notes for your write-up

- If you need **strictly more than four** visits (not “4–8”), the data only has bins; using only `gt8` would change cond3 and shrink that group.  
- On **Windows PowerShell**, `python -c "..."` can break strings containing `$`; use `python` in Jupyter or build `chr(36)` dollar signs if you test from the command line.  
- Drop or handle `NaN` in `Bar` / `RestaurantLessThan20` if you imputed missing values in step 3.

Based on these observations, what do you hypothesize about drivers who accepted the bar coupons?

Here is a concise hypothesis you can adapt for your report.

### Hypothesis

**Drivers who accept bar coupons look more like people who already go to bars regularly, are in situations where a bar stop is socially “allowed,” and are not in the lowest-engagement groups.** Acceptance is less about random willingness and more about **fit between the offer and existing behavior and context**.

### What the numbers suggest (association, not proof)

1. **Habit / relevance**  
   Acceptance is much higher among people who report going to bars **more often** (e.g. `4~8`, `gt8`) than among **rare** visitors (`never`, `less1`, `1~3`). That fits the idea that the coupon matches **existing habits** and feels usable, not odd or out of character.

2. **Social context**  
   When you add **no kid in the car** (and similar filters), acceptance stays high relative to “everyone else.” That supports a hypothesis that **who is in the vehicle** shapes whether a bar detour is acceptable—not just personal taste.

3. **Demographics and life situation**  
   Splits by **age** (e.g. over 25) and **marital** status (e.g. not widowed in one of your rules) point to **life stage / social situation** mattering, though you should describe them carefully as **patterns in the survey**, not as universal rules about all drivers.

4. **Broader eating-out / income picture**  
   The combined **OR** rule (bar behavior, age, cheap restaurants + income) still shows a **modest** lift vs everyone else, which is consistent with **several paths** to acceptance (bar-centric vs budget / frequent cheap dining), not a single “type.”

### How to phrase it academically

- Say you **hypothesize** that **acceptance is driven by alignment of the coupon with usual bar-going, acceptable social context, and possibly age/lifestyle**, as **observed in this sample**.  
- Add that **causation cannot be claimed**—these are **associations** from a scenario survey (Mechanical Turk), not a randomized experiment.

That gives you a defensible story: **bar coupon acceptance clusters with frequent bar use and contexts where a bar stop is plausible**, more than with “everyone equally likely to say yes.”


## Independent Investigation

Using the bar coupon example as motivation, you are to explore one of the other coupon groups and try to determine the characteristics of passengers who accept the coupons.  


Exploring other coupon types and how acceptance relates to `passanger` to provide concrete guidance.




Here is a practical way to do the **Independent Investigation**, using your dataset and the same spirit as the bar-coupon work.

### 1. Pick one other coupon type

From `coupons.csv`, the non-bar types are:

| Coupon | Approx. *n* | Overall acceptance (`Y` mean) |
|--------|-------------|--------------------------------|
| Coffee House | 3,996 | ~0.50 |
| Restaurant(&lt;20) | 2,787 | ~0.71 |
| Carry out & Take away | 2,393 | ~0.74 |
| Restaurant(20–50) | 1,492 | ~0.44 |

**Good choices for a short write-up**

- **Coffee House** — Overall acceptance is near 50/50 and **acceptance varies a lot by passenger** (see below), so you can tell a clear story.
- **Restaurant(20–50)** — Uptake is lower overall, and **Partner** stands out vs other passengers (interesting contrast to bar/cheap patterns).

### 2. Define your subset

```python
focus = data[data["coupon"] == "Coffee House"].copy()  # or another label, exact string from data
```

Use the **exact** `coupon` string (e.g. `"Coffee House"`, `"Restaurant(<20)"`).

### 3. Passenger and acceptance (core ask)

Group by `passanger` (spelling in the file) and compare `Y`:

```python
focus.groupby("passanger")["Y"].agg(["count", "mean"])
```

**Illustrative pattern (Coffee House, full file):** acceptance is roughly **highest with `Friend(s)`**, mid with **`Partner`**, lower with **`Kid(s)`** and **`Alone`** — so you can hypothesize that **social context** matters for this coupon, similar in spirit to bar but with different magnitudes.

**Illustrative pattern (Restaurant(20–50)):** **`Partner`** has much higher acceptance than **`Alone` / `Friend(s)` / `Kid(s)`** — worth discussing **price / occasion** (expensive meal as a couple activity).

### 4. Go beyond passengers (like the bar section)

To “determine characteristics,” add a few **context** variables on the same subset:

- `weather`, `time`, `temperature`, `destination`
- `expiration` (2h vs 1d)
- Optional: `age`, `income`, `gender` — compare groups with `groupby` or side-by-side acceptance rates.

Example:

```python
focus.groupby("expiration")["Y"].mean()
focus.groupby("weather")["Y"].mean()
```

### 5. What to write

1. **Why this coupon** (e.g. most common, or strongest passenger contrast).  
2. **Table or plot**: acceptance by `passanger` (bar chart of rates or counts).  
3. **1–2 sentences**: who accepts more vs less (e.g. friends vs alone).  
4. **Hypothesis**: tie behavior to **social setting**, **time/money**, or **scenario** — and state that this is **association in survey data**, not proof of causation.

### 6. Hypothesis examples (tailor to the coupon you choose)

- **Coffee House:** Acceptance is higher when the scenario involves **friends or a partner** than when **alone**, consistent with coffee as a **social** stop.  
- **Expensive restaurant:** Acceptance may cluster with **`Partner`** and with **longer expiration** or **specific times**, if your tables support it.

You do **not** need every coupon type—one deep dive on **Coffee House** or **Restaurant(20–50)** is enough for “independent investigation” if you justify it with numbers and a clear passenger-focused comparison.


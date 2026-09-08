# 🚗 Used Cars Data Analysis

A data analysis project focused on exploring used car prices, vehicle characteristics, and usage patterns through data cleaning, feature engineering, exploratory data analysis (EDA), and visualization.

---

## 📌 Project Overview

This project analyzes a dataset of used cars to understand the main factors associated with vehicle selling prices and usage patterns.

The analysis covers the complete data analysis workflow, starting from data inspection and cleaning, followed by feature engineering, exploratory data analysis, visualization, and extracting meaningful insights from the dataset.

---

## 🎯 Project Objectives

* Inspect and understand the structure and quality of the dataset.
* Clean missing, inconsistent, and invalid data.
* Standardize categorical values such as fuel type and seller type.
* Create new features to improve the analysis.
* Explore the relationship between car characteristics and selling price.
* Identify patterns in car age, mileage, brands, fuel types, and ownership.
* Extract useful insights from the analysis.

---

## 📊 Dataset Summary

| Metric            |         Value |
| ----------------- | ------------: |
| Number of Rows    |         4,345 |
| Number of Columns |             8 |
| Original Features |             8 |
| Main Target       | Selling Price |
| Dataset Type      |     Used Cars |

### Original Features

| Feature         | Description              |
| --------------- | ------------------------ |
| `name`          | Car name/model           |
| `year`          | Manufacturing year       |
| `selling_price` | Selling price of the car |
| `km_driven`     | Kilometers driven        |
| `fuel`          | Fuel type                |
| `seller_type`   | Type of seller           |
| `transmission`  | Transmission type        |
| `owner`         | Ownership history        |

---

## 🔄 Project Pipeline

```text
Dataset
   ↓
Data Inspection
   ↓
Data Cleaning
   ↓
Data Preprocessing
   ↓
Feature Engineering
   ↓
Exploratory Data Analysis
   ↓
Data Visualization
   ↓
Insights & Findings
```

---

## 🛠️ Technologies Used

### Programming Language

* Python

### Data Processing

* Pandas
* NumPy

### Data Visualization

* Matplotlib

### Development Environment

* Jupyter Notebook

---

# 🧹 Data Cleaning

The dataset contained missing values, inconsistent categorical values, incorrect data types, and invalid values.

### Missing Values

The following cleaning steps were performed:

* Rows with missing `name` values were removed.
* Missing `year` values were filled using the median year.
* Rows with missing `selling_price` values were removed.
* Missing `transmission` values were filled using the most frequent value.

The most common transmission type was **Manual**, so it was used to fill missing transmission values.

---

### Data Type Correction

The `km_driven` column was originally stored as an object even though it contained numeric values.

It was converted into a numeric data type:

```python
df["km_driven"] = pd.to_numeric(df["km_driven"], errors="coerce")
```

This allowed mileage to be used correctly in numerical analysis.

---

### Categorical Data Cleaning

Several categorical columns contained spelling inconsistencies.

#### Fuel Type

Different spellings of Petrol were standardized:

```text
Petrrol → Petrol
Petorl  → Petrol
Petrl   → Petrol
```

Different spellings of Diesel were also standardized:

```text
Diesl  → Diesel
Deisel → Diesel
```

After cleaning, the main fuel categories included:

* Petrol
* Diesel
* CNG
* LPG
* Electric

---

### Seller Type Cleaning

Inconsistent seller type values were standardized.

Examples:

```text
Indvidual → Individual
Indivudal → Individual

Deelar → Dealer
Dealerr → Dealer
```

The cleaned categories included:

* Individual
* Dealer
* Trustmark Dealer

---

### Invalid Values

Rows containing negative selling prices were removed because a vehicle cannot have a negative selling price.

```python
df = df[df["selling_price"] >= 0]
```

---

# ⚙️ Feature Engineering

Several new features were created to make the dataset more useful for analysis.

---

## 1. Car Age

A new feature called `car_age` was created based on the manufacturing year.

```python
df["car_age"] = 2026 - df["year"]
```

This feature represents the approximate age of each vehicle.

Example:

| Year | Car Age |
| ---: | ------: |
| 2017 |       9 |
| 2014 |      12 |
| 2012 |      14 |
| 2007 |      19 |

---

## 2. Brand

The car brand was extracted from the first word of the `name` column.

```python
df["brand"] = (
    df["name"].dropna().astype(str).apply(lambda x: x.split()[0])
)
```

Example:

| Car Name                 | Brand   |
| ------------------------ | ------- |
| Maruti Wagon R LXI Minor | Maruti  |
| Hyundai Verna 1.6 SX     | Hyundai |
| Datsun RediGO T Option   | Datsun  |
| Honda Amaze VX i-DTEC    | Honda   |

---

## 3. Kilometers Per Year

A `km_per_year` feature was created to estimate the average yearly usage of each vehicle.

```python
df["km_per_year"] = df["km_driven"] / np.maximum(df["car_age"], 1)
```

This provides a more useful measure of vehicle usage than total kilometers alone because it considers the vehicle's age.

---

## 4. Price Category

Vehicles were grouped into three price categories:

| Category  |       Price Range |
| --------- | ----------------: |
| Budget    |         < 250,000 |
| Mid-Range | 250,000 – 600,000 |
| Luxury    |         > 600,000 |

The classification was created using:

```python
def classify_price(price):
    if price < 250000:
        return "Budget"
    elif price <= 600000:
        return "Mid-Range"
    else:
        return "Luxury"

df["price_category"] = df["selling_price"].apply(classify_price)
```

---

# 📈 Exploratory Data Analysis

## 💰 Selling Price Distribution

The selling price was analyzed using descriptive statistics and a histogram.

### Price Statistics

| Statistic          |     Value |
| ------------------ | --------: |
| Mean               |   506,720 |
| Median             |   350,000 |
| Minimum            |    20,000 |
| Maximum            | 8,900,000 |
| Standard Deviation |   590,322 |

The mean selling price is higher than the median, indicating that the dataset is influenced by a smaller number of high-priced vehicles.

The large difference between the median and maximum price also shows the presence of expensive vehicles compared with the majority of the dataset.

---

## 🚗 Car Age Distribution

Car age was analyzed to understand the distribution of vehicle ages in the dataset.

```python
plt.hist(df["car_age"], bins=20)
plt.title("Car Age Distribution")
plt.xlabel("Age (Years)")
plt.ylabel("Count")
plt.show()
```

The analysis shows that the dataset contains a wide range of vehicle ages, allowing comparisons between newer and older used cars.

---

## 🏷️ Top Car Brands

The most common brands in the dataset were:

| Rank | Brand    | Count |
| ---: | -------- | ----: |
|    1 | Maruti   | 1,097 |
|    2 | Hyundai  |   708 |
|    3 | Tata     |   313 |
|    4 | Mahindra |   307 |
|    5 | Honda    |   218 |

**Maruti** is clearly the most represented brand in the dataset, followed by **Hyundai**.

This indicates that the dataset is not evenly distributed across brands.

---

## 📉 Car Age vs Selling Price

The relationship between vehicle age and selling price was explored using a scatter plot.

```python
plt.scatter(df["car_age"], df["selling_price"], alpha=0.5)
plt.title("Car Age vs Selling Price")
plt.xlabel("Car Age (Years)")
plt.ylabel("Selling Price")
plt.show()
```

The analysis helps investigate the relationship between vehicle age and resale value.

In general, newer vehicles tend to have higher selling prices, while older vehicles are more concentrated in lower price ranges.

---

## ⛽ Fuel Type Analysis

Fuel types were analyzed to compare their distribution and relationship with selling prices.

The dataset originally contained several spelling variations for Petrol and Diesel, which were standardized during data cleaning.

After cleaning, **Diesel** and **Petrol** represent the dominant fuel categories.

The analysis also compares vehicle age and selling price across different fuel types.

---

## 🛣️ Mileage & Vehicle Usage

The `km_driven` feature was analyzed to understand how heavily vehicles had been used.

A new `km_per_year` feature was also created to account for vehicle age.

This provides a better representation of yearly vehicle usage and helps compare cars with different ages.

---

## ⚙️ Transmission Analysis

The dataset contains two main transmission types:

* Manual
* Automatic

Manual transmission is the dominant category in the dataset.

Missing transmission values were filled using the mode, which was **Manual**.

---

## 👤 Owner Analysis

The ownership distribution shows that **First Owner** vehicles represent the largest group.

The recorded ownership categories include:

* First Owner
* Second Owner
* Third Owner
* Fourth & Above Owner
* Test Drive Car

The counts observed in the dataset were:

| Owner Type           | Count |
| -------------------- | ----: |
| First Owner          | 2,294 |
| Second Owner         |   885 |
| Third Owner          |   242 |
| Fourth & Above Owner |    64 |
| Test Drive Car       |    12 |

---

# 🔍 Key Findings

### 1. Price Distribution

Most vehicles are concentrated in the lower and mid-price ranges, while a smaller number of vehicles have considerably higher selling prices.

The mean price (~506K) is higher than the median price (350K), suggesting that high-priced vehicles influence the overall price distribution.

### 2. Age & Depreciation

Vehicle age is an important factor when analyzing used car prices.

Newer cars generally have higher selling prices, while older cars tend to have lower resale values.

### 3. Brand Dominance

Maruti is the most common brand in the dataset, followed by Hyundai.

The top five brands are:

**Maruti, Hyundai, Tata, Mahindra, and Honda.**

### 4. Fuel Type Trends

Diesel and Petrol are the dominant fuel types in the dataset.

Fuel type provides another useful dimension for comparing vehicle prices and characteristics.

### 5. Mileage & Usage

`km_driven` provides an indication of how heavily a vehicle has been used.

The engineered `km_per_year` feature provides additional context by considering the vehicle's age.

### 6. Price Categories

Dividing vehicles into Budget, Mid-Range, and Luxury categories makes it easier to compare cars across different price levels.

### 7. Data Quality

The dataset required several cleaning steps, including:

* Handling missing values
* Correcting data types
* Standardizing categorical values
* Removing invalid negative prices
* Correcting spelling inconsistencies

These steps improved the reliability of the analysis.

---

# 📌 Executive Summary

This project demonstrates an end-to-end data analysis workflow on a used-car dataset containing **4,345 records and 8 original features**.

The analysis focused on understanding used car prices and identifying patterns related to vehicle age, brand, fuel type, mileage, transmission, and ownership.

After cleaning and preprocessing the dataset, several useful features were engineered, including `car_age`, `brand`, `km_per_year`, and `price_category`.

The analysis shows that **vehicle age, brand, fuel type, mileage, and other vehicle characteristics** can provide valuable information for understanding differences in used car prices.

---

# 💡 Business Insights

* Used car prices are concentrated mainly in lower and mid-range segments.
* A smaller number of expensive vehicles significantly increases the average selling price.
* Newer vehicles generally have stronger resale values.
* Maruti and Hyundai represent a large share of the available vehicles.
* Mileage can help indicate the level of vehicle usage.
* Price categories make vehicle comparison easier for different customer segments.
* Clean and standardized data provides a stronger foundation for further analysis or predictive modeling.

---

# 🤖 Machine Learning Readiness

The cleaned and feature-engineered dataset can serve as a foundation for future predictive modeling.

Potential future applications include:

* Used car price prediction
* Price category classification
* Estimating vehicle resale value
* Identifying the factors that have the strongest influence on selling price

---

# 🚀 Future Work

Possible extensions of the project include:

* Building a machine learning model to predict selling prices.
* Performing detailed correlation analysis.
* Applying outlier detection techniques.
* Exploring brand-specific pricing patterns.
* Comparing average prices across fuel and transmission types.
* Creating an interactive visualization dashboard.
* Improving feature engineering with additional vehicle characteristics.

---

# 📂 Project Structure

```text
used-cars-data-analysis/
│
├── data/
│   └── used_cars.csv
│
├── notebooks/
│   └── used_cars_analysis.ipynb
│
├── visualizations/
│   └── charts/
│
├── README.md
└── requirements.txt
```

---

# 📦 Requirements

Main Python libraries used in the analysis:

```text
pandas
numpy
matplotlib
jupyter
```

Install the required libraries with:

```bash
pip install pandas numpy matplotlib jupyter
```

---

# 👩‍💻 Project Type

**Data Analysis | Exploratory Data Analysis | Data Cleaning | Feature Engineering | Data Visualization**

---

# 📄 License

This project is intended for educational and portfolio purposes.


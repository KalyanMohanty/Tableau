# Contents
  - LOOKUP()
  - Year-over-Year (YoY) Calculation
  - Common Window Functions in Tableau
  - BUFFER()
  - CONTAINS()
  - Date Functions
  - FIRST()
  - LAST()
  - Regex or RegExp
  - PREVIOUS_VALUE
  - NOW() vs TODAY()
  - ZN()

#### comments:
```















```

### QUESTION 1.
The `LOOKUP` function in Tableau is used to access values from previous or next rows in a table. It’s especially useful for calculating differences or changes over time, such as Year-over-Year (YoY) or Month-over-Month (MoM) calculations.


### Syntax
```tableau
LOOKUP(expression, [offset])

```
- **expression**: The field or calculation you want to retrieve.
- **offset**: The number of rows (or periods) to look ahead or behind. This can be positive, negative, or zero.
  - **0**: Current row.
  - **1**: Next row.
  - **-1**: Previous row.

### How `LOOKUP` Works
`LOOKUP` fetches a value relative to the current row, based on the offset provided. For instance:
- **LOOKUP(SUM([Sales]), -1)** looks at the value of `SUM([Sales])` from the previous row.
- **LOOKUP(SUM([Sales]), 1)** would look at the value of `SUM([Sales])` from the next row.

### Examples


#### 1. Year-over-Year Growth
To calculate Year-over-Year (YoY) growth, you could use:
```tableau
(SUM([Sales]) - LOOKUP(SUM([Sales]), -1)) / ABS(LOOKUP(SUM([Sales]), -1))
```
Here:
- `SUM([Sales])` is the current year’s sales.
- `LOOKUP(SUM([Sales]), -1)` retrieves the previous year’s sales.
- The formula calculates the percentage change from the previous year.

#### 2. Moving Average
To create a 3-month moving average, you can use `LOOKUP` to refer to previous rows:
```tableau
(SUM([Sales]) + LOOKUP(SUM([Sales]), -1) + LOOKUP(SUM([Sales]), -2)) / 3
```
This takes the current month’s sales, plus the previous two months, and averages them.

### Important Notes
- `LOOKUP` relies on the order and structure of data in the view, so your table setup affects how it calculates. 
- It requires that there be enough rows for it to look back or forward, or it will return `NULL` (for example, the first row can’t have a `LOOKUP(SUM([Sales]), -1)` because there’s no previous row).

### Summary
`LOOKUP` is ideal for comparisons across rows, like trends over time. By setting up your date field and offsets correctly, you can use it for various comparisons, from simple differences to more complex moving calculations.

To calculate Year-over-Year (YoY) and Month-over-Month (MoM) growth in Tableau, you can use table calculations to compare values from the current period with values from the previous period. Here’s how to set up these calculations:
#### comments:
```















```
#
### QUESTION 2.
### Year-over-Year (YoY) Calculation

1. **Drag your date field to the Columns shelf** and set it to the year level (e.g., `YEAR(Date)`).
2. **Place your measure (like Sales or Profit)** on the Rows shelf.
3. **Right-click the measure on the Rows shelf** and select **Quick Table Calculation > Year-over-Year Growth**.
4. Tableau will compute the YoY percentage change for each year.

If you want to create a custom calculation:
- Use a calculated field with the formula below:

   ```tableau
   (SUM([Sales]) - LOOKUP(SUM([Sales]), -1)) / ABS(LOOKUP(SUM([Sales]), -1))
   ```
  
   This formula calculates the percentage change from the previous year by taking the current year's value, subtracting the previous year's value, and dividing by the absolute value of the previous year.

### Month-over-Month (MoM) Calculation

1. **Drag your date field to the Columns shelf** and set it to the month level (e.g., `MONTH(Date)`).
2. **Place your measure on the Rows shelf**.
3. **Right-click the measure on the Rows shelf** and select **Quick Table Calculation > Month-over-Month Growth**.

For a custom MoM calculation, create a calculated field using this formula:

   ```tableau
   (SUM([Sales]) - LOOKUP(SUM([Sales]), -1)) / ABS(LOOKUP(SUM([Sales]), -1))
   ```

This formula works similarly to the YoY calculation, but it now compares the measure to the previous month.

### Additional Tips

- **Filter by Year/Month**: You may need to filter your data to display only the relevant months/years for a more focused view.
- **Format the result as a percentage**: Right-click on the calculated field, go to **Format**, and set it to **Percentage** to make the values more readable.

Window functions in Tableau, also known as **table calculations**, allow you to perform calculations across a range of data within a specified "window" of rows. This is different from row-level or aggregate calculations, as window functions compute values across multiple rows relative to the current row. They are useful for calculating things like running totals, moving averages, rankings, and percentages.

#
### QUESTION 3.
### Common Window Functions in Tableau
1. **WINDOW_SUM**: Sums values across a specified range.
2. **WINDOW_AVG**: Calculates the average within a specified window.
3. **WINDOW_MAX** and **WINDOW_MIN**: Finds the maximum or minimum within the window.
4. **WINDOW_COUNT**: Counts rows within the window.
5. **RANK** and **RANK_DENSE**: Ranks values across a window.

### Syntax
```tableau
WINDOW_FUNCTION(expression, [start, end])
```
- **expression**: The calculation or field to apply within the window.
- **start** and **end**: Optional. Define the starting and ending point of the window (relative to the current row).

### Application of Window Functions with Examples

#### 1. Running Total using `WINDOW_SUM`
A running total calculates the cumulative sum of a measure across rows.

Example: **Calculate Running Total Sales**
```tableau
WINDOW_SUM(SUM([Sales]))
```
Here, `WINDOW_SUM(SUM([Sales]))` calculates a running total of `Sales`. For each row, Tableau calculates the sum of all `Sales` values up to that row.

#### 2. Moving Average using `WINDOW_AVG`
Moving averages smooth out fluctuations by averaging values over a certain number of periods.

Example: **3-Month Moving Average of Sales**
```tableau
WINDOW_AVG(SUM([Sales]), -2, 0)
```
In this formula:
- `WINDOW_AVG` calculates the average within a specified window.
- `SUM([Sales])` is the measure we want to average.
- `-2, 0` means that the window spans from two rows before the current row (i.e., previous two months) up to the current row.

#### 3. Ranking using `RANK` or `RANK_DENSE`
Ranks each row based on the value of a field, which is helpful for top-N analysis or comparing values within groups.

Example: **Rank Products by Sales**
```tableau
RANK(SUM([Sales]))
```
This formula ranks products by `Sales`. It calculates the rank based on the sum of `Sales` for each product, assigning a rank of `1` to the product with the highest sales.

#### 4. Percent of Total using `WINDOW_SUM`
To calculate a percentage, we can divide each row’s value by the total sum across all rows in the window.

Example: **Calculate Sales Percentage of Total**
```tableau
SUM([Sales]) / WINDOW_SUM(SUM([Sales]))
```
This formula calculates the percentage of each row's `Sales` relative to the total `Sales` across the window.

#### 5. Window Max or Min using `WINDOW_MAX` and `WINDOW_MIN`
These functions help find the maximum or minimum value within a window, which is useful for identifying peaks or troughs in data.

Example: **Calculate Maximum Sales over a Year**
```tableau
WINDOW_MAX(SUM([Sales]))
```
This formula returns the maximum sales value within the defined window, providing insight into the peak performance of sales across a year.

### Important Notes
- **Window functions** are typically used with table calculations, and the window size depends on the data partitioning (how you "slice" the data).
- Adjusting **table calculation settings** (like "Compute Using") is essential to ensure the calculation uses the correct data range.
  
### Summary
Window functions are powerful for aggregating and analyzing data across rows. They provide insights into patterns, trends, and comparisons that aren't possible with row-level calculations alone, making them invaluable for data exploration and reporting in Tableau.


In Tableau, the **BUFFER** function creates a geographic buffer around a specific point on a map. This function is especially useful for spatial analysis, such as determining areas within a certain distance from a given location. For example, you could use BUFFER to find all customers or stores within a 5-mile radius of a central location.
#### comments:
```















```

#
### QUESTION 4.
### Syntax
```tableau
BUFFER(spatial_point, distance, units)
```
- **spatial_point**: The geographic point to create the buffer around. This could be a field with geographic coordinates or a known location.
- **distance**: The radius of the buffer around the point, expressed as a numeric value.
- **units**: The unit of measurement for the distance (e.g., `"mi"` for miles, `"km"` for kilometers, `"m"` for meters).

### Supported Units
- `"km"`: Kilometers
- `"mi"`: Miles
- `"m"`: Meters

### How BUFFER Works
The BUFFER function draws a circle (or buffer) around the specified point based on the given distance and units. Any geographic points or shapes that fall within this buffer area can then be highlighted or filtered in your analysis.

### Example: Find All Stores within a 10-mile Radius

Let's say you have a dataset of store locations and you want to highlight all stores within a 10-mile radius of a particular central store.

1. **Create a Buffer**: Create a calculated field named `Store Buffer` with this formula:
   ```tableau
   BUFFER([Store Location], 10, "mi")
   ```
   Here, `[Store Location]` is the geographic point representing each store, `10` is the distance, and `"mi"` is the unit.

2. **Visualize the Buffer**: Drag the `Store Buffer` field to your map. Tableau will create a circle around each store, representing a 10-mile radius.

3. **Filter Points Within the Buffer**: If you want to see only stores that fall within this buffer, you can apply a spatial join or filter by using the `MAKEPOINT` function with latitude and longitude to create your target location and comparing it against the buffer.

In Tableau, the **CONTAINS** function is a logical function that checks whether a specific substring exists within a larger string. It returns **TRUE** if the substring is found and **FALSE** if it is not. This function is especially useful for filtering, highlighting, or categorizing data based on partial matches in text fields.
#### comments:
```















```
#
### QUESTION 5.
### Syntax
```tableau
CONTAINS(string, substring)
```
- **string**: The text in which you want to search.
- **substring**: The text you want to search for within the larger string.

### Example Use Cases for CONTAINS

#### 1. Filtering Data Based on a Keyword
If you have a dataset with product names and want to filter for products that contain the word "Deluxe," you can create a calculated field like this:
```tableau
CONTAINS([Product Name], "Deluxe")
```
This returns **TRUE** for any product name containing "Deluxe," which you can then use as a filter to show only matching products.

#### 2. Categorizing Data Based on Keywords
If you have a column with customer feedback, you might want to create categories based on specific keywords. For example, if you want to categorize reviews mentioning "service," you could use:
```tableau
IF CONTAINS([Customer Feedback], "service") THEN "Mentions Service"
ELSE "Does Not Mention Service" END
```
This categorizes feedback into "Mentions Service" or "Does Not Mention Service" based on the presence of the word "service."

#### 3. Highlighting Data Based on Partial Matches
If you want to visually highlight rows containing a specific term, such as “Urgent” in a comments field, you can use CONTAINS in a calculated field:
```tableau
IF CONTAINS([Comments], "Urgent") THEN "Highlight" ELSE "No Highlight" END
```
You can then use this field in your visualization to apply different colors to highlight urgent cases.

### Important Notes
- **Case Sensitivity**: The CONTAINS function in Tableau is **case-insensitive** by default, so “Deluxe” and “deluxe” are treated as the same.
- **Partial Matching**: CONTAINS only looks for exact substring matches. For more complex pattern matching, you may need to use other functions or combine it with **REGEXP** functions.

### Summary
The CONTAINS function is straightforward but powerful, enabling text-based filtering, highlighting, and categorization. This can be particularly helpful when working with datasets that have descriptive text fields where partial matches need to be identified.
#### comments:
```















```
#
### QUESTION 6.
Tableau provides a wide range of date functions that help manipulate and perform calculations with dates. Here’s a breakdown of the most commonly used **date functions** in Tableau, along with examples.

### 1. **DATE**
Converts a number, string, or datetime expression into a date. If a field is not already in date format, this function can help convert it.

**Syntax**: `DATE(expression)`

**Example**: `DATE("2023-01-15")` converts the string `"2023-01-15"` to a date.

### 2. **DATETRUNC**
Truncates the date to the specified date part, such as month, quarter, or year, making it useful for grouping data into periods.

**Syntax**: `DATETRUNC(date_part, date)`

**Example**: `DATETRUNC('quarter', [Order Date])` returns the date truncated to the start of the quarter.

### 3. **DATEADD**
Adds a specified number of date parts (like days, months, years) to a date, shifting it forward or backward.

**Syntax**: `DATEADD(date_part, interval, date)`

**Example**: `DATEADD('month', 3, [Order Date])` adds three months to each order date.

### 4. **DATEDIFF**
Calculates the difference between two dates in the specified units, such as days, months, or years.

**Syntax**: `DATEDIFF(date_part, start_date, end_date)`

**Example**: `DATEDIFF('day', [Order Date], [Ship Date])` gives the number of days between an order date and its ship date.

### 5. **DATEPART**
Returns a specific part of a date as an integer, like the day, month, quarter, or year.

**Syntax**: `DATEPART(date_part, date)`

**Example**: `DATEPART('month', [Order Date])` returns the month part of the order date as a number (e.g., 1 for January).

### 6. **YEAR, MONTH, DAY**
Extracts the year, month, or day as an integer from a date.

**Syntax**: 
- `YEAR(date)`
- `MONTH(date)`
- `DAY(date)`

**Example**: `YEAR([Order Date])` returns the year of the order date.

### 7. **NOW**
Returns the current date and time in the format `datetime`.

**Syntax**: `NOW()`

**Example**: `NOW()` could return `2023-10-01 14:45:32`.

### 8. **TODAY**
Returns the current date without the time portion.

**Syntax**: `TODAY()`

**Example**: `TODAY()` returns today’s date in the format `2023-10-01`.

### 9. **MAKEDATE**
Creates a date from three integer arguments: year, month, and day.

**Syntax**: `MAKEDATE(year, month, day)`

**Example**: `MAKEDATE(2023, 1, 15)` returns `2023-01-15`.

### 10. **MAKEDATETIME**
Creates a `datetime` value from a date and a time.

**Syntax**: `MAKEDATETIME(date, time)`

**Example**: `MAKEDATETIME("2023-01-15", "10:30:00")` returns `2023-01-15 10:30:00`.

### 11. **MIN / MAX**
Returns the minimum or maximum date in a dataset, often used in aggregation.

**Syntax**: 
- `MIN(date)`
- `MAX(date)`

**Example**: `MIN([Order Date])` returns the earliest order date.

### 12. **ISDATE**
Tests whether a string can be interpreted as a valid date and returns TRUE or FALSE.

**Syntax**: `ISDATE(string)`

**Example**: `ISDATE("2023-01-15")` returns TRUE, while `ISDATE("hello")` returns FALSE.

### 13. **DATEPARSE**
Converts a string into a date by specifying a custom date format, making it useful for non-standard date formats.

**Syntax**: `DATEPARSE(format, string)`

**Example**: `DATEPARSE("yyyy-MM-dd", "2023-01-15")` returns a date for the given string.

### 14. **DATE_NAME**
Returns the name of a date part, like the month or weekday name, as a string.

**Syntax**: `DATENAME(date_part, date)`

**Example**: `DATENAME('month', [Order Date])` returns the name of the month (e.g., “January”) for each order date.

### 15. **WEEKDAY**
Returns the weekday of a date as an integer (1 = Sunday, 2 = Monday, etc.).

**Syntax**: `WEEKDAY(date)`

**Example**: `WEEKDAY([Order Date])` returns the weekday of each order date.

### 16. **QUARTER**
Returns the quarter of the date as an integer (1 to 4).

**Syntax**: `QUARTER(date)`

**Example**: `QUARTER([Order Date])` returns the quarter in which the order date falls.

### 17. **MONTHNAME and DAYNAME**
Returns the month or day as a name (e.g., “January” or “Monday”).

**Syntax**: 
- `MONTHNAME(date)`
- `DAYNAME(date)`

**Example**: `MONTHNAME([Order Date])` returns the month name (e.g., “January”) for each order date.

### Summary
These date functions enable powerful analysis by allowing you to manipulate, extract, and format dates to gain insights into trends, timing, and durations in your data.

In Tableau, **FIRST** is a table calculation function that returns the relative position of a row from the first row in a partition. It is commonly used in table calculations to perform operations based on row position, like calculating cumulative totals or comparing values to the first entry.
#### comments:
```















```

#
### QUESTION 7.
### Syntax
```tableau
FIRST()
```

### How It Works
- **FIRST()** returns an integer representing the number of rows between the current row and the first row in the partition.
- The value is **0** for the first row, **1** for the second row, **2** for the third row, and so on.
- It can return negative values if used in rows after the first in the opposite direction (e.g., the last row is `-1`, the second-last is `-2`).

### Example Use Cases for FIRST

#### 1. Identifying the First Row in a Partition
To highlight the first row in each partition (such as a category), you can create a calculated field like this:
```tableau
IF FIRST() = 0 THEN "First Row" ELSE "Other Rows" END
```
This calculation labels the first row as "First Row" and all subsequent rows as "Other Rows."

#### 2. Calculating Difference from the First Row
If you want to calculate the difference between each row and the first row in a partition, you can use `FIRST()` with an `INDEX` calculation.

For example, to calculate the difference in `Sales` from the first row:
```tableau
SUM([Sales]) - LOOKUP(SUM([Sales]), -FIRST())
```
Here, `LOOKUP(SUM([Sales]), -FIRST())` retrieves the `Sales` value in the first row, and the formula calculates the difference between the current row’s sales and the first row’s sales.

#### 3. Filtering to Show Only the First Row
If you want to show only the first row in each partition, you can use a filter with `FIRST()`:
```tableau
FIRST() = 0
```
This filter condition keeps only the first row of each partition visible.

### Important Notes
- **FIRST()** is a **table calculation** and depends on the table’s structure and the **Compute Using** setting. It calculates based on the view, so how you sort and partition your data affects its results.
- **FIRST()** is similar to **INDEX()**, but **INDEX()** returns the position number within the partition rather than the offset from the first row.

### Summary
The **FIRST** function is useful for working with row positions and offsets, especially when creating comparisons or filtering based on relative positions within partitions.
#### comments:
```















```

#
### QUESTION 8.
In Tableau, **LAST** is a table calculation function that returns the relative position of a row from the last row in a partition. This is particularly useful when you want to perform calculations or filtering based on the position of rows relative to the end of a data partition.

### Syntax
```tableau
LAST()
```

### How It Works
- **LAST()** returns an integer representing the number of rows between the current row and the last row in the partition.
- The last row in the partition will have a **0** value, the second-last row will have **1**, the third-last row will have **2**, and so on.
- This function can be useful for identifying, filtering, or calculating values for the last few rows in each partition.

### Example Use Cases for LAST

#### 1. Identifying the Last Row in a Partition
If you want to highlight or identify the last row in a partition (e.g., the most recent entry in each category), you can use this calculated field:
```tableau
IF LAST() = 0 THEN "Last Row" ELSE "Other Rows" END
```
This formula will label the last row as "Last Row" and all other rows as "Other Rows."

#### 2. Calculating Difference from the Last Row
To calculate the difference between each row and the last row in a partition, you can use `LAST()` with the `LOOKUP` function.

For example, to calculate the difference in `Sales` from the last row:
```tableau
SUM([Sales]) - LOOKUP(SUM([Sales]), LAST())
```
In this case, `LOOKUP(SUM([Sales]), LAST())` retrieves the `Sales` value of the last row in the partition, and the formula calculates the difference between the current row’s sales and the last row’s sales.

#### 3. Filtering to Show Only the Last Row
If you only want to display the last row in each partition, you can apply a filter using `LAST()`:
```tableau
LAST() = 0
```
This filter will display only the last row for each partition.

#### 4. Calculating a Moving Sum of the Last Few Rows
If you want to create a rolling calculation that includes only the last few rows, **LAST()** can be used to dynamically select those rows. For example, if you want a moving sum of the last three rows, you could use:
```tableau
IF LAST() <= 2 THEN SUM([Sales]) END
```
This formula will compute the sum of sales only for the last three rows (0, 1, and 2 relative to the last row).

### Important Notes
- **LAST()** is a **table calculation** that depends on how the data is structured and partitioned in the view. Adjusting the **Compute Using** setting affects how `LAST()` calculates values.
- **LAST()** is similar to **FIRST()**, but **LAST()** counts rows from the end of the partition, while **FIRST()** counts from the beginning.

### Summary
The **LAST** function is a powerful tool for working with the last row(s) in a data partition. It’s ideal for identifying, filtering, and calculating values based on the last position in each partition, making it valuable for end-of-period reporting or trailing analyses.
#### comments:
```















```


#
### QUESTION 9.
Regular Expressions (often abbreviated as **Regex** or **RegExp**) are sequences of characters that define a search pattern. They are used to find, match, or manipulate strings based on specific patterns. In Tableau, regular expressions are powerful tools for pattern-matching and text extraction in fields like data cleaning, validation, and extraction.

Tableau provides four main regex functions:

### 1. **REGEXP_MATCH**
Checks if a string matches a regular expression pattern and returns **TRUE** if it does, otherwise **FALSE**. It’s useful for validating data or filtering specific patterns.

**Syntax**:
```tableau
REGEXP_MATCH(string, pattern)
```

**Example**: 
To check if a field contains an email address:
```tableau
REGEXP_MATCH([Email], "^[\w\.-]+@[\w\.-]+\.\w+$")
```
This pattern matches a typical email format (e.g., `name@example.com`).

### 2. **REGEXP_REPLACE**
Replaces parts of a string that match a regular expression pattern with a new substring.

**Syntax**:
```tableau
REGEXP_REPLACE(string, pattern, replacement)
```

**Example**: 
To remove all non-numeric characters from a phone number:
```tableau
REGEXP_REPLACE([Phone Number], "[^\d]", "")
```
This pattern replaces all non-digit characters (`[^\d]`) with an empty string, leaving only numbers.

### 3. **REGEXP_EXTRACT**
Extracts substrings from a string based on a regular expression pattern. It returns the first match found, making it useful for pulling out specific parts of a string.

**Syntax**:
```tableau
REGEXP_EXTRACT(string, pattern)
```

**Example**: 
To extract the domain from an email address:
```tableau
REGEXP_EXTRACT([Email], "@([\w\.-]+)")
```
This pattern matches any characters after the `@` symbol, extracting the domain (e.g., `example.com` from `user@example.com`).

### 4. **REGEXP_EXTRACT_N**
Extracts the **n-th capture group** in the pattern from a string, which is useful if your pattern has multiple parts and you want to extract a specific one.

**Syntax**:
```tableau
REGEXP_EXTRACT_N(string, pattern, n)
```

**Example**:
To extract the second word in a sentence:
```tableau
REGEXP_EXTRACT_N([Sentence], "\b(\w+)\b", 2)
```
This pattern uses word boundaries (`\b`) to capture words and returns the second word.

---

### Common Patterns Used in Regular Expressions
- **`.`**: Matches any single character.
- **`*`**: Matches zero or more of the preceding character.
- **`+`**: Matches one or more of the preceding character.
- **`?`**: Matches zero or one of the preceding character.
- **`\d`**: Matches any digit (equivalent to `[0-9]`).
- **`\w`**: Matches any word character (alphanumeric plus underscore).
- **`^`**: Asserts the start of a string.
- **`$`**: Asserts the end of a string.
- **`[]`**: Matches any single character inside the brackets.
- **`()`**: Defines a capture group, which can be used to extract specific parts.

---

### Example Use Cases for Regular Expressions in Tableau

1. **Validating Email Addresses**: 
   ```tableau
   REGEXP_MATCH([Email], "^[\w\.-]+@[\w\.-]+\.\w+$")
   ```
   Use this formula to validate email addresses in your data.

2. **Extracting Area Code from Phone Numbers**:
   ```tableau
   REGEXP_EXTRACT([Phone Number], "\((\d{3})\)")
   ```
   This extracts a three-digit area code from a phone number in the format `(123) 456-7890`.

3. **Replacing Special Characters**:
   ```tableau
   REGEXP_REPLACE([Product Code], "[^a-zA-Z0-9]", "")
   ```
   This removes any non-alphanumeric characters from a product code.

4. **Splitting Names**: 
   To extract the first name from a "First Last" name format:
   ```tableau
   REGEXP_EXTRACT([Full Name], "^(\w+)")
   ```
   This extracts the first word, assuming it’s the first name.

---

### Summary
Regular expressions in Tableau are versatile for data transformation, validation, and pattern matching, helping users clean and format text data for further analysis. Using regex effectively can make complex text manipulation much easier in Tableau.
#### comments:
```














```


#
### QUESTION 10.
The **PREVIOUS_VALUE** function in Tableau returns the last calculated value in a sequence, which makes it useful for running totals, cumulative calculations, or calculations where you need to maintain a value from one row to the next. **PREVIOUS_VALUE** can be used only in calculated fields and is particularly helpful for scenarios that involve carrying a value from one row to another within the same partition.

### Syntax
```tableau
PREVIOUS_VALUE(expression)
```

### Important Details
- **PREVIOUS_VALUE** returns the specified value from the prior row, so it depends on the table's structure and how the data is partitioned in the view.
- The first row in a partition can be initialized with a starting value (often set to **0** or **NULL**).
- Each subsequent row will use the previous row's calculation, allowing for accumulations or running calculations across rows.

### Example Use Cases for PREVIOUS_VALUE

#### 1. Running Total (Cumulative Sum)
Suppose you want to calculate a running total of `Sales`. You could do this with **PREVIOUS_VALUE** by adding each row’s `Sales` value to the cumulative sum of the previous rows:

```tableau
PREVIOUS_VALUE(0) + SUM([Sales])
```
In this formula:
- **PREVIOUS_VALUE(0)** initializes the first row with 0.
- As Tableau processes each row, it adds `SUM([Sales])` to the value from the previous row, resulting in a cumulative sum.

#### 2. Carrying Forward Calculations or Maintaining State
If you want to create a counter that increments for each row, you could use:

```tableau
PREVIOUS_VALUE(0) + 1
```
This calculation initializes the first row at 0 and then increments by 1 with each subsequent row.

#### 3. Simulating Conditional Running Total
If you want a running total that only adds `Sales` when certain conditions are met, you can use an **IF** condition:

```tableau
IF [Condition] THEN PREVIOUS_VALUE(0) + SUM([Sales]) ELSE PREVIOUS_VALUE(0) END
```
This formula updates the running total only for rows that satisfy `[Condition]`. For rows that do not meet the condition, it carries forward the last calculated total.

### Important Notes
- **PREVIOUS_VALUE** can only be used for recursive calculations, where each row’s result depends on the previous row.
- **PREVIOUS_VALUE** cannot be combined with row-level calculations; it only applies within table calculations.
- Changing the order or partitioning of data in the view can affect **PREVIOUS_VALUE** results.

### Summary
The **PREVIOUS_VALUE** function in Tableau is a unique table calculation that allows you to carry forward values across rows, making it perfect for cumulative totals and running calculations where each row builds upon the last.
#### comments:
```















```

#
### QUESTION 11.
In Tableau, **NOW()** and **TODAY()** are date functions that retrieve the current date or datetime. Here’s a breakdown of how they work and the key differences:

### 1. **NOW()**
- **NOW()** returns the current **date and time**.
- The function provides a **datetime** data type, which includes both date and time.
- Useful for time-based calculations where you need the exact current moment, such as comparing specific timestamps or calculating elapsed time down to the second.

**Example**:
```tableau
NOW() 
```
If you used this function on November 1, 2024, at 10:00 AM, the output would be something like `2024-11-01 10:00:00`.

#### Example Use Case for NOW():
- Calculating the exact number of hours or minutes from a timestamp to the current time:
  ```tableau
  DATEDIFF('minute', [Order Date], NOW())
  ```
  This calculates the number of minutes between the order date and the current datetime.

### 2. **TODAY()**
- **TODAY()** returns only the **current date** (no time component).
- The function provides a **date** data type, so the time is automatically set to 12:00:00 AM.
- Useful for date-only calculations, like age calculations, deadlines, or comparisons that don’t need the precise time.

**Example**:
```tableau
TODAY() 
```
If you used this function on November 1, 2024, the output would be `2024-11-01` (just the date).

#### Example Use Case for TODAY():
- Calculating the number of days from a date to today:
  ```tableau
  DATEDIFF('day', [Start Date], TODAY())
  ```
  This finds the number of days between the start date and today’s date.

---

### Summary of Differences

| Function | Returns            | Data Type | Use Case                          |
|----------|---------------------|-----------|-----------------------------------|
| **NOW()**   | Date and Time      | DateTime  | For exact date and time calculations |
| **TODAY()** | Date only          | Date      | For day-based calculations           |

**Choose NOW()** when you need both date and time; **choose TODAY()** when you only need the date.
#### comments:
```















```

#
### QUESTION 12.
In Tableau, the **ZN** function is used to handle null values in numeric fields by converting them to zero. This is particularly useful when performing calculations that might involve nulls, as it ensures that nulls are treated as zeros, preventing errors or unexpected results.

**Syntax:**
```tableau
ZN(expression)
```

**Parameters:**
- `expression`: The numeric field or calculation you want to evaluate.

**Return Value:**
- If the `expression` is **not null**, it returns the value of the expression.
- If the `expression` is **null**, it returns **0**.

**Example:**
Suppose you have a field `[Sales]` that contains some null values. To replace these nulls with zeros, you can use:
```tableau
ZN([Sales])
```
This calculation will return the value of `[Sales]` if it's not null; otherwise, it will return 0.

**Use Cases:**
- **Aggregations:** When aggregating data, null values can affect the results. Using `ZN` ensures that nulls are treated as zeros, leading to more accurate aggregations.
- **Calculations:** In calculations involving multiple fields, null values can cause errors or unexpected results. Wrapping fields with `ZN` can prevent these issues.

**Note:**
The `ZN` function is specifically designed for numeric fields. For other data types, such as strings or dates, you can use the `IFNULL` function to replace nulls with a specified value.

#### comments:
```















```

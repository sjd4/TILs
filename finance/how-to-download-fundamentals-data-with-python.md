**How can we download fundamentals data with Python?**
------------------------------------------------------

In this post we will explore how to download fundamentals data with Python. We'll be extracting fundamentals data from [Yahoo Finance](https://finance.yahoo.com/) using the [yahoo_fin](http://theautomatic.net/yahoo_fin-documentation/) package. For more on **yahoo_fin**, including installation instructions, [check out its full documentation here](http://theautomatic.net/yahoo_fin-documentation/).

**Getting started**
-------------------

Now, let's import the *stock_info* module from **yahoo_fin**. This will provide us with the functionality we need to scrape fundamentals data from Yahoo Finance. We'll also import the **pandas** package as we'll be using that later to work with data frames.

```python
import yahoo_fin.stock_info as si
```

```python
import pandas as pd
```

Next, we'll dive into getting common company metrics, starting with P/E ratios.

**How to get P/E (Price-to-Earnings) Ratios**
---------------------------------------------

There's a couple ways to get the current P/E ratio for a company. First, we can use the **get_quote_table** method, which will extract the data found on the summary page of a stock ([see here](https://finance.yahoo.com/quote/AAPL?p=AAPL)).

|

1

 |

`quote` `=` `si.get_quote_table(``"aapl"``)`

 |

![python get P/E ratios](https://i2.wp.com/theautomatic.net/wp-content/uploads/2020/05/python-get-PE-ratio.png?w=640)

Next, let's pull the P/E ratio from the dictionary that is returned.

|

1

 |

`quote[``"PE Ratio (TTM)"``]` `# 22.71`

 |

A company's P/E ratio can also be extracted from the *get_stats_valuation* method. Running this method returns a data frame of the "Valuation Measures" on the [statistics tab for a stock](https://finance.yahoo.com/quote/AAPL/key-statistics?p=AAPL).

|

1

2

3

4

5

 |

`val` `=` `si.get_stats_valuation(``"aapl"``)`

`val` `=` `val.iloc[:,:``2``]`

`val.columns` `=` `[``"Attribute"``,` `"Recent"``]`

 |

Next, let's extract the P/E ratio.

|

1

 |

`float``(val[val.Attribute.``str``.contains(``"Trailing P/E"``)].iloc[``0``,``1``])`

 |

**How to get P/S (Price-to-Sales) Ratios**
------------------------------------------

Another popular metric is the P/S ratio. We can get the P/S ratio, along with several other other metrics, using the same *get_stats_valuation* method. Let's use the object we pulled above, currently stored as *val*.

Then, we can get the Price/Sales ratio like below.

|

1

 |

`float``(val[val.Attribute.``str``.contains(``"Price/Sales"``)].iloc[``0``,``1``])`

 |

**Getting fundamentals stats for many stocks at once**
------------------------------------------------------

Now, let's get the Price-to-Earnings and Price-to-Sales ratios for each stock in the Dow. We could also do this for a custom list of tickers as well.

|

1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

17

18

19

20

21

22

 |

`# get list of Dow tickers`

`dow_list` `=` `si.tickers_dow()`

`# Get data in the current column for each stock's valuation table`

`dow_stats` `=` `{}`

`for` `ticker` `in` `dow_list:`

`temp` `=` `si.get_stats_valuation(ticker)`

`temp` `=` `temp.iloc[:,:``2``]`

`temp.columns` `=` `[``"Attribute"``,` `"Recent"``]`

`dow_stats[ticker]` `=` `temp`

`# combine all the stats valuation tables into a single data frame`

`combined_stats` `=` `pd.concat(dow_stats)`

`combined_stats` `=` `combined_stats.reset_index()`

`del` `combined_stats[``"level_1"``]`

`# update column names`

`combined_stats.columns` `=` `[``"Ticker"``,` `"Attribute"``,` `"Recent"``]`

 |

### **Price-to-Earnings ratio for each Dow stock**

The P/E ratio for each stock can be obtained in a single line:

|

1

2

 |

`# get P/E ratio for each stock`

`combined_stats[combined_stats.Attribute.``str``.contains(``"Trailing P/E"``)`

 |

### **Getting the Price-to-Sales ratio for each Dow stock**

After the above code, we can get the Price / Sales ratios for each stock like below.

|

1

2

 |

`# get P/S ratio for each stock`

`combined_stats[combined_stats.Attribute.``str``.contains(``"Price/Sales"``)`

 |

### **How to get Price / Book ratio**

Similarly, we can get the Price-to-Book ratio for every stock in our list below.

|

1

2

 |

`# get Price-to-Book ratio for each stock`

`combined_stats[combined_stats.Attribute.``str``.contains(``"Price/Book"``)`

 |

### **How to get PEG ratio**

Next, let's get the PEG (Price / Earnings-to-Growth ratio).

|

1

2

 |

`# get PEG ratio for each stock`

`combined_stats[combined_stats.Attribute.``str``.contains(``"PEG"``)`

 |

### **How to get forward P/E ratios**

We can get forward P/E ratios like this:

|

1

2

 |

`# get forward P/E ratio for each stock`

`combined_stats[combined_stats.Attribute.``str``.contains(``"Forward P/E"``)]`

 |

**Getting additional stats from multiple stocks**
-------------------------------------------------

In addition to the "Valuation Measures" table on the stats tab, we can also scrape the remaining data points on the webpage using the *get_stats* method. Calling this method lets us extract metrics like Return on Equity (ROE), Return on Assets, profit margin, etc. [Click here to see the webpage for Apple](https://finance.yahoo.com/quote/AAPL/key-statistics?p=AAPL).

Similar to above, we can get this information for each stock in the Dow.

|

1

2

3

4

5

6

7

8

9

10

11

12

 |

`dow_extra_stats` `=` `{}`

`for` `ticker` `in` `tqdm(dow_list):`

`dow_extra_stats[ticker]` `=` `si.get_stats(ticker)`

`combined_extra_stats` `=` `pd.concat(dow_extra_stats)`

`combined_extra_stats` `=` `combined_extra_stats.reset_index()`

`del` `combined_extra_stats[``"level_1"``]`

`combined_extra_stats.columns` `=` `[``"ticker"``,` `"Attribute"``,` `"Value"``]`

 |

### **How to get Return on Equity (ROE)**

Using the result data frame, *combined_extra_stats*, let's get Return on Equity for each stock in our list.

|

1

 |

`combined_extra_stats[combined_extra_stats.Attribute.``str``.contains(``"Return on Equity"``)]`

 |

### **How to get Return on Assets**

A simple tweak gives us Return on Assets for each stock.

|

1

 |

`combined_extra_stats[combined_extra_stats.Attribute.``str``.contains(``"Return on Assets"``)]`

 |

### **How to get profit margin**

To get profit margin, we just need to adjust our filter like below.

|

1

 |

`combined_extra_stats[combined_extra_stats.Attribute.``str``.contains(``"Profit Margin"``)]`

 |

**How to get balance sheets**
-----------------------------

We can extract balance sheets from Yahoo Finance using the *get_balance_sheet* method. Using the data frame that is returned, we can get several attributes about the stock's financials, including total cash on hand, assets, liabilities, stockholders' equity, etc.

|

1

 |

`sheet` `=` `si.get_balance_sheet(``"aapl"``)`

 |

### **How to get total cash on hand**

We can see the "Total Cash" row in the balance sheet by filtering the "Breakdown" column by that name. This will give us the total cash value for the last several years.

|

1

 |

`sheet[sheet.Breakdown` `=``=` `"Total Cash"``]`

 |

### **How to get stockholders' equity**

Next, we can also get Total Stockholders' Equity.

|

1

 |

`sheet[sheet.Breakdown` `=``=` `"Total stockholders' equity"``]`

 |

### **How to get a company's total assets**

Now, let's get Total Assets.

|

1

 |

`sheet[sheet.Breakdown` `=``=` `"Total Assets"``]`

 |

**How to get balance sheets for many stocks at once**
-----------------------------------------------------

Like with the company statistics tables we pulled earlier, we can also download the balance sheet for all the stocks in the Dow (or again, a custom list of your choice).

|

1

2

3

4

 |

`balance_sheets` `=` `{}`

`for` `ticker` `in` `dow_list:`

`balance_sheets[ticker]` `=` `si.get_balance_sheet(ticker)`

 |

From here, we could then look at values from the balance sheets across multiple companies at once. For example, the code below combines the balance sheets from each stock in the Dow. Since each individual balance sheet may have different column headers (from different dates), we'll just get the most recent column of data from the balance sheet for each stock.

|

1

2

3

4

5

6

7

8

9

10

11

12

13

14

15

16

 |

`recent_sheets` `=` `{ticker : sheet.iloc[:,:``2``]` `for` `ticker,sheet` `in` `balance_sheets.items()}`

`for` `ticker` `in` `recent_sheets.keys():`

`recent_sheets[ticker].columns` `=` `[``"Breakdown"``,` `"Recent"``]`

`# combine all balance sheets together`

`combined_sheets` `=` `pd.concat(recent_sheets)`

`# reset index to pull in ticker`

`combined_sheets` `=` `combined_sheets.reset_index()`

`# get rid of numeric index field`

`del` `combined_sheets[``"level_1"``]`

`# update column names`

`combined_sheets.columns` `=` `[``"Ticker"``,` `"Breakdown"``,` `"Recent"``]`

 |

![python download balance sheets from yahoo finance](https://i2.wp.com/theautomatic.net/wp-content/uploads/2020/05/python-balance-sheets.png?w=640)

Now we have a data frame containing the balance sheet information for each stock in our list. For example, we can look at the Total Assets for each Dow stock like this:

|

1

 |

`combined_sheets[combined_sheets.Breakdown` `=``=` `"Total Assets"``]`

 |

**How to get income statements**
--------------------------------

Next, let's examine income statements. Income statements can be downloaded from Yahoo Finance using the *get_income_statement* method. See an example income statement [here](https://finance.yahoo.com/quote/AAPL/financials?p=AAPL).

|

1

 |

`si.get_income_statement(``"aapl"``)`

 |

Using the income statement, we can examine specific values, such as total revenue, gross profit, total expenses, etc.

### **Looking at a company's total revenue**

To get the total revenue, we just need to apply a filter like previously.

|

1

 |

`income[income.Breakdown` `=``=` `"Total Revenue"``]`

 |

### **Getting a company's gross profit**

Similarly, we can get the gross profit:

|

1

 |

`income[income.Breakdown` `=``=` `"Gross Profit"``]`

 |

**Getting the income statement from each Dow stock**
----------------------------------------------------

Next, let's pull the income statement for each Dow stock.

|

1

2

3

 |

`income_statements` `=` `{}`

`for` `ticker` `in` `dow_list:`

`income_statements[ticker]` `=` `si.get_income_statement(ticker)`

 |

Now, we can look at metrics in the income statement across multiple companies at once. First, we just need to combine the income statements together, similar to how we combined the balance sheets above.

|

1

2

3

4

5

6

7

8

9

10

11

12

 |

`recent_income_statements` `=` `{ticker : sheet.iloc[:,:``2``]` `for` `ticker,sheet` `in` `income_statements.items()}`

`for` `ticker` `in` `recent_income_statements.keys():`

`recent_income_statements[ticker].columns` `=` `[``"Breakdown"``,` `"Recent"``]`

`combined_income` `=` `pd.concat(recent_income_statements)`

`combined_income` `=` `combined_income.reset_index()`

`del` `combined_income[``"level_1"``]`

`combined_income.columns` `=` `[``"Ticker"``,` `"Breakdown"``,` `"Recent"``]`

 |

![python download income statements](https://i1.wp.com/theautomatic.net/wp-content/uploads/2020/05/python-get-income-statement.png?w=640)

Now that we have a combined view of the income statements across stocks, we can examine specific values in the income statements, such as Total Revenue, for example.

|

1

 |

`combined_income[combined_income.Breakdown` `=``=` `"Total Revenue"``]`

 |

**How to extract cash flow statements**
---------------------------------------

In this section, we'll extract cash flow statements. We can do that using the *get_cash_flow* method.

|

1

 |

`flow` `=` `si.get_cash_flow(``"aapl"``)`

 |

Here's the first few rows of the cash flow statement:

![python download cash flow statement from yahoo finance](https://i2.wp.com/theautomatic.net/wp-content/uploads/2020/05/python-download-cash-flow-statement.png?w=640)

Now let's get the cash flow statements of each Dow stock.

|

1

2

3

 |

`cash_flows` `=` `{}`

`for` `ticker` `in` `dow_list:`

`cash_flows[ticker]` `=` `si.get_cash_flow(ticker)`

 |

Again, we combine the datasets above, using similar code as before.

|

1

2

3

4

5

6

7

8

9

10

11

12

13

14

 |

`recent_cash_flows` `=` `{ticker : flow.iloc[:,:``2``]` `for` `ticker,flow` `in` `cash_flows.items()}`

`for` `ticker` `in` `recent_cash_flows.keys():`

`recent_cash_flows[ticker].columns` `=` `[``"Breakdown"``,` `"Recent"``]`

`combined_cash_flows` `=` `pd.concat(recent_cash_flows)`

`combined_cash_flows` `=` `combined_cash_flows.reset_index()`

`del` `combined_cash_flows[``"level_1"``]`

`combined_cash_flows.columns` `=` `[``"Ticker"``,` `"Breakdown"``,` `"Recent"``]`

 |

Now, we can examine information in the cash flow statements across all the stocks in our list.

### **Getting free cash flow across companies**

One example to look at in a cash flow statement is the free cash flow amount, which we can see across the companies in our list by using the filter below.

|

1

 |

`combined_cash_flows[combined_cash_flows.Breakdown` `=``=` `"Free Cash Flow"``]`

 |

Here's the first few rows of the result above.

![python get cash flow statement](https://i0.wp.com/theautomatic.net/wp-content/uploads/2020/05/python-get-cash-flow-statement-yahoo-finance.png?w=640)

### **Getting debt information**

Here's another example -- this time, we'll look at debt-related numbers across the cash flow statements.

|

1

 |

`combined_cash_flows[combined_cash_flows.Breakdown` `=``=` `"Issuance of Debt"``]`

 |

**Conclusion**
--------------

That's it for this post! Learn more about web scraping by [checking out this online course on Udemy](https://www.udemy.com/course/web-scraping-and-api-fundamentals-in-python/?referralCode=5F1A89DC81D5A8B7D589) that I co-created with 365 Data Science! You'll learn all about scraping data from different sources, downloading files programmatically, working with APIs, scraping JavaScript-rendered content, and more! [Check it out here!](https://www.udemy.com/course/web-scraping-and-api-fundamentals-in-python/?referralCode=5F1A89DC81D5A8B7D589)

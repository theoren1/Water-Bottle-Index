# Water-Bottle-Index
The website that showcases my water bottle index!
# Water-Bottle-Index

## Intro to my website and water bottle index

I'm excited to present the Water Bottle Index. I scavanged the internet for different products to base my index off of whilst in the end choosing to do it on watter bottles. I chose water bottles as it is a basic neccessity to every human on Earth! It was a fairly easy decision as everyone is familiar with this product and I can guarantee everyone in this classroom has purchased a plastic water bottle before. Whether stocking up for emergency, a quick gas station stop, or a quick visit to the schools vending machine, my product is apparent in everyones lives. In this assignment I dove into factors influencing the cost of this essential product and share insights gained from the data.

## My code and motivations behind it

Each step of my coding is documented inside of my google colab https://colab.research.google.com/drive/1P1OcDHRE3r-NIw2I4y-MvGD9--X4dfZM#scrollTo=peX9QGCrnSTK 
But here is some short explanation behind each of the steps I took

1. **Step 1:** - Here I grabbed my websites URL that I would be using and printed the results from the chart into my notebook.
import requests from bs4 import BeautifulSoup
import pandas as pd
url = 'https://www.numbeo.com/cost-of-living/country_price_rankings?itemId=7'
response = requests.get(url)
soup = BeautifulSoup(response.text, 'html.parser')
desired_table = soup.find_all('table')[1]
for row in desired_table.find_all('tr')[0:]:
    columns = row.find_all(['th', 'td'])
    row_data = [col.text.strip() for col in columns if col.text.strip()]
    data.append(row_data)
df = pd.DataFrame(data, columns=['Ranking', 'Country', 'Price'])
print(df.head(10))
3. **Step 2:** - With help from the example, used more imports that would be helpful later on.
from datascience import *
import numpy as np
import re
from re import search
import requests
from bs4 import BeautifulSoup

%matplotlib inline
import matplotlib.pyplot as plots
plots.style.use('fivethirtyeight')
import pandas as pd
def scrape_table(table):
    df = pd.read_html(str(table))
    df = pd.DataFrame(df[0])
    return Table.from_df(df)
5. **Step 3:** - Scraped data from the water bottle pricing based on the different countries.
water_bottle_pricing_url = 'https://www.numbeo.com/cost-of-living/country_price_rankings?itemId=7'
water_bottle_request = requests.get(water_bottle_pricing_url)
water_bottle_soup = BeautifulSoup(water_bottle_request.text, 'html.parser')
water_bottle_table = water_bottle_soup.find("table")
water_bottle_df = scrape_table(water_bottle_table)
print(water_bottle_df.head())
**Step 4:** - Here I cleaned some of the data by dropping an uneccessary column and moved onto converting the currency. 
water_bottle_pricing = water_bottle_pricing.drop(1) 
currencies = make_array()
gst_rates = make_array()
for each_country in water_bottle_pricing.column("Price"):
    currencies = np.append(currencies, each_country.split("VAT")[0].split(" ")[0])
    match = search("\d\d%", each_country)
    if match != None:
        gst_rates = np.append(gst_rates, match.group(0))
    else:
        gst_rates = np.append(gst_rates, '0%')
water_bottle_pricing = water_bottle_pricing.with_column("Currency", currencies)
water_bottle_pricing = water_bottle_pricing.with_column("GST Rate", gst_rates)
**Step 5:** - Then I took local prices with value added tax and applied a function to the price column.
def tidy_local_price(input):
    return re.findall("\d+", input.split("VAT")[0].split(" ")[1])[0] 
    water_bottle_pricing["Price"] = water_bottle_pricing.apply(tidy_local_price, "Price")
**Step 6:** - I set the currency_url to a URL that gave info on the different currencies. I then proceeded to give a get request and ended using the scrape table function.
currency_url = "https://currencyscoop.com/supported-currencies"
currency_request = request.get(currency_url)
currency_soup = BeautifulSoup(currency_request.text, 'html.parser')
currency_table = currency_soup.find("table")
currency_codes = scrape_table(currency_table)

**Step 7:** - I extracted the currency and put them into a new column. I then cleaned up the country names in my list. I then finished off by using the examples help to grab the currency of countries by their currency symbol.
codes = make_array()
for each_country in currency_codes.column("Currency"):
    codes = np.append(codes, each_country[-3:])
currency_codes = currency_codes.with_column("Code", codes)
def strip_code(country):
    return country[:-3]
    currency_codes["Currency"] = currency_codes.apply(strip_code, "Currency")
    def get_currency_code(country, symbol):
    if symbol == "AED" or symbol == "CZK":
        code = symbol
    else:
        country_match = process.extract(country, currency_codes.column("Countries Used"), scorer=fuzz.token_set_ratio)[0][0]
        index_match = np.where(currency_codes["Countries Used"] == country_match)[0][0]
        code = currency_codes.row(index_match)[2]
    return code
**Step 8:** - I created a new column that contains currency codes. In this set of code I also converted prices to USD and stored tax prices into a new column called USD Conversion.
water_bottle_pricing = water_bottle_pricing.with_column("Code", water_bottle_pricing.apply(get_currency_code, "Country", "Currency"))
water_bottle_pricing = water_bottle_pricing.exclude(36)
water_bottle_pricing
water_bottle_pricing = make_array()
for x in range(water_bottle_df.shape[0]):
    print("Converting " + str(water_bottle_df.iloc[x, 0]) + "...")
    with_tax = float(water_bottle_df.iloc[x, 2]) + float(water_bottle_df.iloc[x, 2]) * (float(water_bottle_df.iloc[x, 4].strip("%")) / 100)
    water_bottle_pricing = np.append(water_bottle_pricing, convert(water_bottle_df.iloc[x, 5], 'USD', with_tax))
  water_bottle_df["USD Conversion"] = water_bottle_pricing
**Step 9** - Then I finished off by calulating the differences with taxes and without.
water_bottle_differences = water_bottle_pricing["USD Conversion"]
water_bottle_pricing["Difference"] = water_bottle_differences

## Graphs

I've included a few graphs in my google colab


## Conclusion

In conclusion, there were a few take aways from this assignment and the water bottle index. It was interesting to see that over half of the countries pay less than $1.00 for a bottle of water. That being said, the most expensive bottle of water comes from Switzerland at $4.35, whilst the cheapest bottle of water comes from Libya at 0.14 cents. Some of the difficulties I had encountered during this assignment was with implementing pictures as well as showcasing my graphs. Regardless of any errors or difficulties in my assignment I hope you enjoyed and were still able to take something away from my assignment. Thank you!


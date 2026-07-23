# data-science-portfolio
End-to-end Data Science portfolio showcasing projects in Python, SQL, Machine Learning, Data Analysis, Power BI, Tableau, Web Scraping, and Predictive Analytics.
# Amazon Product Scraper

This Python script reads Amazon product URLs from `url.txt`, scrapes basic product details, and saves the results into `out.csv`.

## Files

- `url.txt`: input file with one Amazon product URL per line
- `out.csv`: output file with scraped product data

## Data Collected

The script collects:

- Product title
- Price
- Rating
- Review count
- Availability

## Requirements

Install the required Python packages:

```bash
pip install beautifulsoup4 requests lxml
```

## Create `url.txt`

Create this file:

```text
/Users/ankita/Downloads/url.txt
```

Add one Amazon product URL per line:

```text
https://www.amazon.com/dp/B08N5WRWNW
https://www.amazon.com/dp/ANOTHER_PRODUCT_ID
```

## Full Code

```python
from pathlib import Path
from bs4 import BeautifulSoup
import requests
import csv

HEADERS = {
    "User-Agent": (
        "Mozilla/5.0 (Windows NT 10.0; Win64; x64) "
        "AppleWebKit/537.36 (KHTML, like Gecko) "
        "Chrome/138.0.0.0 Safari/537.36"
    ),
    "Accept-Language": "en-US,en;q=0.9",
}

URL_FILE = Path("/Users/ankita/Downloads/url.txt")
OUT_FILE = Path("/Users/ankita/Downloads/out.csv")


# Create url.txt if it does not exist
if not URL_FILE.exists():
    URL_FILE.write_text(
        "https://www.amazon.com/dp/B08N5WRWNW\n",
        encoding="utf-8"
    )
    print(f"Created file: {URL_FILE}")
    print("Add your Amazon product URLs inside this file, one URL per line.")


def scrape_product(url):
    response = requests.get(url, headers=HEADERS)

    if response.status_code != 200:
        print(f"Failed to fetch page: {response.status_code}")
        return None

    soup = BeautifulSoup(response.content, "lxml")

    title_tag = soup.find("span", id="productTitle")
    title = title_tag.get_text(strip=True) if title_tag else "NA"

    price_tag = soup.select_one("span.a-price span.a-offscreen")
    price = price_tag.get_text(strip=True) if price_tag else "NA"

    rating_tag = soup.find("span", class_="a-icon-alt")
    rating = rating_tag.get_text(strip=True) if rating_tag else "NA"

    reviews_tag = soup.find("span", id="acrCustomerReviewText")
    reviews = reviews_tag.get_text(strip=True) if reviews_tag else "NA"

    availability_tag = soup.find("div", id="availability")
    availability = availability_tag.get_text(strip=True) if availability_tag else "NA"

    print("Product Title:", title)
    print("Price:", price)
    print("Rating:", rating)
    print("Reviews:", reviews)
    print("Availability:", availability)

    return [title, price, rating, reviews, availability]


with OUT_FILE.open("w", encoding="utf-8", newline="") as csv_file:
    writer = csv.writer(csv_file)
    writer.writerow(["Title", "Price", "Rating", "Reviews", "Availability"])

    with URL_FILE.open("r", encoding="utf-8") as file:
        for url in file:
            url = url.strip()

            if url:
                print("=" * 80)
                product_data = scrape_product(url)

                if product_data:
                    writer.writerow(product_data)

print(f"Done. Data saved to: {OUT_FILE}")
```

## Output

After running the script, the scraped data will be saved here:

```

## Note

Amazon pages may block automated requests or change their HTML structure. If the script prints `NA`, the page may not have loaded in the expected format.

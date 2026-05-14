import requests
from bs4 import BeautifulSoup
import pandas as pd
import re # Import regex for cleaning
import matplotlib.pyplot as plt
import seaborn as sns

# Define the URL of the website to scrape
url = 'https://books.toscrape.com/' # A website for book scraping examples

# Send a GET request to the URL
response = requests.get(url)

# Check if the request was successful (status code 200)
if response.status_code == 200:
    # Parse the HTML content of the page
    soup = BeautifulSoup(response.text, 'html.parser')

    # List to store extracted book data
    book_data = []

    # Extract all book details from the page
    books = soup.find_all('article', class_='product_pod')
    for book in books:
        title = book.h3.a['title']
        price = book.find('p', class_='price_color').text.strip()
        rating = book.find('p', class_='star-rating')['class'][1] # Get the second class which indicates the rating

        book_data.append({
            'Title': title,
            'Price': price,
            'Rating': rating
        })

    # Create a pandas DataFrame from the extracted data
    df_books = pd.DataFrame(book_data)

    # --- Data Cleaning for 'Fancier' Presentation ---
    # Clean 'Price' column: remove currency symbol and convert to float
    df_books['Price_Float'] = df_books['Price'].apply(lambda x: float(re.sub(r'[Â£]', '', x)))

    # Clean 'Rating' column: convert 'One', 'Two', etc. to numerical 1, 2, etc.
    rating_map = {'One': 1, 'Two': 2, 'Three': 3, 'Four': 4, 'Five': 5}
    df_books['Rating_Num'] = df_books['Rating'].map(rating_map)

    # Display the DataFrame with cleaned data
    print("\nExtracted and Cleaned Book Data (first 8 rows):")
    display(df_books.head(8)) # spécifie le numbre de ranges à montrir

    # --- Visualizing the Scraped Book Data ---
    sns.set_style("whitegrid")

    plt.figure(figsize=(12, 5))

    # Plot 1: Histogram of Book Prices
    plt.subplot(1, 2, 1) # 1 row, 2 columns, first plot
    sns.histplot(df_books['Price_Float'], bins=10, kde=True, color='skyblue')
    plt.title('Distribution of Book Prices')
    plt.xlabel('Price (£)')
    plt.ylabel('Number of Books')

    # Plot 2: Bar Chart of Book Ratings
    plt.subplot(1, 2, 2) # 1 row, 2 columns, second plot
    sns.countplot(x='Rating_Num', data=df_books, palette='viridis', hue='Rating_Num', legend=False)
    plt.title('Distribution of Book Ratings')
    plt.xlabel('Rating (Stars)')
    plt.ylabel('Number of Books')
    plt.xticks(ticks=[0, 1, 2, 3, 4], labels=['1 Star', '2 Stars', '3 Stars', '4 Stars', '5 Stars'])

    plt.tight_layout() # Adjust layout to prevent overlapping titles/labels
    plt.show()

else:
    print(f"Failed to retrieve the webpage. Status code: {response.status_code}")

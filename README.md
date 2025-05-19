## code py
```bash
import requests
import csv
from io import StringIO

def decode_secret_message_from_google_doc(url):
    # Convert edit URL to export CSV URL
    if "/edit" in url:
        url = url.replace("/edit", "/export?format=csv")

    print(f"🔗 Fetching data from: {url}")

    try:
        response = requests.get(url)
        response.raise_for_status()
    except Exception as e:
        print(f"Failed to fetch data: {e}")
        return

    data = response.content.decode('utf-8')
    reader = csv.DictReader(StringIO(data))

    grid_data = []
    max_x = 0
    max_y = 0

    # Read data, store coordinates and characters, find max grid size
    for row in reader:
        try:
            x = int(row['x-coordinate'])
            y = int(row['y-coordinate'])
            char = row['Character'].strip()
            grid_data.append((x, y, char))
            if x > max_x:
                max_x = x
            if y > max_y:
                max_y = y
        except (KeyError, ValueError) as e:
            # Skip invalid rows
            continue

    # Create empty grid filled with spaces
    grid = [[' ' for _ in range(max_x + 1)] for _ in range(max_y + 1)]

    # Fill the grid with characters at the correct positions
    for x, y, char in grid_data:
        grid[y][x] = char

    # Print the grid to the console
    print(" Resulting grid:\n")
    for row in grid:
        print(''.join(row))


# Example usage:
# Replace the URL below with your Google Sheets URL
url_google_sheet = "https://docs.google.com/spreadsheets/d/1mbR1a1J-LA14A-k_Z_KbIS_VCPWcpeM8iJ7xqC4XBHs/export?format=csv"
decode_secret_message_from_google_doc(url_google_sheet)
```

## Code Purpose
This code aims to retrieve data from Google Sheets exported as CSV, where the data contains characters and their coordinate positions. It will then form a 2D grid (like a board) and display the secret message by arranging the characters based on the x and y coordinates.

## Step-by-Step Explanation
### 1. Import Module
```bash
import requests
import csv
from io import StringIO
```

- requests: to retrieve data from a URL.
- csv: to read the contents of a CSV file.
- StringIO: makes strings (CSV from the web) readable like files.

### 2. Main Functions
```bash
def decode_secret_message_from_google_doc(url):
```
- This function accepts a Google Sheets URL as input.

### 3. URL conversion
```bash
if "/edit" in url:
    url = url.replace("/edit", "/export?format=csv")
```

If the URL is a Google Sheets edit link (e.g. .../edit), it will be converted to a CSV export URL (e.g. .../export?format=csv).

### 4. Retrieve CSV Data
```bash
response = requests.get(url)
response.raise_for_status()
```
Performs an HTTP GET request to the URL and ensures that no errors occur.

### 5. Decode and Read CSV
```bash
data = response.content.decode('utf-8')
reader = csv.DictReader(StringIO(data))
CSV from Google is decoded into UTF-8 string.
```
It is read with csv.DictReader, which maps the rows into a dictionary: {'x-coordinate': ..., 'y-coordinate': ..., 'Character': ...}.

### 6.  Read Coordinates and Characters
```bash
grid_data = []
max_x = 0
max_y = 0

for row in reader:
    x = int(row['x-coordinate'])
    y = int(row['y-coordinate'])
    char = row['Character'].strip()
```
- Stores the characters and positions (x, y) in grid_data.
- Stores the maximum x and y values to determine the grid size.


### 7. Creating an Empty Grid
```bash
grid = [[' ' for _ in range(max_x + 1)] for _ in range(max_y + 1)]
```
Create a 2D grid containing spaces (' '), the grid size is adjusted based on the largest position of x and y.

### 8.  Filling the Grid
```bash 
for x, y, char in grid_data:
    grid[y][x] = char
```
- Places the character in the appropriate position.
- Note: row index is y and column is x according to matrix format: grid[row][column].


### 9. Display Grid
```bash
for row in grid:
    print(''.join(row))
```
Each row of the grid is printed as a whole string, so it looks like a hidden message or ASCII image.

## Overall.
This code is an elegant and practical solution to encode and reveal hidden messages in the form of text-based 2D grids from Google Sheets CSV files. Suitable for educational projects, visual puzzles, or simple steganography.

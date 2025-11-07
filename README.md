# AutoTracker

A Python-based automated stock data retrieval and portfolio tracking system that downloads historical stock prices from the Refinitiv Knowledge Direct (RKD) API and calculates portfolio returns based on configurable stock weights.

## Overview

AutoTracker consists of two main components:

1. **RKDRetriever**: A Python class that interfaces with the Refinitiv Knowledge Direct API to retrieve both interday and intraday stock price data
2. **Portfolio Data Downloader**: A Jupyter notebook that automates the download of stock prices for multiple securities and consolidates them into a structured dataset for portfolio analysis

## Features

- **Automated Data Retrieval**: Download historical stock prices (OHLC, volume, VWAP) from the Refinitiv API
- **Smart Caching**: Automatically caches downloaded data locally to minimize redundant API calls
- **Batch Processing**: Download data for multiple stocks simultaneously based on index compositions (CAC40, DAX40, SMI20)
- **Portfolio Management**: Track portfolio weights and calculate returns using Excel-based configuration files
- **Flexible Time Ranges**: Support for both daily (interday) and minute-level (intraday) data retrieval
- **Data Export**: Export consolidated price data to CSV and Excel formats

## Project Structure

```
AutoTracker/
├── RKDRetriever.py                          # Core API client class
├── PortDataDownloader.ipynb                 # Jupyter notebook for batch downloads
├── CAC40.csv                                # CAC40 index stock list with RICs and weights
├── DAX40.csv                                # DAX40 index stock list
├── SMI20.csv                                # SMI20 index stock list
├── Market_Neutral_Paper_Trading.xlsx        # Portfolio weights and paper trading data
├── sentiment_paper_trading.xlsx             # Sentiment-based portfolio configuration
├── data/                                    # Local cache directory for downloaded data
└── README.md                                # This file
```

## Prerequisites

- Python 3.7+
- Active Refinitiv Knowledge Direct (RKD) API credentials
- Internet connection for API access

## Installation

1. Clone this repository:
```bash
git clone <repository-url>
cd AutoTracker
```

2. Install required dependencies:
```bash
pip install -r requirements.txt
```

3. Configure API credentials in `RKDRetriever.py`:
```python
self.username = 'your_username@domain.com'
self.password = 'your_password'
self.appid = 'your_application_id'
```

## Usage

### Using RKDRetriever Class

The `RKDRetriever` class provides methods to retrieve stock data programmatically:

```python
from RKDRetriever import RKDRetriever

# Initialize retriever
retr = RKDRetriever()
retr.CreateAuthorization()

# Retrieve daily (interday) data
ricName = '/.SSMI'  # Swiss Market Index
startTime = '2022-07-01T00:00:00'
endTime = '2024-12-31T23:59:59'
data, is_cached = retr.smartRetrieveInterday(ricName, startTime, endTime)

# Retrieve intraday (minute-level) data
intraday_data, is_cached = retr.smartRetrieveIntraday(ricName, '2024-08-26')

# Save to CSV
data.to_csv(f'{ricName}.csv')
```

### Using the Notebook

1. Open the Jupyter notebook:
```bash
jupyter notebook PortDataDownloader.ipynb
```

2. Configure the parameters in the notebook:
   - `companies_path`: Path to CSV file containing stock RIC codes (e.g., "CAC40.csv")
   - `start_date`: Start date for data retrieval (YYYY-MM-DD)
   - `end_date`: End date for data retrieval (YYYY-MM-DD)

3. Run all cells to download and consolidate stock prices

The notebook will:
- Read the list of stocks from the specified CSV file
- Download closing prices for each stock in the date range
- Consolidate all prices into a single DataFrame (RICs as rows, dates as columns)
- Save the results to `data/prices_{start_date}_{end_date}.csv`

## API Methods

### RKDRetriever Methods

#### Authentication
- `CreateAuthorization()`: Authenticate with the RKD API and obtain an access token

#### Data Retrieval
- `RetrieveInterday(ricName, startTime, endTime, interval='DAILY', fields=[...])`: Retrieve daily historical data
- `RetrieveIntraday(ricName, startTime, endTime, interval='MINUTE', fields=[...])`: Retrieve intraday data
- `smartRetrieveInterday(ricName, startTime, endTime, reuse=True, ...)`: Retrieve with intelligent caching
- `smartRetrieveIntraday(ricName, date, reuse=True, ...)`: Retrieve intraday data with caching

#### Available Fields
- OPEN, HIGH, LOW, CLOSE: OHLC prices
- VOLUME: Trading volume
- BID, ASK: Bid and ask prices
- VWAP: Volume-weighted average price

## Portfolio Configuration Files

The CSV files (e.g., `CAC40.csv`) contain portfolio information:

| Column | Description |
|--------|-------------|
| Company Name | Full company name |
| RIC | Refinitiv Instrument Code (ticker symbol) |
| Weight in Index | Official index weight percentage |
| Portfolio Weight | Your portfolio allocation percentage |
| Asset allocation | Dollar amount allocated |
| Close price | Latest closing price |
| #Shares | Number of shares held |

The Excel files (`Market_Neutral_Paper_Trading.xlsx`, `sentiment_paper_trading.xlsx`) store portfolio weights and are used to calculate portfolio returns based on downloaded price data.

## Data Caching

The `smartRetrieve` methods implement intelligent caching:

- **Interday data**: Cached in `data/{ric_name}.xlsx` with date range stored in `data/{ric_name}.txt`
- **Intraday data**: Cached in `data/{ric_name}/{ric_name}_{date}.xlsx`

If cached data covers the requested time range, it will be loaded from disk instead of making an API call.

## Output Format

Price data is exported with the following structure:

**CSV Output** (`data/prices_{start_date}_{end_date}.csv`):
```
RIC         | 2025-09-27 | 2025-09-28 | ...
------------|------------|------------|----
LVMH.PA     | 650.5      | 652.3      | ...
SCHN.PA     | 212.8      | 213.5      | ...
...
```

## Security Note

**IMPORTANT**: The `RKDRetriever.py` file currently contains hardcoded API credentials. For production use:
1. Remove credentials from the source code
2. Use environment variables or a secure configuration file
3. Add `.env` to `.gitignore` to prevent credential exposure

Example using environment variables:
```python
import os
self.username = os.getenv('RKD_USERNAME')
self.password = os.getenv('RKD_PASSWORD')
self.appid = os.getenv('RKD_APPID')
```

## Limitations

- Requires active Refinitiv RKD API subscription
- API rate limits may apply depending on your subscription tier
- Historical data availability depends on your data package
- Timezone handling: All timestamps are converted to local timezone in the output

## Troubleshooting

### Authentication Errors
- Verify your username, password, and application ID are correct
- Ensure your RKD API subscription is active
- Check network connectivity

### Missing Data
- Some stocks may not have data for all dates (weekends, holidays, delisted stocks)
- The notebook handles missing data by inserting `pd.NA` values

### File Not Found Errors
- Ensure the `data/` directory exists (it will be created automatically)
- Verify CSV file paths are correct

## Contributing

Contributions are welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## License

[Specify your license here]

## Contact

For questions or support, please [specify contact information].

## Acknowledgments

- Built using the Refinitiv Knowledge Direct (RKD) API
- Portfolio tracking inspired by quantitative trading strategies

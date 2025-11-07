# AutoTracker

A stock data retrieval and portfolio tracking system that downloads historical stock prices from the RKD API and calculates portfolio returns based on configurable stock weights.

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

### Using the Notebook

1. Open the Jupyter notebook:
```bash
jupyter notebook PortDataDownloader.ipynb
```

2. Configure the parameters in the notebook:
   - `companies_path`: Path to CSV file containing stock RIC codes (e.g., "CAC40.csv")
   - `start_date`: Start date for data retrieval (YYYY-MM-DD)
   - `end_date`: End date for data retrieval (YYYY-MM-DD)

3. Run all cells to download stock prices

The notebook will:
- Save the results to `data/prices_{start_date}_{end_date}.csv`

4. Copy and paste stock prices into coresponding excel

5. Calculate P&L and updates charts









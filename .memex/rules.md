# Application Setup
- Update the following files to describe current application
- `pyproject.toml` - `name` and `description`
- `app.py` - `page_title` and `title`
- `README.md`

# How to run Data App
Use `start.sh`
Start script runs `uv sync` and `uv run`

# Data Sources

## Google Sheets

### CSV Export URL Format
Google Sheets can be accessed as CSV using the export URL format:
```
https://docs.google.com/spreadsheets/d/{SHEET_ID}/export?format=csv&gid={GID}
```

Where:
- `{SHEET_ID}`: The unique identifier from the sheet URL
- `{GID}`: The specific tab/sheet ID (default is 0)

### SSL Certificate Handling
When loading Google Sheets data with `pandas.read_csv()`, you may encounter SSL certificate verification errors on macOS. Use this pattern to handle it:

```python
import ssl
import urllib.request
import pandas as pd

@st.cache_data(ttl=300)  # Cache for 5 minutes
def load_data():
    """Load data from Google Sheets"""
    try:
        # Create SSL context that doesn't verify certificates for public Google Sheets
        ssl_context = ssl.create_default_context()
        ssl_context.check_hostname = False
        ssl_context.verify_mode = ssl.CERT_NONE
        
        # Fetch data with custom SSL context
        with urllib.request.urlopen(SHEET_URL, context=ssl_context) as response:
            df = pd.read_csv(response)
        
        return df
    except Exception as e:
        st.error(f"Error loading data: {e}")
        return None
```

### Why SSL CERT_NONE is Safe Here
- Reading from public Google Sheets (no sensitive data transmission)
- Google Sheets URLs are read-only CSV exports
- Alternative to installing/configuring system certificates
- Only use for public data sources

### Production Considerations
For production environments with private sheets or sensitive data:
1. Use Google Sheets API with proper OAuth authentication
2. Install proper SSL certificates on the deployment environment
3. Consider caching data locally to reduce API calls
4. Use environment variables for sheet IDs and credentials


Your app will be available at: `https://[username]--[app-name]-run.modal.run`

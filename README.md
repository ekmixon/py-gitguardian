<img src="./doc/logo.svg">

# GitGuardian API Client

API client library for the GitGuardian API.

You can check API details [here](https://api.gitguardian.com/doc)
with all the response codes and expected structures on each method.

## Requirements

Python 3.5+

## Getting started

You can obtain API keys for API usage on your [dashboard](https://dashboard.gitguardian.com/api).

**pip**

```bash
pip3 install --upgrade pygitguardian
```

**pipenv**

```bash
pipenv install pygitguardian
```

**poetry**

```bash
poetry add pygitguardian
```

## Examples

Check [examples/](examples/) for full examples on how to use py-gitguardian.

### Scanning text content

```py
API_KEY = os.getenv("GG_API_KEY")
DOCUMENT = """
    import urllib.request
    url = 'http://jen_barber:correcthorsebatterystaple@cake.gitguardian.com/isreal.json'
    response = urllib.request.urlopen(url)
    consume(response.read())"
"""

client = GGClient(api_key=API_KEY)

# Check the health of the API and the API key used.
health_obj, status = client.health_check()

if status != codes[r"\o/"]:  # this is 200 but cooler
    print("Invalid API Key")

try:
    scan_result = client.content_scan(DOCUMENT)
except Exception as exc:
    # Handle exceptions such as schema validation
    traceback.print_exc(2, file=sys.stderr)
    print(str(exc))

print("Scan results:", scan_result.has_secrets, "-", scan_result.policy_break_count)
```

### Scanning multiple files

```py
API_KEY = os.getenv("GG_API_KEY")
client = GGClient(api_key=API_KEY)
# Create a list of dictionaries for scanning
to_scan = []
for name in glob.glob("**/*"):
    with open(name) as fn:
        to_scan.append({"document": fn.read(), "filename": os.path.basename(name)})

scan, status_code = client.multi_content_scan(to_scan)
```

### Transform results to dict or JSON

Any model in `py-gitguardian` can be turned to a JSON string or a dictionary using
the `to_dict` and `to_json` methods.

```py
from pygitguardian.models import Detail

detail = Detail("Invalid API Key.")
print(detail.to_dict())
print(detail.to_json())
```

### Dependencies

Py-gitguardian depends on these excellent libraries:

- `requests` - HTTP client
- `marshmallow` - Request (de)serialization and input validation

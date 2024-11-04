
Last Updated on 2024-11-04

# Library Website Link Checking Workflow

A workflow for crawling, collecting, and validating links on University
of Southampton Library website. This workflow consists of three main
components working together through GitHub Actions automation.

It is designed to balance speed and breadth against exhaustive checking,
so do not expect to capture every possible failure. The workflow should
run in around an hour.

This workflow can be adapted for any website.

## Overview

The workflow includes:

1.  Link Crawler (`get-links.py`)
2.  URL Validator (`check-urls.py`)
3.  GitHub Actions Workflow (`library-link-crawler.yaml`)

## Features

- Automated weekly link checking
- Recursive website crawling
- Comprehensive link validation
- Results reported as `csv` tables
- Email notifications
- GitHub Actions integration
- Memory-efficient batch processing

## Components

### 1. Link Crawler (`get-links.py`)

A Playwright-based crawler that collects links from web pages.

#### Key Features

- Asynchronous web crawling
- Recursive link collection
- Configurable crawl depth
- Link filtering
- CSV output format

#### Usage

``` bash
python get-links.py --recurse --max-depth 3
```

#### Configuration

- `--recurse`: Enable recursive crawling
- `--max-depth`: Set maximum crawl depth
- `--format`: Output format (CSV)

### 2. URL Validator (`check-urls.py`)

An asynchronous link checker that validates collected URLs.

#### Key Features

- Concurrent URL checking
- Retry mechanism
- Detailed status reporting
- Separate 404 reporting
- Memory-efficient batch processing

#### Configuration

``` python
checker = URLChecker(
    max_retries=3,
    timeout_seconds=15,
    max_concurrent=50,
    retry_delay=1
)
```

#### Output

- Main report: All URLs and their status
- 404 report: Failed URLs only

### 3. GitHub Actions Workflow (`library-link-crawler.yml`)

Automates the entire link management process.

#### Features

- Weekly scheduled runs
- Manual trigger option
- Dependency caching
- Email notifications
- Artifact storage
- Error handling

#### Schedule

- Runs every Wednesday at 01:00 UTC
- Manual workflow dispatch option

#### Manual Execution

To run the workflow manually:

1\. Go to the “Actions” tab

2\. Select “Library Python Link Crawler”

3\. Click “Run workflow”

#### Maintenance

The workflow includes a keepalive mechanism to prevent GitHub from
disabling it due to inactivity.

#### Security

- Uses GitHub token for authentication
- Implements explicit permissions
- Secures sensitive information in GitHub Secrets

## Setup and Installation

### Prerequisites

- Python 3.7+

- Required packages:

  ``` bash
  pip install playwright asyncio aiohttp tenacity
  ```

### Installation Steps

1.  Clone the repository

2.  Install dependencies:

    ``` bash
    pip install -r requirements.txt
    playwright install
    ```

3.  Configure GitHub Actions:

    - Set up repository secrets
    - Configure email settings
    - Adjust workflow schedule if needed

## Usage

### Manual Execution

1.  Run link crawler:

    ``` bash
       python get-links.py --recurse --max-depth 3
    ```

### Command Line Arguments

- `--recurse`: Enable recursive crawling
- `--max-depth`: Set maximum crawl depth (default: 5)
- `--format`: Output format (currently only supports CSV)

### Environment Variables

- `BASE_URL`: The starting URL for crawling (default:
  “https://library.soton.ac.uk”)

## Output

The script generates a CSV file in the `reports` directory with the
following format: - Filename: `get-links-YYYY-MM-DD.csv` - Columns: URL,
Parent URL

## Features

### Link Collection

- Collects all `<a>` tag links from web pages
- Converts relative URLs to absolute URLs
- Ignores:
  - mailto: links
  - javascript: links
  - tel: links
  - Internal page anchors (#)
  - Download content links (e.g., “ld.php?content_id=”)

### Crawling

- Asynchronous operation for better performance
- Batch processing to manage memory usage
- Domain restriction for recursive crawling
- Configurable maximum depth
- Link limit to prevent excessive crawling

## Integration with GitHub Actions

The script is designed to work with GitHub Actions: - Sets environment
variables for workflow use - Creates standardized output files - Handles
GitHub Actions environment file

## Error Handling

- Graceful handling of navigation errors
- Continues crawling even if individual pages fail
- Error logging for debugging

## Limitations

- Currently only supports CSV output format
- Maximum link limit of 10,000 to prevent memory issues
- Single domain crawling only

2.  Run URL checker:

    The script reads URLs from a CSV file and checks their validity:

``` bash
python check-urls.py
```

### Configuration

The URLChecker class can be configured with the following parameters:

``` python
checker = URLChecker(
    max_retries=3,          # Maximum number of retry attempts
    timeout_seconds=15,     # Request timeout in seconds
    max_concurrent=50,      # Maximum concurrent connections
    retry_delay=1          # Initial retry delay in seconds
)
```

### Input Format

Expects a CSV file with the following columns: - URL - Parent URL
(optional)

### Output Files

Generates two CSV reports: 1. Main report
(`check-links-report-YYYY-MM-DD.csv`) - All URLs and their status 2. 404
report (`check-links-404-report-YYYY-MM-DD.csv`) - Only URLs returning
404 status

### Report Columns

Both reports include: - URL - Status Code - Content-Type - Parent URL -
Input Line Number

## Features in Detail

### Request Handling

- Browser-like headers
- SSL verification disabled
- Redirect following
- Configurable timeouts
- Rate limiting

### Error Handling

- Retry mechanism with exponential backoff
- Timeout handling
- Connection error handling
- Unexpected error catching

### Performance

- Batch processing
- Concurrent requests
- Memory management
- Progress tracking

### Reporting

- Detailed status logging
- Separate 404 reporting
- GitHub Actions integration
- Environment variable setting

## Integration with GitHub Actions

The script supports GitHub Actions workflow integration: - Sets
environment variables - Creates standardized output files - Provides
status information - Supports CI/CD pipeline integration

## Error Codes

The script handles various status codes and errors: - HTTP status codes
(200, 404, etc.) - Timeout errors - Connection errors - SSL errors -
General exceptions

3.  Or trigger GitHub Actions workflow manually

### Automated Execution

The workflow runs automatically according to the workflow schedule.

## Configuration

### Environment Variables

- `BASE_URL`: Target website URL
- `LINKS_FILE`: Path to links CSV
- `REPORT_FILE`: Path to main report
- `REPORT_404_FILE`: Path to 404 report

### Email Settings

Configure in workflow file:

``` yaml
username: library.linkchecker@gmail.com
to: webmaster@library.example.edu
```

## Reports

### Types of Reports

1.  Links Collection Report
    - All discovered URLs
    - Parent page information
2.  URL Validation Report
    - Status codes
    - Content types
    - Error details
3.  404 Error Report
    - Failed URLs
    - Parent pages
    - Line numbers

### Report Location

- Reports are stored in the `reports` directory
- Uploaded as GitHub Actions artifacts
- Sent via email to specified recipients

## Best Practices

1.  Regular Monitoring
    - Review reports weekly
    - Monitor system performance
    - Update configuration as needed
2.  Performance Optimization
    - Adjust concurrent connections
    - Set appropriate timeouts
    - Use batch processing
3.  Maintenance
    - Keep dependencies updated
    - Monitor GitHub Actions usage
    - Review and update filters

## Troubleshooting

Common issues and solutions: 1. Rate Limiting - Adjust max_concurrent
setting - Implement longer delays

2.  Memory Issues
    - Reduce batch size
    - Adjust crawl depth
3.  Timeout Errors
    - Increase timeout settings
    - Check network conditions

## Contributing

1.  Fork the repository
2.  Create a feature branch
3.  Commit your changes
4.  Push to the branch
5.  Create a Pull Request

## License

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)

## Support

For issues or questions, please create a GitHub issue and/or check the
existing documentation.

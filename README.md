# Library Website Link Checker

Last Updated on 2024-11-19

This repository contains the python code and yaml workflow for
collecting and checking the status of links on the
<https://library.soton.ac.uk> website.

## Link Collection

For link collection, the script crawls links found on the home page to
other pages, for up to 3 levels of recursion. Here’s an **imaginary**
example:

1.  Initial crawl: `library.soton.ac.uk`
    - Finds links to `library.soton.ac.uk/about` and
      `library.soton.ac.uk/search`
2.  Next level of recursion:
    - Crawls `library.soton.ac.uk/about` and finds links to
      `library.soton.ac.uk/about/team` and
      `library.soton.ac.uk/about/contact`
    - Crawls `library.soton.ac.uk/search` and finds links to
      `library.soton.ac.uk/search/databases` and
      `library.soton.ac.uk/search/ejournals`
3.  Third level of recursion:
    - Crawls `library.soton.ac.uk/about/team` and finds links to
      individual staff profiles
    - Crawls `library.soton.ac.uk/about/contact` and finds links to
      contact forms
    - Crawls `library.soton.ac.uk/search/databases` and finds links to
      specific database pages
    - Crawls `library.soton.ac.uk/search/ejournals` and finds links to
      specific e-journal pages

We could recurse further, but this depth seemed appropriate to capture
the majority of links on the website without overloading the GitHub
Actions workflow.

The collection also ignores links that point to downloadable files or
emails.

The link crawler and collection code is in `get-links.py` and this is
run as part of the GitHub Actions workflow. It also generate a CSV file
with the collected links which is used by the link checker script and
also written to the `reports` directory.

## Link Checking

The link checker validates the status of links found by `get-links.py`.
This reads the CSV file and then checks the status of each link,
reporting the status of all links and separately reporting any 404
broken links as CSV files.

The link checker code is in `check-urls.py` and provides User-Agent
headers identifying the source of the request as the GitHub Actions
workflow to enable server log filtering.

## Github Actions Workflow

The GitHub Actions workflow is in `library-link-crawler.yaml` that runs
as a cron job monthly on the 13th day at 0100 UTC, or can be triggered
manually from Github.

The Action workflow runs as an Ubuntu Linux container that has
Playwright and Python and dependencies installed and cached.

The workflow runs the link crawler, link checker, and the publishes the
results to Github Pages and sends an email notification as to whether
404 broken links were found or not, along with a link to the results.

The workflow is also commits the CSV files of links to the repository in
the `reports` directory and has a job to keep the workflow alive even if
no commits are made to the repository.

The workflow uses the following secrets in the GitHub repository:

- `LIB_URL`: Base URL for the link collection (this isn’t necessary as a
  secret, but makes updating easier in the future)
- `GMAIL_USERNAME`: SMTP email username for email notification
- `GMAIL_PASSWORD`: App Password for email notification. See [Create App
  passwords](https://knowledge.workspace.google.com/kb/how-to-create-app-passwords-000009237)
- `LIB_EMAIL_RECIPIENT`: Primary email recipient
- `EMAIL_RECIPIENT`: CC email recipient (if desired)
- `EMAIL_SENDER`: Gmail address used for email notification

## Github Pages Report

The Github Pages report is built from the `index.html` file. It displays
two tables: **All links** and **Broken links (404)**.

- All links is the table that includes both working and broken links.
- Broken links (404) is the table that includes only broken links.

The table displays these columns:

- URL is the URL of the checked link.
- Status Code is the HTTP status code of the checked link.
- Status Emoji is a visual indicator of the status code: 💙 for 200 OK,
  💔 for 404 broken, 😕 for any other status.
- Page Found On is the webpage on which the link occurs.

The table can be filtered using the search box and either the filtered
or unfiltered data can be downloaded as a CSV file.

The page also contains contact information and a link back to this
repository.

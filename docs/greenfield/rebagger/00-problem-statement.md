# Problem Statement (Original)

**Captured:** 2026-04-22

# Problem
## Scenario
A Lego set builder acquires a previously constructed set, but is unsure of the following:
- Are all the pieces present to build the set per the instructions?
- Are the pieces in the correct numbered bags?

The builder may be given the set in any of these ways:
- Everything in a single bag
- Parts in numbered bags

## Goals this app will help the builder achieve
- Get the correct pieces in the correct numbered bags per the instructions.
- Identify missing (or extra) parts found during the inventory process.

## Other product considerations
The app should support multiple Lego sets.

## Technical considerations
The correct (i.e., factory specified) inventory will be provided externally. For simplicity, the inventory will be initially defined as a CSV file the app will ingest. Eventually, the app could support storing the inventory in a database of some kind, such that the user doesn't have to import a set's correct inventory each time the app is used. There are websites that host inventory pages that could be scraped to obtain the correct inventory if the user doesn't have a CSV file (or an existing inventory in the database).

# Minimum Viable Product Workflow
1. The user runs the application locally.
2. The user is prompted to specify a local CSV file containing a Lego set's inventory. (For simplicity, the set will only have one bag.)
3. The user will be shown a page that displays the imported Lego piece information.
4. The user will interact with the page to set the quantity of pieces found in their Lego set's bag.
5. The user will have the app generate a report of pieces that don't match the expected quantities.

# Global Sales Project

A cloud-based data pipeline that collects daily sales data from store managers across the UK, France, and Germany, processes it on Google Cloud Platform, and delivers real-time insights to executive leadership through Looker dashboards.

## Overview

Store managers in the UK, France, and Germany upload their daily sales data through a web application. The data flows through Google Cloud Platform, where it is stored, split by country, processed, and transformed, then visualized in Looker for consumption by the CEO and other stakeholders.

## Architecture

```
Store Managers (Web) → Flask Web App → Cloud Storage → Cloud Function → Looker → CEO
```

1. **Store Managers** in the UK, France, and Germany access the web app from their browsers and upload daily sales data (e.g., CSV/Excel exports from point-of-sale systems).
2. **Web App (Python Flask)** receives the uploads, validates the input, and forwards the files to Google Cloud Platform.
3. **Cloud Storage** acts as the landing zone, storing the raw uploaded files in designated buckets.
4. **Country Views** — once the raw data lands in Cloud Storage, it is split into 3 separate views based on country (UK, France, Germany), keeping each country's data isolated for processing and reporting.
5. **Cloud Function** is triggered automatically when a new file lands in Cloud Storage. It parses, cleans, and transforms the raw data for each country view into a structured format suitable for reporting.
6. **Looker** connects to the processed data and builds interactive dashboards and reports, both per-country and consolidated globally.
7. **CEO / Leadership** views consolidated sales performance across all three countries through the Looker dashboard.

## Software Development Life Cycle (SDLC)

### 1. Requirements & Planning
- Gathered requirements from store managers and leadership on what sales data needed to be captured (daily revenue, units sold, store location, date, etc.).
- Defined the reporting needs of the CEO: a single, real-time view of sales performance across all stores.
- Selected the tech stack: Python Flask for the web app, Google Cloud Platform for storage and processing, and Looker for visualization.

### 2. Design
- Designed the system architecture (see diagram above) to keep data collection, storage, processing, and visualization as separate, decoupled components.
- Designed the web app's upload form and validation rules (file type, required columns, size limits).
- Designed the Cloud Storage bucket structure (e.g., organized by store ID and upload date) to keep incoming files organized and traceable.
- Designed the data transformation logic that the Cloud Function would need to apply (cleaning, standardizing formats, aggregating fields).

### 3. Development
- **Web App (Python Flask):**
  - Built a lightweight Flask application to give store managers a simple interface for uploading their sales data files.
  - Implemented server-side validation to catch malformed files before they reach Cloud Storage.
  - Implemented secure upload handling (authentication, file-type restrictions) so only authorized store managers can submit data.
- **Cloud Storage Integration:**
  - Configured the Flask app to push validated files directly into a Google Cloud Storage bucket using the GCP client libraries.
  - Once files land in Cloud Storage, the data is split into 3 separate views based on country — UK, France, and Germany — so each region's data can be processed and reported on independently.
- **Cloud Function:**
  - Wrote an event-driven Cloud Function that triggers automatically whenever a new file is added to the Cloud Storage bucket.
  - The function parses the incoming file, cleans and standardizes the data, and writes the processed output per country view to a location/dataset that Looker can query.
- **Looker Configuration:**
  - Connected Looker to the processed data source.
  - Built LookML models and explores to define how the sales data should be aggregated and displayed.
  - Designed dashboards summarizing sales trends, store-by-store performance, and other key metrics for leadership.

### 4. Testing
- Unit tested the Flask upload endpoints (valid files, invalid files, oversized files, missing fields).
- Tested the Cloud Function trigger with sample files to confirm it correctly parses and transforms data under different edge cases (empty files, duplicate entries, incorrect formats).
- Validated that Looker dashboards reflected the transformed data accurately by cross-checking against raw source files.
- Conducted end-to-end testing: uploading a file as a store manager and confirming it appeared correctly on the CEO's Looker dashboard within an acceptable time window.

### 5. Deployment
- Deployed the Flask web app to a hosting environment accessible to all store managers (e.g., Google App Engine, Cloud Run, or a similar GCP-compatible service).
- Deployed the Cloud Function to GCP with the appropriate Cloud Storage trigger configured.
- Published the finalized Looker dashboards and granted view access to the CEO and relevant stakeholders.

### 6. Maintenance & Monitoring
- Monitored Cloud Function execution logs to catch and resolve processing errors early.
- Monitored Cloud Storage for unexpected file volumes or malformed uploads.
- Iterated on the Flask app and Looker dashboards based on ongoing feedback from store managers and leadership.

## Tech Stack

| Layer | Technology |
|---|---|
| Web App | Python, Flask |
| File Storage | Google Cloud Storage |
| Data Processing | Google Cloud Functions |
| Visualization / Reporting | Looker |
| Cloud Platform | Google Cloud Platform (GCP) |

## Data Flow Summary

1. Store manager (UK, France, or Germany) logs into the Flask web app and uploads a sales data file.
2. Flask app validates the file and uploads it to a Google Cloud Storage bucket.
3. The incoming data is split into 3 views based on country (UK, France, Germany).
4. A Cloud Function is triggered by the new file, processes/cleans each country's data, and writes the output to the reporting data source.
5. Looker reads from the processed data source and updates its dashboards, per country and globally.
6. The CEO views up-to-date, consolidated sales performance across all three countries in Looker.

## Getting Started

### Prerequisites
- Python 3.9+
- A Google Cloud Platform project with Cloud Storage and Cloud Functions enabled
- A Looker instance connected to your GCP data source
- GCP service account credentials with appropriate IAM permissions

### Setup
1. Clone this repository.
2. Install dependencies: `pip install -r requirements.txt`
3. Set your GCP credentials as an environment variable: `export GOOGLE_APPLICATION_CREDENTIALS="path/to/credentials.json"`
4. Configure your Cloud Storage bucket name in the app's config file.
5. Run the Flask app locally: `flask run`
6. Deploy the Cloud Function using the GCP CLI, pointing it at the same Cloud Storage bucket as its trigger.
7. Connect Looker to the processed data output and publish your dashboards.

## Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## License

Specify your project's license here (e.g., MIT).

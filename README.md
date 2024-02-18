# NewsFeed Data Pipeline

## Overview
This project aims to aggregate the latest news from various RSS Feeds and make them accessible to users via a convenient API. Leveraging Apache Airflow, Kafka, Kafka-Connect Connectors, MongoDB, MinIO, Elasticsearch and Kibana, the data pipeline orchestrates periodic web scraping tasks to gather, process, and store (and visualise) news data efficiently.

### Workflow Design
The data pipeline workflow is orchestrated by an Airflow DAG and consists of the following steps:
- **Proxy Pool Refresh:** Refreshes the proxy pool to circumvent anti-scraping measures.
- **RSS Feed Scraping and Kafka:** Retrieves and validates news from specified RSS feeds, dispatching the data to Kafka topic A using selected proxies.
- **Data Ingestion to MongoDB:** Kafka Connect's Mongo Sink ingests data from topic A into MongoDB, employing an upsert operation based on unique identifiers.
- **Change Data Capture:** Debezium monitors MongoDB for changes, broadcasting these events into Kafka topic B.
- **Data Mirroring to Elasticsearch:** Updates are mirrored in Elasticsearch via the Kafka Connect Elasticsearch Sink.
- **Long-term Storage in MinIO:** Kafka Connect's S3-Minio Sink pushes records from topic B into MinIO for reliable long-term storage.
- **Data Availability:** The aggregated data is made available through a Django REST Framework and data statistics visualized using a Kibana dashboard.

## Prerequisites
Before starting with the project, ensure you have the following installed:
- Docker
- Python 3.8 or higher
- pip
- docker-compose

## Run Instructions
First clone the repository, then use the below commands as needed.
#### Build Project Infrastructure
```bash
./manage.sh up
```

#### Stop Project Infrastructure
```bash
./manage.sh stop
```

#### Delete Project Infrastructure
```bash
./manage.sh down
```

## Usage

### Accessing the API
The Django API service can be accessed through the following URLs:
- **View News Items:** http://127.0.0.1:5000/api/v1/news/
- **Search News Items:** http://127.0.0.1:5000/api/v1/news/?search=<searchphrase>
- **User Registration:** http://127.0.0.1:5000/api/v1/user/register/
- **User Login:** http://127.0.0.1:5000/api/v1/user/login/
- **Admin Page:** http://127.0.0.1:5000/admin (Requires creating a superuser beforehand)

### Accessing the Kibana Dashboard
To access the Kibana dashboard, go to http://localhost:5601 and upload the `dashboard_kibana_export.ndjson` file to set up the dashboard structure. The dashboard will be populated once data is loaded into Elasticsearch.

## Demo

   ![GIF](news1.gif)


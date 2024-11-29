## Airflow with Spark Setup

This project demonstrates how to set up Apache Airflow with Apache Spark using Docker. It provides a seamless way to manage and execute Spark jobs within Airflow DAGs. By leveraging Docker and Astronomer CLI, you can easily spin up the necessary services and start orchestrating your data workflows.


## Prerequisites

- Docker
- Docker Compose
- Astronomer CLI

## Setup Instructions

1. **Clone the repository**:
    ```sh
    git clone <repository-url>
    cd <repository-directory>
    ```

2. **Install Astronomer CLI**:
    Follow the instructions on the [Astronomer CLI installation page](https://www.astronomer.io/docs/cloud/stable/get-started/quickstart#step-4-install-the-astronomer-cli).

3. **Initialize the project**:
    ```sh
    astro dev init
    ```

4. **Start the Airflow and Spark services**:
    ```sh
    astro dev start
    ```

    This command will spin up the following Docker containers:
    - **Postgres**: Airflow's Metadata Database
    - **Webserver**: The Airflow component responsible for rendering the Airflow UI
    - **Scheduler**: The Airflow component responsible for monitoring and triggering tasks
    - **Triggerer**: The Airflow component responsible for triggering deferred tasks
    - **Spark Master**: The master node for Spark
    - **Spark Worker**: The worker node for Spark

5. **Access the Airflow UI**:
    Open your web browser and go to `http://localhost:8080`. Use the default credentials (`admin`/`admin`) to log in.

## Project Contents

- **dags**: Contains the Python files for your Airflow DAGs.
    - [final.py](dags/final.py): A DAG that demonstrates how to run a PySpark job using Airflow.
- **Dockerfile**: Contains the versioned Astro Runtime Docker image and additional configurations for Spark.
- **include**: Contains additional files for the project.
    - [`data.csv`](include/data.csv): Sample data file.
    - [read.py](include/scripts/read.py): PySpark script to read and display the CSV data.
- **docker-compose.override.yml**: Docker Compose configuration for Spark services.
- **requirements.txt**: Python packages needed for the project.

```python
from airflow.decorators import dag, task
from datetime import datetime
from pyspark import SparkContext
from pyspark.sql import SparkSession
import pandas as pd

@dag(
    start_date=datetime(2024, 1, 1),
    schedule=None,
    catchup=False,
)
def my_dag():
    @task.pyspark(conn_id="my_spark_conn")
    def read_data(spark: SparkSession, sc: SparkContext) -> pd.DataFrame:
        df = spark.createDataFrame(
            [
                (1, "John Doe", 21),
                (2, "Jane Doe", 22),
                (3, "Joe Bloggs", 23),
            ],
            ["id", "name", "age"],
        )
        df.show()
        return df.toPandas()
    
    read_data()

my_dag()
```
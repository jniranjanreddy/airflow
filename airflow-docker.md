```
python3.12 -m venv airflow
  cd /myworkspace/
  mkdir airflow-docker
  cd airflow-docker
  curl -LfO 'https://airflow.apache.org/docs/apache-airflow/2.7.2/docker-compose.yaml'
  mkdir -p ./dags ./logs ./plugins
  echo -e "AIRFLOW_UID=$(id -u)" > .env
  docker-compose up airflow-init
  docker compose up airflow-init
  docker-compose up
  docker compose up
```

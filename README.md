# airflow
## Source: https://medium.com/orchestras-data-release-pipeline-blog/installing-and-configuring-apache-airflow-a-step-by-step-guide-5ff602c47a36
## https://www.youtube.com/watch?v=K9AnJ9_ZAXE

## Postgresql if needed
```
## Postgresql - https://www.snowdba.com/postgresql-install-rhel-8/


yum install postgresql16-devel
pip3 install psycopg2
```





```
How to install airflow in Redhat 8

sudo yum update
sudo yum install python3 python3-pip

python3 - version

xport AIRFLOW_HOME=~/airflow

## Install Airflow
pip3 install apache-airflow

## chmod +x /usr/local/bin/airflow

[root@sen-kafka-milvus ~]# /usr/local/bin/airflow version
2.2.5

## Starting webserver
airflow webserver -p 8080

## Run the Airflow Scheduler
/usr/local/bin/airflow scheduler


## Initialize the Backend
airflow db init


## Creating user

airflow users  create --role Admin --username admin --email admin --firstname admin --lastname admin --password admin
```
## DAG
## Example 1: Defining a Simple DAG
```
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from datetime import datetime
def hello_world():
  print("Hello, World!")
with DAG('my_dag', start_date=datetime(2022, 1, 1), schedule_interval='@daily') as dag:
  task = PythonOperator(task_id='hello_task', python_callable=hello_world)
  task
```
## Example 2: Defining Dependencies between Tasks
from airflow import DAG
from airflow.operators.bash_operator import BashOperator
from datetime import datetime
default_args = {
'start_date': datetime(2022, 1, 1),
'schedule_interval': '@daily'
}
with DAG('my_dag', default_args=default_args) as dag:
    task1 = BashOperator(task_id='task1', bash_command='echo "Task 1"')
    task2 = BashOperator(task_id='task2', bash_command='echo "Task 2"')
    task3 = BashOperator(task_id='task3', bash_command='echo "Task 3"')
    task1 >> task2
    task1 >> task3
```
```
## Postgresql - airflow.cfg
```
CREATE DATABASE airflow;
CREATE USER airflow WITH PASSWORD 'airflow';
GRANT ALL PRIVILEGES ON DATABASE airflow TO airflow;
```
## Systemd for Airflow service.
```
root@dev ~ # cat /etc/systemd/system/airflow.service
[Unit]
Description=Start  App

[Service]
Type=forking
WorkingDirectory=/root/airflow
ExecStart=/usr/local/bin/airflow.sh

[Install]
WantedBy=multi-user.target

 cat /usr/local/bin/airflow.sh
#!/usr/bin/env bash
source bin/activate
nohup /root/airflow/bin/airflow webserver -p 8080 &
nohup /root/airflow/bin/airflow scheduler &
```
## Connecting to Postgresql
```
Find the [core] section in the airflow.cfg file and set the following values:
executor = SequentialExecutor  # Or choose the executor you prefer
sql_alchemy_conn = postgresql+psycopg2://airflow_user:your_password@localhost/airflow
```

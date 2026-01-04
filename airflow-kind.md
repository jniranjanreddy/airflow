## Installing airflow in Kind  
```
helm repo add apache-airflow https://airflow.apache.org
helm repo update

kubectl create namespace airflow

kubectl create secret generic git-credentials -n airflow \
  --from-literal=GITSYNC_USERNAME=your-username \
  --from-literal=GITSYNC_PASSWORD=your-github-pat-token \
  --from-literal=GIT_SYNC_USERNAME=your-username \
  --from-literal=GIT_SYNC_PASSWORD=your-github-pat-token

helm install airflow apache-airflow/airflow -n airflow \
  -f airflow-values.yaml \
  -f airflow-gitsync.yaml

kubectl get pods -n airflow

SCHEDULER_POD=$(kubectl get pods -n airflow -l component=scheduler -o jsonpath='{.items[0].metadata.name}')
kubectl exec -it $SCHEDULER_POD -n airflow -c scheduler -- \
  airflow users create --username admin --password admin123 \
  --firstname Admin --lastname User --role Admin --email admin@example.com



SCHEDULER_POD=$(kubectl get pods -n airflow -l component=scheduler -o jsonpath='{.items[0].metadata.name}')

# Check the synced files
kubectl exec -it $SCHEDULER_POD -n airflow -c scheduler -- ls -la /opt/airflow/dags/repo/dags/

# List DAGs
kubectl exec -it $SCHEDULER_POD -n airflow -c scheduler -- airflow dags list

# to check logs
kubectl logs $SCHEDULER_POD -n airflow -c git-sync --tail=20

kubectl exec -it $SCHEDULER_POD -n airflow -c scheduler -- airflow dags reserialize

# List DAGs
kubectl exec -it $SCHEDULER_POD -n airflow -c scheduler -- airflow dags list
```

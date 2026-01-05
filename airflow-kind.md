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
## How to update python Modules..
```
1. Create Dockerfile (in the same folder as requirements.txt):
FROM apache/airflow:3.0.2# Copy requirements fileCOPY requirements.txt /requirements.txt# Install packagesRUN pip install --no-cache-dir -r /requirements.txt

2. Build & Load into KIND:
# Build the imagedocker build -t airflow-custom:3.0.2 .# Load into KIND clusterkind load docker-image airflow-custom:3.0.2 --name airflow
3. Update airflow-values.yaml - change this section:
images:  airflow:    repository: airflow-custom    tag: 3.0.2    pullPolicy: IfNotPresent
4. Upgrade Airflow:
helm upgrade airflow apache-airflow/airflow -n airflow \  -f airflow-values.yaml \  -f airflow-gitsync-values.yaml
For AKS/Production - push to a container registry:
# Tag for your registrydocker tag airflow-custom:3.0.2 your-acr.azurecr.io/airflow-custom:3.0.2# Pushdocker push your-acr.azurecr.io/airflow-custom:3.0.2
Then update values:
images:  airflow:    repository: your-acr.azurecr.io/airflow-custom    tag: 3.0.2


```

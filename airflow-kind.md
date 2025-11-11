## Installing airflow in Kind  
```
helm repo add apache-airflow https://airflow.apache.org
helm repo update

helm install airflow apache-airflow/airflow \
  --namespace airflow \
  --create-namespace \
  --values configs/airflow-values-kind.yaml \
  --timeout 20m \
  --wait

kubectl apply -f manual-migration-job.yaml
kubectl logs -f job/airflow-manual-migration -n airflow









kubectl get secret airflow-metadata -n airflow -o jsonpath='{.data.connection}' | base64 --decode && echo
postgresql://postgres:postgres@airflow-postgresql.airflow:5432/postgres?sslmode=disable





```

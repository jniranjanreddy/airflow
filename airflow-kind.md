## 
```
kubectl get secret airflow-metadata -n airflow -o jsonpath='{.data.connection}' | base64 --decode && echo
postgresql://postgres:postgres@airflow-postgresql.airflow:5432/postgres?sslmode=disable





```

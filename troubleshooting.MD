## 

```
kubectl exec -it deployment/airflow-api-server -n airflow -- env | grep -i csrf
Defaulted container "api-server" out of: api-server, wait-for-airflow-migrations (init)
AIRFLOW__WEBSERVER__WTF_CSRF_ENABLED=False
AIRFLOW__WEBSERVER__WTF_CSRF_TIME_LIMIT=None



kubectl exec -it deployment/airflow-api-server -n airflow -- airflow config get-value webserver wtf_csrf_enabled
Defaulted container "api-server" out of: api-server, wait-for-airflow-migrations (init)
False

 kubectl exec -it deployment/airflow-api-server -n airflow -- cat /opt/airflow/airflow.cfg | grep -A 5 "\[webserver\]"
Defaulted container "api-server" out of: api-server, wait-for-airflow-migrations (init)
[webserver]
base_url = http://localhost:8080
cookie_samesite = Lax
cookie_secure = false
enable_proxy_fix = true
expose_config = true


```

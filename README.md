# spring-boot-postgres

Deploys a simple Spring Boot web application that connects to Postgres onto a Kubernetes cluster.
- Install Java,Maven,git,docker & kubernetes
- clone the code
- you must be signed into your Docker Hub account

1. Build the Spring Boot app
   ```
   mvn clean install -DskipTests package
   ```
2. Build a Docker image and push the image to Docker Hub
   ```
   docker build -t <your Docker Hub account>/spring-boot-postgres-on-k8s:v1 .
   docker push <your Docker Hub account>/spring-boot-postgres-on-k8s:v1
   ```  
3. Deploy postgres with a persistent volume claim
   ```
   kubectl create -f deploymentkubernetes/postgres.yml
   ```
4. Replace `<your Docker Hub account>` with your account name in `specs/spring-boot-app.yml`, then deploy the app
   ```
   kubectl create -f deploymentkubernetes/spring-boot-app.yml
   ```
5. Create an external load balancer for your app
   ```
   kubectl expose deployment spring-boot-postgres-sample --type=LoadBalancer --port=8080
   ```
6. Get the External IP address of Service, then the app will be accessible at `http://<External IP Address>:8080`
   ```
   kubectl get svc spring-boot-postgres-sample
   ```
   > **Note:** It may take a few minutes for the load balancer to be created
7. Scale your application
   ```
   kubectl scale deployment spring-boot-postgres-sample --replicas=3
   ```
## Deleting the Resources
1. Delete the Spring Boot app deployment
   ```
   kubectl delete -f specs/spring-boot-app.yml
   ```
2. Delete the service for the app
   ```
   kubectl delete svc spring-boot-postgres-sample
   ```
3. Delete the hostname config map
   ```
   kubectl delete cm hostname-config
   ```
4. Delete Postgres
   ```
   kubectl delete -f specs/postgres.yml
   ```

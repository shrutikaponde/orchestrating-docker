## Deploy Any Flask Project to Kubernetes

Docker image used for deployment [shrutikaponde/flask-postgres](https://hub.docker.com/r/shrutikaponde/flask-postgres).

Dockerfile[(KubDockerfile)](https://github.com/shrutikaponde/orchestrating-docker/blob/master/web/KubDockerfile%20) used to build `shrutikaponde/flask-postgres` image.

You can replace the image name with your own image from docker-hub.

For getting started clone the repo and `cd k8s`.

### Deploy to kubernetes
1. Create cluster
    ```
    kind create cluster --config kind-config.yaml --name cluster
    kubectl cluster-info --context kind-cluster
    ```

2. Setup ingress for our cluster
    ```
    kubectl apply -f "https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/kind/deploy.yaml"
    ```

3. Deploying Application
    ```
    kubectl config set-context kind-cluster --namespace blueprint
    KEY_FILE="blueprint.key"
    CERT_FILE="blueprint.crt"
    HOST="localhost"
    CERT_NAME=tls-secret

    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ${KEY_FILE} -out ${CERT_FILE} -subj "/CN=${HOST}/O=${HOST}"

    kubectl create secret generic env-secrets-blueprint --from-literal=DEBUG=False --from-literal=SECRET_KEY= --from-literal=DB_NAME=<your_db_name> --from-literal=DB_USER=<your_user_name> --from-literal=DB_PASS=<your_password> --from-literal=DB_SERVICE=<your_host_name> --from-literal=DB_PORT=5432 --dry-run -o yaml >> app.yaml && echo "---" >> app.yaml

    kubectl create secret tls ${CERT_NAME} --key ${KEY_FILE} --cert ${CERT_FILE} --dry-run -o yaml >> app.yaml

    kubectl apply -f app.yaml
    ```

4. Check all resources are created in the cluster
    ```
    kubectl get all
    ```

5. Finally we can check whether the application is accessible at https://localhost/

For detailed explaination of all steps check the references.

### References:
* <https://towardsdatascience.com/deploy-any-python-project-to-kubernetes-2c6ad4d41f14>
* <https://medium.com/@janek_schleicher/really-nice-setup-a9e47e9a3299>
Here’s a `README.md` file explaining the process to create and use the two Kubernetes manifests you provided:

---

```markdown
# Kubernetes Deployment and Service Setup

This guide explains how to create and apply two Kubernetes manifests: one for deploying an Apache application and another for exposing it to the internet via a `LoadBalancer` service.

---

## Prerequisites
1. Ensure you have **kubectl** installed and configured to interact with your Kubernetes cluster.
2. Verify that your cluster is running and accessible:
   ```bash
   kubectl cluster-info
   ```
3. If you are using a local Kubernetes setup (e.g., Minikube), note that `LoadBalancer` services might require additional configuration (e.g., using `minikube tunnel`).

---

## Manifest 1: Apache Deployment
This manifest creates a **Deployment** that manages 4 replicas of an Apache container.

### **YAML File**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: apache-deployment
  labels:
    app: apache
spec:
  replicas: 4
  selector:
    matchLabels:
      app: apache
  template:
    metadata:
      labels:
        app: apache
    spec:
      containers:
       - name: apache
         image: dreanydrew/andre18
         ports:
           - containerPort: 80
```

### **Steps to Create and Apply**
1. Save the above YAML to a file named `apache-deployment.yaml`.
2. Apply the manifest to the cluster:
   ```bash
   kubectl apply -f apache-deployment.yaml
   ```
3. Verify that the deployment is created and running:
   ```bash
   kubectl get deployments
   kubectl get pods
   ```

---

## Manifest 2: LoadBalancer Service
This manifest creates a **Service** of type `LoadBalancer`, exposing the Apache Deployment to external traffic on port 80.

### **YAML File**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: service-loadbalancer
spec:
  selector:
    app: apache
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```

### **Steps to Create and Apply**
1. Save the above YAML to a file named `service-loadbalancer.yaml`.
2. Apply the manifest to the cluster:
   ```bash
   kubectl apply -f service-loadbalancer.yaml
   ```
3. Verify that the service is created and an external IP is assigned:
   ```bash
   kubectl get service service-loadbalancer
   ```
   - If using Minikube, run:
     ```bash
     minikube tunnel
     ```
     Then check the service again to see the external IP.

---

## Verifying the Setup
1. Once both manifests are applied, ensure the pods are running:
   ```bash
   kubectl get pods
   ```
2. Access the application using the external IP assigned to the service:
   - Find the external IP:
     ```bash
     kubectl get service service-loadbalancer
     ```
   - Open a browser or use `curl` to test:
     ```bash
     curl http://<EXTERNAL-IP>
     ```

---

## Cleanup
To delete the resources created by these manifests:
```bash
kubectl delete -f apache-deployment.yaml
kubectl delete -f service-loadbalancer.yaml
```

---

## Notes
- **Image Source**: The deployment uses the image `dreanydrew/andre18`. Ensure this image is published and accessible in your container registry.
- **LoadBalancer Service**: For local clusters like Minikube, additional steps like `minikube tunnel` are required to enable `LoadBalancer` functionality.

---

This concludes the setup for deploying and exposing an Apache application using Kubernetes.

```bash
# Create deployment with 2 replicas using the given image
kubectl create deployment node-deployment --image=gcr.io/kodekloud/centos-ssh-enabled:node --replicas=2

# Expose the deployment as a NodePort service
kubectl expose deployment node-deployment --type=NodePort --port=8080 --target-port=8080 --name=node-service

# Patch the service to set nodePort to 30012
kubectl patch service node-service -p '{"spec": {"ports": [{"port": 8080, "targetPort": 8080, "nodePort": 30012}], "type": "NodePort"}}'

# Verify pods are running
kubectl get pods

# Verify service details
kubectl get svc
```

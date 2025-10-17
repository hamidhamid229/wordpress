# wordpress

Deploying WordPress on Kubernetes
This repository provides a step-by-step guide to deploying a WordPress application on a Kubernetes cluster using declarative manifests. The setup includes WordPress with an Apache web server and a MySQL database, configured with secure credentials, persistent storage, and a NodePort service for external access.
Overview
This project demonstrates how to deploy a production-ready WordPress instance on Kubernetes, leveraging best practices for container orchestration. Key components include:

WordPress: Deployed using the wordpress:php8.0-apache image, serving as the front-end application.
MySQL: Deployed using the mysql:5.7 image, providing persistent storage for the database.
Kubernetes Resources:
Secrets: For securely managing database credentials.
PersistentVolumeClaim (PVC): For durable storage with the nfs-client StorageClass.
Deployments: For managing WordPress and MySQL pods.
Services: A headless service for MySQL and a NodePort service for WordPress.



Prerequisites

A running Kubernetes cluster (e.g., Minikube for local development or a managed service like EKS/GKE).
kubectl installed and configured to interact with your cluster.
An NFS server with the nfs-client StorageClass configured (or another StorageClass like standard).
Basic understanding of Kubernetes concepts (Pods, Deployments, Services, Secrets, PVCs).

Setup Instructions

Clone the Repository:
git clone <https://github.com/hamidhamid229/wordpress.git>



Apply Manifests:Apply the Kubernetes manifests in the following order to ensure dependencies are met:

kubectl apply -f db-secret.yaml
kubectl apply -f mysql-pvc.yaml
kubectl apply -f mysql-deployment.yaml
kubectl apply -f wordpress-deployment.yaml


Verify Deployment:

Check pod status:kubectl get pods


Ensure MySQL and WordPress pods are in the Running state.
View logs for troubleshooting:kubectl logs <mysql-pod-name>
kubectl logs <wordpress-pod-name>




Access WordPress:

For Minikube:minikube service wordpress --url


For other clusters, access WordPress at <node-ip>:30080 (NodePort).
Complete the WordPress setup via the web interface if needed.



Manifest Details

db-secret.yaml: Defines a Kubernetes Secret to store sensitive database credentials (e.g., username hamid, password hamid123, database wordpress) in base64-encoded format.
mysql-pvc.yaml: Requests a 1Gi PersistentVolumeClaim using the nfs-client StorageClass for MySQL data persistence.
mysql-deployment.yaml: Deploys a MySQL pod with a headless service, mounts the PVC, and applies a securityContext to handle file permissions (fsGroup: 999).
wordpress-deployment.yaml: Deploys a WordPress pod with a NodePort service (port 30080) and connects to MySQL using environment variables from the Secret.

Notes

Ensure the nfs-client StorageClass is properly configured in your cluster. If issues arise, consider switching to a default StorageClass like standard.
Check pod logs and use kubectl describe for debugging issues like CrashLoopBackOff or storage permission errors.
For production, consider adding resource limits, replicas, and Ingress for better scalability and access control.

Troubleshooting

MySQL CrashLoopBackOff: Verify the Secret values and NFS permissions. Check logs with kubectl logs <mysql-pod-name>.
WordPress Database Connection Error: Ensure the MySQL service is running and the WORDPRESS_DB_HOST environment variable is set to mysql.
PVC Issues: Confirm the nfs-client StorageClass is available (kubectl get storageclass) and the PVC is bound (kubectl get pvc).

Contributing
Feel free to submit issues or pull requests to improve this setup or add features like autoscaling, monitoring, or Ingress support.

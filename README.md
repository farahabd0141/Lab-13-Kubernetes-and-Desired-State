Lab 13 - Kubernetes Deployment

In this lab, we took the same three-tier application from Lab 12 (Apache frontend, Flask backend, and MariaDB database) and moved it from Docker Compose into Kubernetes using k3s. The main goal was to understand how Kubernetes manages containers differently by using the idea of desired state, meaning it constantly checks and makes sure everything is running the way it’s supposed to.

What Changed from Lab 12

In Lab 12, we used Docker Compose to run multiple containers on one machine. That worked, but if a container stopped or failed, it stayed down until we manually fixed or restarted it. In this lab, Kubernetes handles that automatically. Instead of running containers directly, we define what we want running, and Kubernetes makes sure it stays that way.

Setting Up Kubernetes (k3s)

We started by installing k3s on the Ubuntu VM using the install script. After waiting for it to initialize, we verified it was working with:

kubectl get nodes

This showed the node status as Ready, which confirmed Kubernetes was running correctly.

Namespace and Secret Setup

We created a namespace called ticket-app to keep all of our resources organized:

kubectl create namespace ticket-app

Then we created a Kubernetes Secret to store our database credentials. In Lab 12, we used a .env file, but in Kubernetes we use Secrets instead. The Secret stored values like:

DB_USER
DB_PASSWORD
DB_NAME
MARIADB_ROOT_PASSWORD

We used a script (create-secret.sh) to generate and apply the secret, and verified it using:

kubectl describe secret db-credentials -n ticket-app
Writing Kubernetes Manifests

We created three YAML files inside the k8s/ directory:

db-deployment.yaml
Created a PersistentVolumeClaim (PVC) so the database data is saved even if the pod restarts
Created a Deployment for MariaDB
Created a Service so other pods can connect to it
app-deployment.yaml
Deployment for the Flask backend
Service to allow communication between Flask and the database
web-deployment.yaml
Deployment for Apache frontend
Service using NodePort so we can access it from the browser on port 30080
Deploying the Application

We deployed everything at once using:

kubectl apply -f k8s/

Kubernetes created all resources including:

Deployments
Pods
Services
PersistentVolumeClaim

We verified everything was running with:

kubectl get pods -n ticket-app
kubectl get services -n ticket-app
kubectl get pvc -n ticket-app
Troubleshooting (Important Part)

At first, the application did not work correctly. The Flask app was returning database connection errors. This was caused by:

Incorrect database credentials in the Secret
The database already being initialized with old credentials

To fix this, we:

Updated the Secret with the correct values from Lab 12
Deleted the database pod
Deleted the PVC to fully reset the database
Re-applied the manifests

This forced MariaDB to reinitialize with the correct user and database settings. After doing this, the connection issue was resolved.

Verifying the Application

We tested the application using:

curl http://localhost:30080/health

The final output was:

{"database":"connected","status":"healthy"}

This confirmed:

Flask can connect to MariaDB
Apache can reach Flask
The full application stack is working inside Kubernetes
Self-Healing (Main Concept)

To test Kubernetes self-healing, we deleted the Flask pod:

kubectl delete pod <app-pod-name> -n ticket-app

Then we watched what happened:

kubectl get pods -n ticket-app -w

After deleting the pod:

The old pod went into Terminating
Kubernetes automatically created a new pod
The new pod went from Pending → ContainerCreating → Running

This happened without any manual restart. The Deployment controller noticed the number of running pods dropped below the desired state (1), so it created a new one automatically.

Deployment Command
kubectl apply -f k8s/
Accessing the Application
http://<VM-IP>:30080
Summary

Overall, this lab showed how Kubernetes improves reliability compared to Docker Compose. Instead of manually managing containers, Kubernetes automatically:

Restarts failed pods
Maintains the desired state
Manages networking between services
Handles storage using PVCs

Even when something breaks or gets deleted, Kubernetes fixes it automatically, which is why it’s used in real production environments.


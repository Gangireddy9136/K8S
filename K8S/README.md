devops/
└── K8S/
    └── services/
        └── user-management-service/
            ├── deployment.yaml        # Kubernetes Deployment manifest
            ├── service.yaml           # Kubernetes Service manifest (ClusterIP)
            └── ingress.yaml           # Ingress resource to expose the service via ALB
            
.env                                     # Environment variables (not committed to Git)
Before starting deployment, ensure the following tools and resources are installed and configured:


| Tool                                                                           | Description                                               |
| ------------------------------------------------------------------------------ | --------------------------------------------------------- |
| [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html) | Used to authenticate and interact with AWS                |
| [kubectl](https://kubernetes.io/docs/tasks/tools/)                             | Kubernetes CLI for managing EKS                           |
| [eksctl](https://eksctl.io/introduction/)                                      | Tool to create/manage EKS clusters (Optional)             |
| [Docker](https://docs.docker.com/get-docker/)                                  | For local image builds and pushing to ECR                 |
| AWS IAM Role/Access                                                            | Ensure your IAM user/role has permissions for EKS and ALB |
| EKS Cluster                                                                    | Already created and running                               |
| AWS Load Balancer Controller                                                   | Already installed in the EKS cluster                      |

# Step 1: Configure kubectl with your EKS cluster credentials
aws eks update-kubeconfig --name <CLUSTER_NAME> --region <AWS_REGION>
# This command updates your kubeconfig file so kubectl can communicate with your EKS cluster.

# Step 2: Create a namespace for the deployment (if not already created)
kubectl create namespace <Name Space>
# Namespaces logically isolate your resources. Replace "fargate-dev" with your desired name.

# Step 3: Create Kubernetes secret from the .env file
kubectl create secret generic user-management-env --from-env-file=.env --namespace=fargate-dev
# This creates a Kubernetes secret named `user-management-env` using environment variables from the `.env` file.
# These variables will be mounted into the application pods.

# Step 4: Apply deployment manifest
kubectl apply -f deployment.yaml -n fargate-dev
# This deploys the user-management application using the defined container image and configuration.

# Step 5: Apply service manifest
kubectl apply -f service.yaml -n fargate-dev
# This creates a ClusterIP service to expose the application internally within the cluster.

# Step 6: Apply ingress manifest
kubectl apply -f ingress.yaml -n fargate-dev
# This configures the AWS ALB Ingress Controller to route traffic to your service based on path or host rules.

# Step 7: Verify Pods are running
kubectl get pods -n fargate-dev
# This lists the pods and their statuses. Ensure they are in a "Running" or "Completed" state.

# Step 8: Verify Service
kubectl get svc -n fargate-dev
# Lists services created. Ensure your user-management service is listed.

# Step 9: Verify Ingress and obtain ALB DNS
kubectl get ingress -n fargate-dev
# AWS ALB will be automatically provisioned for this Ingress. Look for the EXTERNAL-IP/DNS in the output.

# Step 10: Access the application in your browser
http://<ALB-DNS>
# Copy the ALB DNS name and access it from your browser to reach the deployed application.


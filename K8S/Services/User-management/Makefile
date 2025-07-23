# Environment variables
CLUSTER_NAME=OMP-EKS-Fargate-dev
AWS_REGION=us-east-2
NAMESPACE=fargate-dev

# Base folders
SERVICE_NAME=user-management-service
SERVICE_PATH=devops/K8S/services/$(SERVICE_NAME)
ENV_FILE=.env

# Setup EKS cluster context
configure:
	aws eks update-kubeconfig --name $(CLUSTER_NAME) --region $(AWS_REGION)

# Create namespace if not exists
namespace:
	kubectl get namespace $(NAMESPACE) || kubectl create namespace $(NAMESPACE)

# Create secret from .env file
secret:
	kubectl create secret generic $(SERVICE_NAME)-env --from-env-file=$(ENV_FILE) --namespace=$(NAMESPACE) --dry-run=client -o yaml | kubectl apply -f -

# Apply all manifests (deployment, service, ingress)
deploy:
	kubectl apply -f $(SERVICE_PATH)/deployment.yaml -n $(NAMESPACE)
	kubectl apply -f $(SERVICE_PATH)/service.yaml -n $(NAMESPACE)
	kubectl apply -f $(SERVICE_PATH)/ingress.yaml -n $(NAMESPACE)

# View status
status:
	kubectl get all -n $(NAMESPACE)

# Combined command
all: configure namespace secret deploy status

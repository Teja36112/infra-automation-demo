# automation-demo — Docker + Kubernetes + Ansible starter

This repository is a starter automation project showing how to:

- Containerize a simple Flask app with Docker.
- Build and push the image with Ansible.
- Deploy the app to Kubernetes using Ansible to apply manifests.
- Example CI workflow to build and push images on git push.

Prerequisites
- Docker (for local image build)
- Ansible >= 2.10 and the following collections:
  - community.docker
  - community.kubernetes
  Install with: `ansible-galaxy collection install community.docker community.kubernetes`
- A Kubernetes cluster (minikube, kind, or cloud) and kubeconfig accessible
- A container registry (Docker Hub, GitHub Container Registry, AWS ECR, etc.)

Quick start (local test with kind/minikube)
1. Build & push the image locally (push can be to your registry or a local registry):
   ANSIBLE_REMOTE_USER=local ansible-playbook -i ansible/inventory.ini ansible/playbooks/build_and_push.yml \
     -e "registry=ghcr.io/youruser image_name=automation-demo tag=latest"

2. Deploy to Kubernetes:
   ansible-playbook -i ansible/inventory.ini ansible/playbooks/deploy_k8s.yml \
     -e "kubeconfig_path=~/.kube/config registry=ghcr.io/youruser image_name=automation-demo tag=latest"

3. Access the app:
   - If using Minikube: `minikube service automation-demo --url`
   - Otherwise, port-forward: `kubectl port-forward svc/automation-demo 5000:80`

Files of interest
- app/ : Flask app + Dockerfile
- k8s/ : Kubernetes Deployment + Service manifests
- ansible/playbooks/ : Build/push and deploy playbooks
- .github/workflows/ci.yml : Example GitHub Actions build & push workflow

Notes and next improvements
- Replace registry credentials and kubeconfig values in `ansible-playbooks` or pass as extra vars.
- To provision Kubernetes nodes with Ansible, add playbooks that install Docker/kubeadm and run kubeadm init/join.
- To support multiple environments, convert k8s manifests to kustomize overlays or Helm charts.

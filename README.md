# ArgoCD Kubernetes Deployment Tutorial

This guide walks you through deploying a sample application to a local Kubernetes cluster using Minikube and ArgoCD, with GitOps best practices.

## Prerequisites

- [Minikube](https://minikube.sigs.k8s.io/docs/) installed
- [kubectl](https://kubernetes.io/docs/tasks/tools/) installed
- [ArgoCD CLI](https://argo-cd.readthedocs.io/en/stable/cli_installation/) (optional, for advanced usage)
- [Git](https://git-scm.com/)

## 1. Start Minikube

```sh
minikube start
```

## 2. Install ArgoCD in the `argocd` Namespace

```sh
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## 3. Expose the ArgoCD API Server (for local access)

```sh
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

## 4. Get the ArgoCD Admin Password

```sh
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

## 5. Login to ArgoCD UI

- Open [http://localhost:8080](http://localhost:8080) in your browser.
- Username: `admin`
- Password: (from previous step)

<!-- ## 6. Register Your Cluster with ArgoCD (if needed)

If using Minikube, your cluster is usually accessible by default. If not, run:

```sh
argocd cluster add <context-name>
``` -->

## 6. Deploy the Application via ArgoCD

### Option 1: Using the ArgoCD UI

- Click **New App** and fill in:
  - **Application Name**: `myapp-argo-application`
  - **Project**: `default`
  - **Repository URL**: `https://github.com/zannujulius/argo-cd-demo.git`
  - **Revision**: `HEAD`
  - **Path**: `dev`
  - **Destination Cluster**: `https://kubernetes.default.svc`
  - **Namespace**: `myapp`
- Click **Create** and **Sync**.

### Option 2: Using the Application Manifest

Apply the provided `application.yaml`:

```sh
kubectl apply -f argocd-app-config/application.yaml -n argocd
```

## 7. Application Structure

- `argocd-app-config/application.yaml`: ArgoCD Application manifest
- `argocd-app-config/dev/deployment.yaml`: Kubernetes Deployment
- `argocd-app-config/dev/service.yaml`: Kubernetes Service

## 8. Access the Deployed Application

Get the Minikube service URL:

```sh
minikube service myapp-service -n myapp
```

---

## Cleanup

To delete everything:

```sh
minikube delete
```

---

## References

- [ArgoCD Official Docs](https://argo-cd.readthedocs.io/)
- [Minikube Docs](https://minikube.sigs.k8s.io/docs/)

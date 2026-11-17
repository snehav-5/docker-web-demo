# Docker Web Application — Project Repository

**Student:** Bhavya Srivastava  
**Roll number:** 2401331540089  
**Section:** B

## 1. Project architecture

This project is a small Python Flask web application packaged in a Docker container. A browser sends an HTTP request to the application. Flask handles the `/` route and returns `Hello from Docker!`. Docker packages the application and its Python dependency into an image; a container runs that image and exposes port 5000. When run locally, host port 8080 can be mapped to container port 5000.

Main components:
- `app.py`: Flask application and `/` route.
- `requirements.txt`: pinned Flask dependency.
- `Dockerfile`: instructions for building the container image.
- Docker image/container: portable runtime environment.
- Browser: sends requests and displays the response.
- Docker Hub: optional registry for sharing the built image.

## 2. DevOps tools and practical use

- **Git:** tracks source-code changes and supports version history and rollback.
- **GitHub:** hosts the Git repository and triggers the included GitHub Actions workflow on pushes and pull requests to `main`.
- **Docker:** builds a repeatable image and runs the Flask application in an isolated container.
- **Docker Hub:** can store and distribute the image after a successful authenticated push.

The supplied practical procedure demonstrates Docker image creation, running the container, and the Docker Hub push/pull workflow. Jenkins, Ansible, and a live Kubernetes cluster are not represented as already deployed in this starter project. Kubernetes YAML files are included as a deployment example for a future cluster.

## 3. CI/CD workflow

The included `.github/workflows/ci.yml` implements a basic continuous-integration workflow:
1. **Trigger:** a push or pull request targeting `main` starts the workflow.
2. **Checkout:** GitHub Actions downloads the repository source.
3. **Environment setup:** Python 3.12 is installed.
4. **Dependency installation:** Flask is installed from `requirements.txt`.
5. **Testing:** Python's `unittest` runner checks that `/` returns HTTP 200 and the expected response.
6. **Container build:** Docker builds an image tagged with the commit SHA.

This workflow tests the code and verifies that the container image can be built. It does not automatically publish or deploy the image. A production CD stage can be added using protected GitHub repository secrets for registry credentials and a secured deployment environment.

## 4. Cloud platform evaluation — AWS (proposed)

AWS is selected as a proposed hosting platform, not as an already-configured deployment:
- **Scalability:** Amazon EKS can run multiple replicas and scale workloads; an Application Load Balancer can distribute requests.
- **Availability:** deploy across multiple Availability Zones and use Kubernetes health checks and replica recovery.
- **Security:** use IAM least-privilege roles, private subnets, security groups, TLS, and Secrets Manager.
- **Performance:** use an Application Load Balancer, appropriately sized worker nodes, and CloudWatch metrics to identify bottlenecks.
- **Project fit:** Amazon ECR can store Docker images, while EKS can orchestrate containers if the application grows beyond a single local container.

For a small demonstration app, a full EKS cluster may cost more and add more operational work than necessary. A simpler AWS container service may be more economical until scaling or orchestration needs justify EKS.

## 5. Improved DevOps and cloud architecture

Recommended future flow:

`Developer -> Git/GitHub -> GitHub Actions -> Tests -> Docker build -> Amazon ECR -> AWS deployment -> Load Balancer -> Flask containers`

Improvements:
- Automate tests and image builds on every pull request and merge.
- Scan dependencies and container images before release.
- Store images in Amazon ECR and deploy immutable version tags.
- Use EKS (or a simpler managed container service for a small workload) for deployment and scaling.
- Configure readiness/liveness probes, multiple replicas, and rolling updates.
- Collect logs and metrics with Amazon CloudWatch; create alerts for errors and resource usage.
- Use HTTPS, IAM roles, secrets management, network restrictions, and regular dependency updates.
- Keep rollback-ready image versions and use deployment approvals for production.

## Run locally

```bash
docker build -t docker-web-demo:1.0 .
docker run -d --name web-demo -p 8080:5000 docker-web-demo:1.0
```

Open `http://localhost:8080`. The expected response is `Hello from Docker!`.

Stop and remove the container:

```bash
docker stop web-demo
docker rm web-demo
```

## Run tests

```bash
pip install -r requirements.txt
python -m unittest discover -s tests -v
```

## Optional Docker Hub push

Replace `YOUR_DOCKERHUB_USERNAME` with your own Docker Hub username:

```bash
docker login
docker tag docker-web-demo:1.0 YOUR_DOCKERHUB_USERNAME/docker-web-demo:1.0
docker push YOUR_DOCKERHUB_USERNAME/docker-web-demo:1.0
```

## Optional Kubernetes example

After replacing the image placeholder in `k8s/deployment.yaml` with an image you have published:

```bash
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
kubectl get deployments,pods,services
```

The Kubernetes manifests are examples; they require a configured Kubernetes cluster and a published image before they can deploy successfully.

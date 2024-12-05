# **Project Documentation: Ray Serve-Based Scalable Machine Learning System**

## **Project Overview**
This project implements a scalable, production-grade machine learning system using **Ray Serve**, designed for efficient deployment and serving of machine learning models. The system is optimized to handle dynamic workloads by leveraging both **horizontal scaling** and **vertical scaling**, ensuring high performance, reliability, and cost efficiency.

---

## **Key Features**
1. **Scalability**:
   - **Horizontal Scaling**: Add multiple replicas to handle increased workloads.
   - **Vertical Scaling**: Allocate more computational resources (CPU/GPU) to a single replica for resource-intensive workloads.

2. **Dynamic Load Balancing**:
   - Automatically distributes incoming requests among replicas.
   - Ensures optimal utilization of resources and minimizes response times.

3. **Concurrent Request Handling**:
   - Configurable concurrency per replica using `max_concurrent_queries`.

4. **Autoscaling**:
   - Integrates with Ray Autoscaler for dynamic replica adjustment based on real-time traffic.

5. **Kubernetes Integration**:
   - Uses **KubeRay** to deploy the system on Kubernetes clusters.
   - Supports GPU-enabled nodes for deep learning inference.

---

## **System Architecture**
### **Components**:
1. **Ray Serve Router**:
   - Directs incoming requests to the appropriate replicas.
   - Ensures efficient load balancing.

2. **Model Deployment**:
   - Deployed machine learning models are served as RESTful endpoints.
   - Supports custom business logic with Python-based implementations.

3. **Autoscaler**:
   - Dynamically adjusts the number of replicas and resources based on workload.

4. **Kubernetes Cluster**:
   - Manages node pools, GPU resources, and networking for the system.

---

## **Setup Instructions**

### **Prerequisites**
1. **Kubernetes Cluster**:
   - GKE, EKS, or AKS with sufficient resources.
   - Ensure GPUs are available if using GPU-based workloads.

2. **Ray Installation**:
   - Ray version `2.9.0` or higher.
   - Install Ray Serve:
     ```bash
     pip install "ray[serve]"
     ```

3. **Ray Serve Deployment YAML**:
   - Ensure the deployment file is properly configured.

### **Steps**

#### **1. Create Kubernetes Cluster**
- Example for GCP:
  ```bash
  gcloud container clusters create assignment-kuberay-gpu-cluster \
      --accelerator type=nvidia-t4-vws,count=1 \
      --num-nodes=1 --min-nodes 1 --max-nodes 2 --enable-autoscaling \
      --zone=us-east1-c --machine-type n1-standard-8
  ```

#### **2. Deploy Ray Serve on Kubernetes**
- Apply the deployment configuration:
  ```bash
  kubectl apply -f deployment.yaml
  ```

#### **3. Verify Deployment**
- Check Ray Serve deployment status:
  ```bash
  kubectl get rayservice
  ```

- View running pods:
  ```bash
  kubectl get pods
  ```

#### **4. Access the Model Endpoint**
- Retrieve the service URL:
  ```bash
  kubectl get service
  ```

- Test the model using `curl` or an API client:
  ```bash
  curl -X POST http://<service-url>/predict -d '{"input_data": "sample"}'
  ```

---

## **Configuration Details**

### **Deployment YAML Configuration**
#### Example YAML:
```yaml
apiVersion: ray.io/v1
kind: RayService
metadata:
  name: rayservice-insight-text
spec:
  serveConfigV2: |
    applications:
      - name: InsightText
        import_path: main:endpoint_app
        route_prefix: /
        runtime_env:
          working_dir: "https://github.com/<your-repo>/InsightText/archive/refs/heads/master.zip"
          pip:
            - torch
            - transformers
  rayClusterConfig:
    rayVersion: '2.9.0'
    headGroupSpec:
      rayStartParams:
        dashboard-host: '0.0.0.0'
      template:
        spec:
          containers:
            - name: ray-head
              image: rayproject/ray:2.9.0
              resources:
                limits:
                  cpu: 4
                  gpu: 1
                  memory: '4Gi'
                requests:
                  cpu: 2
                  gpu: 1
                  memory: '2Gi'
    workerGroupSpecs:
      - replicas: 1
        rayStartParams: {}
        template:
          spec:
            containers:
              - name: ray-worker
                image: rayproject/ray:2.9.0
                resources:
                  limits:
                    cpu: "1"
                    memory: "2Gi"
                  requests:
                    cpu: "500m"
                    memory: "2Gi"
```

---

## **Scaling Configuration**

### **Horizontal Scaling**
- Increase `num_replicas` in the deployment:
  ```python
  @serve.deployment(num_replicas=3)
  class MyService:
      ...
  ```

### **Vertical Scaling**
- Adjust `ray_actor_options` for resource allocation:
  ```python
  @serve.deployment(ray_actor_options={"num_cpus": 4, "num_gpus": 1})
  class MyService:
      ...
  ```

### **Autoscaling**
- Example config:
  ```yaml
  autoscalingConfig:
    minReplicas: 1
    maxReplicas: 5
    targetNumOngoingRequestsPerReplica: 10
  ```

---

## **Performance and Testing**

### **Stress Testing**
- Use tools like `locust` or `ab` to simulate load and test scaling behavior.

### **Monitoring**
- Access the Ray Dashboard:
  ```bash
  kubectl port-forward <ray-head-pod> 8265:8265
  ```
- View logs and resource usage:
  ```bash
  kubectl logs <pod-name>
  ```

---

## **Best Practices**
1. Use **autoscaling** to optimize resource usage during peak loads.
2. Allocate **GPUs** only for replicas requiring heavy computation (e.g., deep learning models).
3. Monitor cluster health and resource utilization regularly via the Ray dashboard or Kubernetes monitoring tools.
4. Use **persistent storage** for model weights to prevent reloading during pod restarts.

---

## **Future Enhancements**
1. Implement **distributed training** to update models dynamically.
2. Add **caching mechanisms** for frequent queries to reduce latency.
3. Enable **multi-tenancy** to support multiple models under the same deployment.

---

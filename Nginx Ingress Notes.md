# Nginx, Ingress, Ingress Controller, and Traefik

## 1️⃣ Nginx

* Web server & reverse proxy.
* Serves static files, routes requests, and load balances.
* Used in Kubernetes as a containerized app or ingress controller.
* Example: Routes `http://yourserver.com` → `http://app:5000`

---

## 2️⃣ Ingress

* Kubernetes resource that **manages external access** to services.
* Can do:

  * Host-based routing (`app1.example.com → service1`)
  * Path-based routing (`/api → api-service`)
  * TLS/SSL termination
* **Example YAML**:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
spec:
  rules:
  - host: myapp.local
    http:
      paths:
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80
```

---

## 3️⃣ Ingress Controller

* Implements **Ingress rules** in Kubernetes.
* Without it, Ingress does nothing.
* Popular options:

  * Nginx Ingress Controller
  * Traefik (default in K3s)
* **Example Deployment Command:**

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.14.0/deploy/static/provider/cloud/deploy.yaml
```

---

## 4️⃣ Traefik

* Modern ingress controller & reverse proxy.
* Features:

  * Auto-detects Kubernetes services
  * Dynamic routing
  * Automatic HTTPS (Let’s Encrypt)
  * Dashboard & metrics
* Preferred in K3s / lightweight clusters.

---

## 5️⃣ How They Work Together

```
         🌐 Internet
             |
             ▼
   [Ingress Controller: Traefik/Nginx]
             |
        -----------------
        |               |
  Ingress Rules      Ingress Rules
 (host/path routing) (host/path routing)
        |               |
     Service 1        Service 2
   (ClusterIP)       (ClusterIP)
        |               |
     Pod(s) App1     Pod(s) App2
```

**Legend / Quick Memorize:**

* **Ingress Controller** → engine implementing rules
* **Ingress Rules** → mapping host/path → service
* **Service** → internal load balancer to pods
* **Pod(s)** → actual running app/container

---

## 6️⃣ Key Points / Interview Tips

| Term               | Key Notes                                           |
| ------------------ | --------------------------------------------------- |
| Nginx              | Web server, reverse proxy, load balancer            |
| Ingress            | K8s object, routes external traffic                 |
| Ingress Controller | Engine that implements ingress rules                |
| Traefik            | Dynamic, auto-SSL, dashboard, default in K3s        |
| Service            | ClusterIP, NodePort, LoadBalancer: internal routing |
| Pod                | Actual containerized app                            |







## Typical Interview Questions & Answers

**1. Difference between Ingress and Ingress Controller?**

* **Ingress:** Kubernetes object/resource that defines rules for routing external HTTP/HTTPS traffic to internal services.
* **Ingress Controller:** The actual pod/software that implements the Ingress rules and routes the traffic accordingly. Without the controller, Ingress rules do nothing.

**2. Why Traefik is preferred in K3s?**

* Lightweight and dynamic, ideal for resource-constrained clusters.
* Automatically detects Kubernetes services.
* Provides automatic HTTPS with Let's Encrypt.
* Dashboard & metrics built-in.

**3. How to expose multiple apps on same domain using paths?**

* Use **path-based routing** in Ingress rules.
* Example:

  ```yaml
  rules:
    - host: myapp.local
      http:
        paths:
        - path: /api
          pathType: Prefix
          backend:
            service:
              name: api-service
              port:
                number: 80
        - path: /web
          pathType: Prefix
          backend:
            service:
              name: web-service
              port:
                number: 80
  ```
* `/api` goes to `api-service`, `/web` goes to `web-service`.

**4. Nginx outside K8s vs Nginx Ingress Controller?**

* **Nginx outside K8s:** Standard web server or reverse proxy, manually configured, serves traffic to apps on VMs or containers.
* **Nginx Ingress Controller:** Runs inside Kubernetes, dynamically routes traffic based on Ingress rules, manages multiple services automatically, and integrates with K8s objects.

# 🚀 Kubernetes App Deployment with `k8s-helper`

This guide walks through deploying a PostgreSQL database and a Django application using [`k8s-helper`](https://github.com/your-repo/k8s-helper), followed by a rolling update using the `rolling-update` command.

---

## 📦 Step 1: Deploy PostgreSQL

```bash
k8s-helper apply db postgres:15 \
  --replicas 1 \
  --port 5432 \
  --service-type ClusterIP \
  --env POSTGRES_DB=todoapp,POSTGRES_USER=postgres,POSTGRES_PASSWORD=postgres \
  --labels app=db
```

✅ This creates a PostgreSQL pod with:
- DB name: `todoapp`
- Username: `postgres`
- Password: `postgres`

You can verify the pod and service:

```bash
kubectl get pods
kubectl get svc
```

---

## 🧠 Step 2: Deploy Django (Latest Tag)

```bash
k8s-helper apply django-todo codernocap/django-todo:latest \
  --replicas 3 \
  --port 8000 \
  --service-type NodePort \
  --env DB_NAME=todoapp,DB_USER=postgres,DB_PASSWORD=postgres,DB_HOST=db,DB_PORT=5432 \
  --labels app=django-todo
```

### 🔍 Check App Access

```bash
minikube service django-todo-service --url
```

---

## 🔄 Step 3: Rolling Update (v2 Image)

```bash
k8s-helper rolling-update django-todo \
  --image codernocap/django-todo:v2 \
  --replicas 4 \
  --show-status
```

This performs a **zero-downtime update** with new replicas and the `v2` image.

---

## 👀 Watch Rollout in Real-Time

### Pod Rollout

```bash
watch kubectl get pods
```

### Deployment Status

```bash
kubectl rollout status deployment/django-todo
```

---

## 🔁 Optional: Rollback (Using kubectl)

If you want to rollback manually:

```bash
kubectl rollout undo deployment django-todo
```

---

## 📌 Notes

- `k8s-helper` automatically creates Deployments, Services, and manages environment variables.
- It supports cleanup using:

```bash
k8s-helper cleanup <deployment-name>
```

---

## 🤝 Contribute

Found a bug or have a feature idea? PRs are welcome!  
Let’s make Kubernetes simpler — one helper at a time.

---

**Made with 💻 by [Harshit Chatterjee](https://github.com/harshitcha)**
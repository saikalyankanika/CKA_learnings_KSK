# Kubernetes YAML Generation – Complete Practical Cheat Sheet

A complete reference for quickly generating, exporting, editing, and managing Kubernetes YAML manifests using kubectl.

---

# 1. Core Concept

Always generate YAML without applying first:

kubectl create <resource> <name> --dry-run=client -o yaml > file.yaml

Then edit and apply:

kubectl apply -f file.yaml

This is the fastest and cleanest workflow for CKA and real-world DevOps/SRE work.

---

# 2. Generate YAML for New Resources

## 2.1 Deployment

kubectl create deployment nginx \
  --image=nginx \
  --replicas=3 \
  --dry-run=client -o yaml > deployment.yaml

---

## 2.2 Service

### ClusterIP

kubectl create service clusterip my-svc \
  --tcp=80:80 \
  --dry-run=client -o yaml > svc.yaml

### NodePort

kubectl create service nodeport my-svc \
  --tcp=80:80 \
  --dry-run=client -o yaml > svc-nodeport.yaml

### LoadBalancer

kubectl create service loadbalancer my-svc \
  --tcp=80:80 \
  --dry-run=client -o yaml > svc-lb.yaml

---

## 2.3 ConfigMap

### From Literal

kubectl create configmap app-config \
  --from-literal=ENV=prod \
  --dry-run=client -o yaml > configmap.yaml

### From File

kubectl create configmap app-config \
  --from-file=app.env \
  --dry-run=client -o yaml > configmap-file.yaml

---

## 2.4 Secret

kubectl create secret generic my-secret \
  --from-literal=password=1234 \
  --dry-run=client -o yaml > secret.yaml

Note: Secret values are base64 encoded.

---

## 2.5 Job

kubectl create job job-demo \
  --image=busybox \
  -- bash -c "echo hello" \
  --dry-run=client -o yaml > job.yaml

---

## 2.6 StatefulSet

kubectl create statefulset sts-demo \
  --image=nginx \
  --dry-run=client -o yaml > statefulset.yaml

---

## 2.7 DaemonSet

kubectl create daemonset ds-demo \
  --image=nginx \
  --dry-run=client -o yaml > daemonset.yaml

---

## 2.8 ServiceAccount

kubectl create serviceaccount sa-demo \
  --dry-run=client -o yaml > sa.yaml

---

## 2.9 Role

kubectl create role pod-reader \
  --verb=get,list,watch \
  --resource=pods \
  --dry-run=client -o yaml > role.yaml

---

## 2.10 RoleBinding

kubectl create rolebinding bind-pod-reader \
  --role=pod-reader \
  --serviceaccount=default:sa-demo \
  --dry-run=client -o yaml > rolebinding.yaml

---

# 3. Generate YAML for a Pod (Quick Testing Only)

kubectl run test-pod \
  --image=busybox \
  --restart=Never \
  --dry-run=client -o yaml > pod.yaml

Use only for temporary testing. For production workloads use Deployments.

---

# 4. Export Existing Resource YAML

## Export Specific Resource

kubectl get deployment my-app -n prod -o yaml > my-app.yaml

## Export All Resources in Namespace

kubectl get all -n staging -o yaml > staging-all.yaml

---

# 5. Clean Export (Remove Runtime Fields)

After exporting YAML, remove:

- status
- metadata.uid
- metadata.resourceVersion
- metadata.creationTimestamp
- metadata.managedFields

These are cluster-generated values and should not be reused.

---

# 6. Discover and Inspect Resources

## List All Resource Types

kubectl api-resources

## Explain Resource Structure

kubectl explain deployment

Deep explanation:

kubectl explain deployment.spec.template.spec.containers

---

# 7. Patch Existing Resources

## Update Replicas

kubectl patch deployment my-app \
  -p '{"spec":{"replicas":5}}'

## JSON Patch Example

kubectl patch service my-svc \
  --type=json \
  -p='[{"op":"replace","path":"/spec/type","value":"NodePort"}]'

---

# 8. Recommended Workflow

1. Generate YAML using:
   kubectl create ... --dry-run=client -o yaml

2. Edit:
   - replicas
   - resource requests/limits
   - environment variables
   - volume mounts
   - liveness/readiness probes
   - labels and selectors

3. Apply:
   kubectl apply -f file.yaml

4. Verify:
   kubectl get pods
   kubectl describe <resource>
   kubectl logs <pod>

---

# 9. Quick Reference Table

Goal | Command Pattern
-----|----------------
Generate new YAML | kubectl create ... --dry-run=client -o yaml
Export existing YAML | kubectl get ... -o yaml
Quick Pod YAML | kubectl run ... --restart=Never --dry-run=client -o yaml
List resources | kubectl api-resources
Explain fields | kubectl explain <resource>
Apply YAML | kubectl apply -f file.yaml

---

# 10. Production Best Practices

- Use Deployments instead of standalone Pods.
- Always define resource requests and limits.
- Use labels consistently.
- Remove runtime metadata before reapplying exported YAML.
- Store YAML in version control.
- Use Helm or Kustomize for reusable templates.
- Add liveness and readiness probes for applications.
- Use namespaces for environment separation.

---

End of Complete Kubernetes YAML Generation Cheat Sheet

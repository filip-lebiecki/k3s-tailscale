# k3s-tailscale

[Youtube video ](https://youtu.be/IeYuZtvMBqQ)

### K3s install + helm install
```
curl -sfL https://get.k3s.io | sh -s - server --cluster-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/16
install -D /etc/rancher/k3s/k3s.yaml .kube/config
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

### Tailscale Kubernetes Operator
```
helm repo add tailscale https://pkgs.tailscale.com/helmcharts
helm repo update
helm upgrade --install tailscale-operator tailscale/tailscale-operator --namespace=tailscale --create-namespace --set-string oauth.clientId= --set-string oauth.clientSecret= --wait
```

ACL
```
"tagOwners": {
   "tag:k8s-operator": [],
   "tag:k8s": ["tag:k8s-operator"],
},
```

### PostgreSQL - ClusterIP

```
kubectl apply -f postgres-dp.yam
kubectl apply -f postgres-svc.yaml

vi postgres-svc.yaml
annotations:
    tailscale.com/expose: "true"
    tailscale.com/hostname: "postgres"

kubectl apply -f postgres-svc.yaml
```

### PostgreSQL - LoadBalancer

```
vi postgres-lb.yaml
type: LoadBalancer
loadBalancerClass: tailscale

annotations:
    tailscale.com/hostname: "postgres-lb"

kubectl apply -f postgres-lb.yaml
```

### Alpine + WeTTY

```
kubectl apply -f alpine-sshd.yaml
kubectl apply -f wetty-dp.yaml
<enable HTTPS in Tailscale console>
kubectl apply -f wetty-ingress.yaml
kubectl get ingress
kubectl describe ingress wetty
```

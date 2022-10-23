```bash
# create namespace
kubectl apply -f namespace.yaml

# create deployment + service
kubectl apply -f api
kubectl apply -f web

# generate SSL
openssl req -newkey rsa:2048 -x509 -nodes -keyout odds-shop-tls.key -new -out odds-shop-tls.crt -subj '/CN=*.odds-shop.team' -extensions v3_new -config <(cat /System/Library/OpenSSL/openssl.cnf <(printf '[v3_new]\nsubjectAltName=DNS:*.odds-shop.team\nextendedKeyUsage=serverAuth')) -sha256 -days 3650

cat odds-shop-tls.crt | base64 | pbcopy
cat odds-shop-tls.key | base64 | pbcopy

# ingress + TLS
kubectl apply -f secret.yaml
kubectl apply -f ingress.yaml
kubectl apply -f ingress-tls.yaml

# apply host
echo "127.0.0.1 www.odds-shop.team
127.0.0.1 api.odds-shop.team" | sudo tee -a /etc/hosts

# verify host
cat /etc/hosts
```

```bash
# delete namespace
kubectl delete -f namespace.yaml

# delete resource
kubectl delete -f api
kubectl delete -f web
kubectl delete -f ingress.yaml
kubectl delete -f secret.yaml

# checking
kubectl get pod -n odds-shop-dev
kubectl get service -n odds-shop-dev
kubectl get ing -n odds-shop-dev
```


```
docker push ghcr.io/bazsup/odds-shopweb:v1
docker push ghcr.io/bazsup/odds-shopapi:v1
```
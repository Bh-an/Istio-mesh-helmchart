# Istio-mesh helm-chart

## Steps for Deployment

- Create kubectl secret for Docker Image

```
kubectl create secret docker-registry regcred \
--docker-server=docker.io \
--docker-username=<<>> \
--docker-password=<<>> \
--docker-email=<<>>
```

- Install istioctl and sidecar in namespace
```
istioctl install https://istio.io/latest/docs/setup/getting-started/	
istioctl install --set profile=default -y
kubectl label namespace default istio-injection=enabled
```

- Install cert-manager helm-chart
```
helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.10.1 \
  --set installCRDs=true
```

- Install istio helm-chart
```
helm install istio-cert -f values.yaml <path_to_chart>
```

- Istio Endpoints
```
export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')

export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')

export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT

echo $GATEWAY_URL
```

*Paste the gateway url as an Alias record in your subdomain!*


- To Analyze:
```
istioctl analyze
kubectl describe certificate cert -n istio-system
```

- To uninstall later:
```
kubectl delete gateway webapp-gateway -n istio-system
kubectl delete virtualservice webapp-vs 
```

- Uninstall Helm Chart
```
helm uninstall istio-cert
```

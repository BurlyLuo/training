##### kind-c1
kind create cluster --config ./clustermesh/kind-config1.yaml --name c1
helm repo add cilium https://helm.cilium.io/
helm install cilium cilium/cilium --version 1.11.1 \
  --namespace kube-system \
  --set ipam.mode=kubernetes \
  --set cluster.id=1 \
  --set cluster.name=cluster1

helm repo add bitnami https://charts.bitnami.com/bitnami
helm install metallb bitnami/metallb \
  --namespace kube-system \
  -f ./clustermesh/configmap1.yaml

cilium clustermesh enable --create-ca --service-type LoadBalancer
cilium hubble enable

kubectl get secret --context kind-c1 -n kube-system cilium-ca -o yaml > cilium-ca.yaml



##### kind-c2 
kind create cluster --config ./clustermesh/kind-config2.yaml --name c2

helm install cilium cilium/cilium --version 1.11.1 \
  --namespace kube-system \
  --set ipam.mode=kubernetes \
  --set cluster.id=2 \
  --set cluster.name=cluster2

helm install metallb bitnami/metallb \
  --namespace kube-system \
  -f ./clustermesh/configmap2.yaml

kubectl apply -f cilium-ca.yaml --context kind-c2
cilium clustermesh enable  --service-type LoadBalancer
cilium hubble enable

cilium clustermesh connect --destination-context kind-c1


## Verify:
kubectl apply -f https://raw.githubusercontent.com/cilium/cilium/1.11.2/examples/kubernetes/clustermesh/global-service-example/cluster1.yaml --context kind-c1
kubectl apply -f https://raw.githubusercontent.com/cilium/cilium/1.11.2/examples/kubernetes/clustermesh/global-service-example/cluster2.yaml --context kind-c2
kubectl exec -ti deployment/x-wing -- curl rebel-base
for i in $(seq 1 10000);do kubectl exec -ti deployment/x-wing -- curl rebel-base;done

kubectl scale deployment rebel-base --replicas=0 --context kind-c1 



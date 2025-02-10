# Install MetalLB

The MetalLB documentation may be found [here](https://metallb.universe.tf/).

## Deploy MetalLB

1. Add helm repo
    ```bash
    helm repo add metallb https://metallb.github.io/metallb
    helm repo update
    ```
1. Create namespace for the deployment
    ```
    kubectl create namespace metallb-system
    kubectl config set-context --current --namespace metallb-system
    ```
1. Deploy the chart
    ```
    helm install metallb metallb/metallb
    ```

At this point there will be one Deployment and one DaemonSet created. The pods of the DaemonSet will not be running until we complete the configuration.

## Configure

First, we have to choose some unused IP addresses in the network for MetalLB to own. The way I have my home network set up is as a block of 256 addresses at 192.168.230.0/24.

* The first block of 64 addresses is reserved for static IPs such as the router and any servers (e.g. the cluster nodes)
* The next block of 64 is managed by a DCHP scope for laptops, phones etc.
* The remaining addresses are free - and it is from this pool that I'll assign addresses to MetalLB

To configure MetalLB, we now need to create and apply two configuration resources

1. An [address pool]
    ```yaml
    apiVersion: metallb.io/v1beta1
    kind: IPAddressPool
    metadata:
      name: default-pool
      namespace: metallb-system
    spec:
       addresses:
       - 192.168.2.200-192.168.2.220
    ```
1. A [Layer2 Advertisement]
    ```yaml
    apiVersion: metallb.io/v1beta1
    kind: L2Advertisement
    metadata:
      name: default-pool-advertisement
      namespace: metallb-system
    spec:
      ipAddressPools:
      - default-pool
    ```
Once you apply these manifests, the DaemonSet pods should start.

## Test

Now we shall create a deployment with a service of type `LoadBalancer` to expose it.

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: node-test-app-blue
```


```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-test-app-blue-deployment
  namespace: node-test-app-blue
spec:
  replicas: 1
  selector:
    matchLabels:
      app: node-test-app-blue
  template:
    metadata:
      labels:
        app: node-test-app-blue
    spec:
      containers:
        - name: node-test-app-blue-container
          image: bahajian/node_test_app:blue
          ports:
            - containerPort: 80
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: node-test-app-blue-service
  namespace: node-test-app-blue
spec:
  selector:
    app: node-test-app-blue
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 80
      protocol: TCP
  loadBalancerIP: 192.168.2.200
```

Give it 30 seconds or so to all come up, then -

```bash
kubectl get services -n default
```

> Output

```
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP       PORT(S)        AGE
kubernetes   ClusterIP      10.96.0.1       <none>            443/TCP        41d
nginx        LoadBalancer   10.96.223.238   192.168.230.129   80:31538/TCP   52s
```

Now we see that MetalLB has intercepted the `LoadBalancer` service type and assigned the service an external IP from the address pool! Let's check it out:

```bash
curl http://192.168.2.200
```

> Output

```

```

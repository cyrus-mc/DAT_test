3) Kubernetes

```sh
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  labels:
    chart: sample-chart
  name: java-api
  namespace: default
spec:
  replicas: 1
  template:
    metadata:
      labels:
        chart: sample-chart
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: stateless
                operator: In
                values:
                - 'true'
              - key: namespace
                operator: In
                values:
                  - default
      containers:
      - env:
        - name: NAMESPACE
          valueFrom:
            fieldRef:
              fieldPath: metadata.namespace
        - name: JAVA_XMX
          value: 4096m
        - name: JAVA_OPTS
          value: ""
        image: samplecontainer:latest
        name: java-api
        ports:
        - containerPort: 8080
          name: http
          protocol: TCP
        resources:
          limits:
            cpu: 2
            memory: 4Gi
          requests:
        volumeMounts:
        - name: config
          mountPath: /config
      imagePullSecrets:
      - name: registry-pull-secret
      volumes:
      - name: config
        configMap:
          name: configuration
```

_You have the above Kubernetes deployment. At a high level describe what the above accomplishes._

1. This is a deployment named: java-api
2. There is one replicated pod
3. The node selection is based on a matched expression of: stateless with a default namespace
4. Container ports 8080 are open to send and accept traffic
5. The container specs set the name, memory, ports, cpu, mounts such as `/config`
6. The node may not be reachable because of a networking issue (route to node). 

**4 Reasons the config causes a PENDING state**

1. The deployment is invalid: `---> spec.template.spec.containers.0.resources.requests: Invalid type. Expected: object, given: null`
2. Resources requested could be greater than the amount of CPU and Memory remaining on the node. 
3. The namespace you want to use may not be registered with your AWS/cloud provider account or the namespace is in an invalid format. 
4. Using the beta api version could cause a pending state issue. 

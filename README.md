test-self-hosted-runner

## Test deployment of Linux based Self hosted Runners

```
helm install actions-runner-controller \
  actions-runner-controller/actions-runner-controller \
  --namespace actions \
  --version 0.22.0 \
  -f runner-controller.yaml
```

runner-controller.yml
```
replicaCount: 1
webhookPort: 9443
syncPeriod: 1m
defaultScaleDownDelay: 5m
enableLeaderElection: true

authSecret:
  enabled: true
  create: false
  name: "controller-manager"

image:
  repository: "summerwind/actions-runner-controller"
  actionsRunnerRepositoryAndTag: "summerwind/actions-runner:ubuntu-20.04"
  dindSidecarRepositoryAndTag: "docker:dind"
  pullPolicy: IfNotPresent

serviceAccount:
  create: true

service:
  type: ClusterIP
  port: 443

certManagerEnabled: true

logFormat: text

githubWebhookServer:
  enabled: false
```

vim runner-deployment.yml
```
apiVersion: actions.summerwind.dev/v1alpha1
kind: RunnerDeployment
metadata:
  annotations:
    karpenter.sh/do-not-evict: "true"
  name: self-hosted-runner
  namespace: actions
spec:
  template:
    spec:
      labels:
        - self-hosted-linux
      image: summerwind/actions-runner:ubuntu-20.04
      organization: Jwwangari6
      resources:
        requests:
          cpu: 1500m
          memory: 2000Mi
```

After successfully installing ARC on the cluster use this repo to run an github action to test the installation.

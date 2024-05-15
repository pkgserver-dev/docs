# Registering a catalog/blueprint repository

First a git repository must be created. We use the pkgserver on github repo one in this tutorial. Given this will be used as a catalog repo we set the deployment attribute to false. Also note that we do not provide any secret, which means that this repository can not be edited by the pkgserver. Basically given this is a public github repository the pkgserver can only read the resources and its content of the packages.

```yaml
kubectl apply -f - <<EOF
apiVersion: config.pkg.pkgserver.dev/v1alpha1
kind: Repository
metadata:
  name: repo-catalog
spec:
  type: git
  deployment: false
  git:
    url: https://github.com/pkgserver-dev/pkgserver-demo-catalog.git
EOF
```

By executing the following command you should see the status of the repository. The repository has a READY condition indicating the success or failure of the reconcilation status of the repository resource. E.g if the repository is not accessible a false READY state will be indicated


when executing the following command

```
kubectl get repositories.config.pkg.pkgserver.dev repo-catalog 

```

the following output is expected

```
NAME           READY   DEPLOYMENT   TYPE   ADDRESS
repo-catalog   True                 git    https://github.com/pkgserver-dev/pkgserver-demo-catalog.git
```


For non deployment repositories the packageserver tries to discover existing packages in the repository and they will appear as packagerevisions through the following command.

e.g when executing the following command we should see various packages.

```
kubectl get packagerevisions.pkg.pkgserver.dev --field-selector=spec.packageID.repository=repo-catalog
```

Expected output

```
NAME                                 REPOSITORY     PACKAGE   REVISION   WORKSPACE   LIFECYCLE
catalog.repo-catalog.nf.example.v1   repo-catalog   example   v1         v1          published
catalog.repo-catalog.nf.example.v2   repo-catalog   example   v2         v2          published
catalog.repo-catalog.nf.example.v3   repo-catalog   example   v3         v3          published
```

The content of a specific package revision can be viewed using the following command.

```
kubectl describe packagerevisionresourceses.pkg.pkgserver.dev catalog.repo-catalog.nf.example.v3 
```

```yaml
Name:         catalog.repo-catalog.nf.example.v3
Namespace:    default
Labels:       <none>
Annotations:  pkg.pkgserver.dev/DiscoveredPkgRev: true
API Version:  pkg.pkgserver.dev/v1alpha1
Kind:         PackageRevisionResources
Metadata:
  Creation Timestamp:  2024-05-14T18:39:43Z
  Finalizers:
    packagediscovery.pkg.pkgserver.dev/finalizer
    packagerevision.pkg.pkgserver.dev/finalizer
  Resource Version:  223532
  UID:               5abb5230-172d-4d00-b4e0-ebff6c7f85cd
Spec:
  Package ID:
    Package:     example
    Realm:       nf
    Repository:  repo-catalog
    Revision:    v3
    Target:      catalog
    Workspace:   v3
  Resources:
    artifacts.yaml:  ---
# blockType: input, resourceID: context
# defines the input of the Kform package as a configmap KRM resource
apiVersion: v1
kind: ConfigMap
metadata:
  name: example
  namespace: default
  annotations:
    kform.dev/block-type: input
    kform.dev/resource-id: context 
    kform.dev/default: "true"
data:
  networkInstance: vpc-kform
  network: knet1
  node: a
  ep: kep1


---
# blockType: provider, resourceID/name: kubernetes
# defines the kubernetes provider configuration
apiVersion: kubernetes.provider.kform.dev/v1alpha1
kind: ProviderConfig
metadata:
  name: kubernetes
  namespace: default
  annotations:
    kform.dev/block-type: provider
#spec: 
#  configPath: "/Users/henderiw/.kube/config"


---
# blockType: resource, resourceID: deployment, resourceType: kubernetes_manifest
# deployment KRM resource that will be applied in the system
# expressions are used to transform the KRM manifest before applying to the system
apiVersion: ipam.be.kuid.dev/v1alpha1
kind: IPClaim
metadata:
  name: vpc-kform.claim1
  namespace: default
  annotations:
    kform.dev/block-type: resource
    kform.dev/resource-type: kubernetes_manifest 
    kform.dev/resource-id: ipclaim
spec:
  index: input.context[0].data.networkInstance
  selector:
    matchLabels:
      inv.kuid.dev/network: input.context[0].data.network
  labels:
    inv.kuid.dev/endpoint-name: input.context[0].data.ep

---
# blockType: output
apiVersion: v1
kind: ConfigMap
metadata:
  name: output
  namespace: default
data:
  prefix: kubernetes_manifest.ipclaim[0].status.address
  defaultGateway: kubernetes_manifest.ipclaim[0].status.defaultGateway

Status:
```
# Package discovery and introspection

You can introspect packages in various ways from listing to checking the content. Here is how you can look at all the package revisions in your system.

/// tab | kubectl

```
kubectl get packagerevisions
```

You should see an output if you have packages created or discovered in a catalog repository.

```
NAME                                 REPOSITORY     TARGET    REALM   PACKAGE   REVISION   WORKSPACE   LIFECYCLE
catalog.repo-catalog.nf.example.v1   repo-catalog   catalog   nf      example   v1         v1          published
catalog.repo-catalog.nf.example.v2   repo-catalog   catalog   nf      example   v2         v2          published
catalog.repo-catalog.nf.example.v3   repo-catalog   catalog   nf      example   v3         v3          published
```
///

/// tab | pkgctl

```
pkgctl rpkg get
```

You should see an output if you have packages created or discovered in a catalog repository.

```
NAME                                 REPOSITORY     TARGET    REALM   PACKAGE   REVISION   WORKSPACE   LIFECYCLE
catalog.repo-catalog.nf.example.v1   repo-catalog   catalog   nf      example   v1         v1          published
catalog.repo-catalog.nf.example.v2   repo-catalog   catalog   nf      example   v2         v2          published
catalog.repo-catalog.nf.example.v3   repo-catalog   catalog   nf      example   v3         v3          published
```

///

The putput show an overview of the various package revisions with its [identifiers](../02-concepts/03_package.md#identification) and [lifecycle](../02-concepts/03_package.md#lifecycle).

if you are interested in a specfic package revision, you can filter using the field-selector or label-selector. Both are standard kubectl primitives we leverage also here. E.g showing all packages of a specific revision. You can filter based on all the respective [identifiers](../02-concepts/03_package.md#identification) of a package revision. These filters can be combined to perform more sophisticated queries. E.g. The below command show all packages with revision identifier equal to v3.

/// tab | kubectl

```
kubectl get packagerevision --field-selector=spec.packageID.revision=v3
```

```
NAME                                 REPOSITORY     TARGET    REALM   PACKAGE   REVISION   WORKSPACE   LIFECYCLE
catalog.repo-catalog.nf.example.v3   repo-catalog   catalog   nf      example   v3         v3          published
```

///

/// tab | pkgctl

```
pkgctl rpkg get --revision v3
```

```
NAME                                 REPOSITORY     TARGET    REALM   PACKAGE   REVISION   WORKSPACE   LIFECYCLE
catalog.repo-catalog.nf.example.v3   repo-catalog   catalog   nf      example   v3         v3          published
```

///


If you are interested to show the content of a package revision, this is possible using the following command. The packageRevisionResources is designed to access the resource of a package.

/// tab | kubectl

```
kubectl get packagerevisionresources
```

```
NAME                                 REPOSITORY     REALM     PACKAGE   PACKAGE   REVISION   WORKSPACE   FILES
catalog.repo-catalog.nf.example.v1   repo-catalog   catalog   nf        example   v1         v1          1
catalog.repo-catalog.nf.example.v2   repo-catalog   catalog   nf        example   v2         v2          1
catalog.repo-catalog.nf.example.v3   repo-catalog   catalog   nf        example   v3         v3          1
```

///

/// tab | pkgctl

This is not supported in pkgctl at this stage

///

Similar to the package revision you can also see how many files are contained in each package revision. If you want to see that content of a specific package revision you can just identify the package revision and provide -o yaml.

/// tab | kubectl

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
Events:  <none>
```

///

/// tab | pkgctl

if you dont supply a directory in the command the command will be shown to the screen, wheras if a directory is supplied the files in the package will be stored relative to the directory supplied.

Example command to store the files in the filesystem

```
pkgctl rpkg pull catalog.repo-catalog.nf.example.v3  example
```

```
tree example    
example
└── artifacts.yaml
```

Example command to show the output to the screen

```
pkgctl rpkg pull catalog.repo-catalog.nf.example.v3 
```

```yaml
path: artifacts.yaml
---
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
```

///
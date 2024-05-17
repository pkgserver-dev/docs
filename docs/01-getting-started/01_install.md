
# Getting Started

First check the [prerequisites](./02_prereq.md)

create a k8s cluster

install pkgserver

```
kubectl apply -f https://github.com/pkgserver-dev/pkgserver/blob/main/artifacts/out/artifact.yaml
```

verify if pkgserver is running

```
kubectl get pods -n pkg-system 
```

```
NAME                          READY   STATUS    RESTARTS   AGE
pkg-server-85fc8896f9-xmrmt   1/1     Running   0          3h
```

```
kubectl api-resources| grep package
packagevariants                                config.pkg.pkgserver.dev/v1alpha1      true         PackageVariant
packagerevisionresourceses                     pkg.pkgserver.dev/v1alpha1             true         PackageRevisionResources
packagerevisions                               pkg.pkgserver.dev/v1alpha1             true         PackageRevision
```

register a catalog repository

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

```
kubectl get repositories.config.pkg.pkgserver.dev repo-catalog 

```

the following output is expected

```
NAME           READY   DEPLOYMENT   TYPE   ADDRESS
repo-catalog   True                 git    https://github.com/pkgserver-dev/pkgserver-demo-catalog.git
```

you should see the following packages appear since the pkgserver discover packages automatically for catalog repositories

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
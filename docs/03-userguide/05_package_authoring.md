# Package authoring

There is various ways to author a package

## Creating a new package revision

This creates a new package from scratch

/// tab | kubectl

```yaml
kubectl apply -f - <<EOF
apiVersion: pkg.pkgserver.dev/v1alpha1
kind: PackageRevision
metadata:
  name: dummy.repo-target.nf.example.ws1
  namespace: default
spec:
  packageID:
    target: dummy
    repository: repo-target
    realm: nf
    package: example
    workspace: ws1
  lifecycle: draft
  tasks:
  - type: init 
EOF
```

When the package revision is successfully created the package should be visible using the following command

```
kubectl get packagerevisions.pkg.pkgserver.dev dummy.repo-target.nf.example.ws1
```

```
NAME                               READY   REPOSITORY    TARGET   REALM   PACKAGE   REVISION   WORKSPACE   LIFECYCLE
dummy.repo-target.nf.example.ws1   True    repo-target   dummy    nf      example              ws1         draft
```

///

/// tab | pkgctl

```
pkgctl rpkg create dummy.repo-target.nf.example.ws1
```

When the package revision is successfully created the package should be visible in the following command

```
pkgctl rpkg get dummy.repo-target.nf.example.ws1
```

```
NAME                               READY   REPOSITORY    TARGET   REALM   PACKAGE   REVISION   WORKSPACE   LIFECYCLE
dummy.repo-target.nf.example.ws1   True    repo-target   dummy    nf      example              ws1         draft
```

///

## Cloning a package revision

To clone a package revision into another, you must provide both the target package revision and the upstream package revision, which serves as the source for the content of the new/target package revision.

/// tab | kubectl

```yaml
kubectl apply -f - <<EOF
apiVersion: pkg.pkgserver.dev/v1alpha1
kind: PackageRevision
metadata:
  name: dummy.repo-target.nf.example.ws1
  namespace: default
spec:
  packageID:
    target: dummy
    repository: repo-target
    realm: nf
    package: example
    workspace: ws1
  lifecycle: draft
  tasks:
  - type: clone
  upstream:
    package: example
    realm: nf
    repository: repo-catalog
    revision: v3
EOF
```

When the package revision is successfully created the package should be visible with the following command

```
kubectl get packagerevisions.pkg.pkgserver.dev dummy.repo-target.nf.example.ws1
```

```
NAME                               READY   REPOSITORY    TARGET   REALM   PACKAGE   REVISION   WORKSPACE   LIFECYCLE
dummy.repo-target.nf.example.ws1   True    repo-target   dummy    nf      example              ws1         draft
```

///

/// tab | pkgctl

```
pkgctl rpkg clone dummy.repo-target.nf.example.ws1 catalog.repo-catalog.nf.example.v3
```

When the package revision is successfully created the package should be visible in the following command

```
pkgctl rpkg get dummy.repo-target.nf.example.ws1
```

```
NAME                               READY   REPOSITORY    TARGET   REALM   PACKAGE   REVISION   WORKSPACE   LIFECYCLE
dummy.repo-target.nf.example.ws1   True    repo-target   dummy    nf      example              ws1         draft
```

///

## Updating a package revision

When you want to change the content of an exiting package, the following comamnds should help you

/// tab | kubectl

not supported

///

/// tab | pkgctl

First ensure you have the content local in a directory. E.g. using the following command, which will create the files of the package revision in the local directory supplied (example in this example)

```
pkgctl rpkg pull dummy.repo-target.nf.example.ws1 example
```

After editing the respecitve files, you can upload the new content using the following command

```
pkgctl rpkg push dummy.repo-target.nf.example.ws1 example
```

you can show the result of the change using the following command.

```
pkgctl rpkg pull dummy.repo-target.nf.example.ws1
```

///

## Deleting a package revision

If you want to delete a package revision that is not in published state, the following methods should allow to delete the package revision. If the package revision is in published state you should update to lifecycle to deletionproposed before executing the below commands

/// tab | kubectl

```
kubectl delete packagerevisions dummy.repo-target.nf.example.ws1
```

```
kubectl get packagerevisions dummy.repo-target.nf.example.ws1
Error from server (NotFound): packagerevisions.pkg.pkgserver.dev "dummy.repo-target.nf.example.ws1" not found
```

///

/// tab | pkgctl

```
pkgctl rpkg delete dummy.repo-target.nf.example
```

```
pkgctl rpkg get dummy.repo-target.nf.example
the server could not find the requested resource (get packagerevisions.pkg.pkgserver.dev dummy.repo-target.nf.example)
```

///
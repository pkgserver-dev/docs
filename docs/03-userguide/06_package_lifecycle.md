# Package Lifecycle and approval flow

Authoring of a package revision is executed in the draft lifecycle state. Before a package can be deployed and even cloned a package must be published. The approval flow is the process by which the package revision is advanced from draft state through proposed state and finally to a published stage.


In these example a package revision is available in the draft state

```
kubectl get packagerevisions.pkg.pkgserver.dev dummy.repo-target.nf.example.ws1
NAME                               READY   REPOSITORY    TARGET   REALM   PACKAGE   REVISION   WORKSPACE   LIFECYCLE
dummy.repo-target.nf.example.ws1   True    repo-target   dummy    nf      example              ws1         draft
```

## Propose a package revision (draft -> proposed)

After editing is completed, the package revision will transition to a proposed state, that will trigger an authoratative entity to either reject or approve the package

/// tab | kubectl

kubectl to be updated, check for the pkgct option
///

/// tab | pkgct

```
pkgctl rpkg updatestatus dummy.repo-target.nf.example.ws1 proposed
```

```
pkgctl rpkg get dummy.repo-target.nf.example.ws1
NAME                               READY   REPOSITORY    TARGET   REALM   PACKAGE   REVISION   WORKSPACE   LIFECYCLE
dummy.repo-target.nf.example.ws1   True    repo-target   dummy    nf      example              ws1         proposed
```
///

## Reject a package revision (proposed -> draft)

A package can be rejected using the following command

/// tab | kubectl

kubectl to be updated, check for the pkgct option
///

/// tab | pkgctl

```
pkgctl rpkg updatestatus dummy.repo-target.nf.example.ws1 draft 
```

```
pkgctl rpkg get dummy.repo-target.nf.example.ws1               
NAME                               READY   REPOSITORY    TARGET   REALM   PACKAGE   REVISION   WORKSPACE   LIFECYCLE
dummy.repo-target.nf.example.ws1   True    repo-target   dummy    nf      example              ws1         draft
```
///

## Approve a package revision (proposed -> publish)

A package can be approved using the following command. Also note that a revision is allocated for the package once approved. At this stage the package revision can no longer be edited. When using git at this stage a tag will be allocated reflecting a pointer to this package revision. On top you will also see the content in merged into the main branch from the workspace branch.

/// tab | kubectl

kubectl to be updated, check for the pkgct option
///

/// tab | pkgctl

```
pkgctl rpkg updatestatus dummy.repo-target.nf.example.ws1 published
```

```
NAME                               READY   REPOSITORY    TARGET   REALM   PACKAGE   REVISION   WORKSPACE   LIFECYCLE
dummy.repo-target.nf.example.ws1   True    repo-target   dummy    nf      example   v1         ws1         published
```
///

## Delete an approved package revision (proposed -> deleteionProposed -> deleted)

A package that was approved can be deleted using the folliwng commands

/// tab | kubectl

kubectl to be updated, check for the pkgct option

///

/// tab | pkgctl

```
pkgctl rpkg updatestatus dummy.repo-target.nf.example.ws1 deletionProposed
```

```
pkgctl rpkg get dummy.repo-target.nf.example.ws1                          
NAME                               READY   REPOSITORY    TARGET   REALM   PACKAGE   REVISION   WORKSPACE   LIFECYCLE
dummy.repo-target.nf.example.ws1   True    repo-target   dummy    nf      example   v1         ws1         deletionProposed
```

```
pkgctl rpkg delete dummy.repo-target.nf.example.ws1   
```

```
pkgctl rpkg get dummy.repo-target.nf.example.ws1   
the server could not find the requested resource (get packagerevisions.pkg.pkgserver.dev dummy.repo-target.nf.example.ws1) 
```
///
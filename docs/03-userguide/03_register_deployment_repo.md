# Registering a deployment repository

In addition to setting up a git repository, it is essential to generate an access token. This token authorizes the pkgserver to perform the necessary CRUD operations with the required permissions.

Here is an example how this can be performed. Set the following environment variable

```
GITHUB_USERNAME=<username>
GITHUB_TOKEN=<token>
```

using this command a git-pat secret is created

```
kubectl create secret generic git-pat \
  --from-literal=username=${GITHUB_USERNAME} \
  --from-literal=password=${GITHUB_TOKEN} \
  --type=kubernetes.io/basic-auth
```

With the following command a deployment repository is created that can be used for deployment and has the proper access right to perform the full lifecycle management of packages within this repository

```yaml
kubectl apply -f - <<EOF
apiVersion: config.pkg.pkgserver.dev/v1alpha1
kind: Repository
metadata:
  name: repo-target
spec:
  type: git
  deployment: true
  git:
    url: https://github.com/pkgserver-dev/pkgserver-demo-target.git
    credentials: git-pat
EOF
```

```
k get repositories.config.pkg.pkgserver.dev repo-target 
```

```
NAME          READY   DEPLOYMENT   TYPE   ADDRESS
repo-target   True    true         git    https://github.com/pkgserver-dev/pkgserver-demo-target.git
```

Discovery is NOT performed by the `pkgserver` for deployment repositories. As such packages are not discovered automatically be the `pkgserver`.
---
hide:
  - navigation
---

![Pkgserver logo](assets/logos/Pkgserver-transparent-withname-100x123.png)

[![Discord](https://img.shields.io/discord/860500297297821756?style=flat-square&label=discord&logo=discord&color=00c9ff&labelColor=bec8d2)](https://discord.gg/fH35bmcTU9)

Welcome to `pkgserver`, your solution for streamlined [KRM][KRM] package management in conjunction with [GitOps][GITOPS] systems. `Pkgserver` is designed to empower operators, developers and/or platform engineers to deploy applications and configurations seamlessly in various environments.

With `pkgserver`, you can embrace continuous delivery practices, ensuring swift and reliable rollouts of changes to your systems. Whether you're managing infrastructure, deploying application in development or production settings, `pkgserver` provides the tools you need to streamline and automate the deployment process.

The `pkgserver` is tailored to meet the needs of modern infrastructure/application management, offering robust features for managing fleet systems at scale. From orchestrating deployments to tracking changes across your environment, `pkgserver` aims to simplify the complexities of `package` management, allowing you to focus on delivering value to your users.

## Architecture

As the name suggests, the `pkgserver` manages `packages`. Fundamentally, a `package` is a set of Kubernetes Resource Model ([KRM][KRM]) resources in [YAML][YAML] format. These resources may include artifacts of software components, configuration artifacts, or a combination thereof. By leveraging [KRM][KRM], `pkgserver` provides a flexible and extensible framework for defining and managing complex software systems, through a well defined API framework.

To facilitate the organization of `packages`, the `pkgserver` leverages `repositories` to manage `packages`. Within this framework, `pkgserver` distinguishes between two primary types of `repositories`: deployment/catalog `repositories`.

Catalog `repositories`, also known as blueprint `repositories`, serve as templates for software components and configurations.`Packages` in these `repositories` define the structure and composition of the desired [KRM][KRM] resources, providing a standardized framework for deployment.

On the other hand, deployment `repositories` holds `packages` with actual artifacts necessary for deployment, including software components and configuration artifacts. These `packages` contain the tangible elements required to instantiate the deployment artifacts.

```mermaid
graph TD;
    subgraph "Package Organization"
        RepositoryC[Repository Catalog];
        RepositoryD[Repository Deployment];
        PackageC[Package Catalog];
        PackageD[Package Deployment];
        RepositoryC --> |1:N| PackageC;
        RepositoryD --> |1:N| PackageD;
    end
```

Central to the functionality of `pkgserver` is its robust lifecycle management system for packages. This system encompasses essential operations such as discovery, creation, reading, updating, and deletion of `packages`, commonly referred to as CRUD operations.

Additionally, `pkgserver` offers comprehensive versioning capabilities, allowing users to track and manage changes to packages over time. This ensures traceability and facilitates the implementation of controlled release processes.

`Pkgserver` extends the Kubernetes API with dedicated resources (`PackageRevision`/`PackageRevisonResources`), ensuring a tailored approach to managing `packages` within Kubernetes environments. Through these API extension the, `Pkgserver` architecture allows for seamless integration with different applications leveraging the event-driven API framework of Kubernetes. This capability empowers users to customize specific scenarios and workflows tailored to their unique requirements. Examples include `Package Specialization`, `Package Approval`, `Package Validation`, `Package Dependencies`, etc.

```mermaid
graph TD;
    subgraph "PkgServer Architecture"
        PkgServerAPI[API Server];
        PkgRevisionController;
        PkgServerApp1[Custom Application 1];
        PkgServerApp2[Custom Application 2];
        PkgServerDB[Version Controlled Backend];
        PkgServerAPI --> PkgServerDB;
        PkgRevisionController --> PkgServerAPI;
        PkgServerApp1 --> PkgServerAPI;
        PkgServerApp2 --> PkgServerAPI;
    end
```


## Join us

Join us on this journey as we redefine package management and deployment for the next generation of software development and infrastructure/application engineering.

Have questions, ideas, bug reports or just want to chat? Come join [our discord server](https://discord.gg/Z9BJeHfBpD).

<script type="text/javascript" src="https://viewer.diagrams.net/js/viewer-static.min.js" async></script>

[KRM]: https://github.com/kubernetes/design-proposals-archive/blob/main/architecture/resource-management.md
[GITOPS]: https://opengitops.dev
[YAML]: https://en.wikipedia.org/wiki/YAML
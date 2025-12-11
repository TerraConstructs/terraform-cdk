# The Future of Terraform CDK

> [!IMPORTANT]
>
> [OCF](https://the-ocf.org/) - [github.com/open-constructs](https://github.com/open-constructs) has stepped up to coordinate effort towards maintaining a fork - this is proof of concept build pipelines

Q: Will CDKTF continue to support new versions of Terraform or providers?

A: To use new versions of Terraform and provider features, use this fork: `tcons` and `tcons-cli` packages. These packages will continue to be updated to support new versions of Terraform and providers.

Q: Is CDKTF still being developed?

A: HashiCorp, an IBM Company, will no longer maintain or develop the project after December 10, 2025. This is a maintained fork of the original CDKTF project.

---

![](https://github.com/terraconstructs/terraform-cdk/workflows/Release/badge.svg)
[![npm version](https://badge.fury.io/js/tcons.svg)](https://badge.fury.io/js/tcons)

<!-- TODO: Add back these badges when packages are published
[![PyPI version](https://badge.fury.io/py/tcons.svg)](https://badge.fury.io/py/tcons)
[![NuGet version](https://badge.fury.io/nu/TerraConstructs.Tcons.svg)](https://badge.fury.io/nu/Terraconstructs.Tcons)
[![Maven Central](https://img.shields.io/maven-central/v/dev.terraconstructs/tcons?color=brightgreen)](https://search.maven.org/artifact/dev.terraconstructs/tcons)
-->

# CDK for Terraform

Cloud Development Kit for Terraform (CDKTF) allows you to use familiar
programming languages to define cloud infrastructure and provision it through
HashiCorp Terraform. This gives you access to the entire Terraform ecosystem without learning HashiCorp Configuration Language (HCL) and lets you leverage the power of your existing toolchain for testing, dependency management, etc.

We currently support TypeScript, Python, Java, C#, and Go.

![terraform platform](./docs/terraform-platform.png)

This fork of CDKTF includes two packages:

- [tcons-cli](./packages/tcons-cli) - A CLI that allows users to run commands to initialize, import, and synthesize CDK for Terraform applications.
- [tcons](./packages/tcons) - A library for defining Terraform resources using programming constructs.

## Get Started

Choose a language:

<!-- TODO: Migrate cdktf docs to terraconstructs.dev -->

- [TypeScript](https://developer.hashicorp.com/terraform/tutorials/cdktf/cdktf-build?in=terraform%2Fcdktf&variants=cdk-language%3Atypescript)
- [Python](https://developer.hashicorp.com/terraform/tutorials/cdktf/cdktf-build?in=terraform%2Fcdktf&variants=cdk-language%3Apython)
- [Java](https://developer.hashicorp.com/terraform/tutorials/cdktf/cdktf-build?in=terraform%2Fcdktf&variants=cdk-language%3Ajava)
- [C#](https://developer.hashicorp.com/terraform/tutorials/cdktf/cdktf-build?in=terraform%2Fcdktf&variants=cdk-language%3Acsharp)
- [Go](https://developer.hashicorp.com/terraform/tutorials/cdktf/cdktf-build?in=terraform%2Fcdktf&variants=cdk-language%3Ago)

> **Hands-on:** Try the tutorials in the [CDK for Terraform](https://learn.hashicorp.com/collections/terraform/cdktf) collection on HashiCorp Learn.

## Documentation

Refer to the [CDKTF documentation](https://developer.hashicorp.com/terraform/cdktf) for more detail about how to build and manage CDKTF applications, including:

- [Application Architecture](https://developer.hashicorp.com/terraform/cdktf/concepts/cdktf-architecture): Learn the tools and processes that CDKTF uses to leverage the Terraform ecosystem and convert code into Terraform configuration files. It also explains the major components of a CDKTF application and how those pieces fit together.

- [Project Setup](https://developer.hashicorp.com/terraform/cdktf/create-and-deploy/project-setup): Learn how to create a new CDKTF project from a pre-built or custom template. Also learn how to convert an existing HCL project into a CDKTF application.

- [Unit Tests](https://developer.hashicorp.com/terraform/cdktf/test/unit-tests): Learn how to test your application in Typescript with jest.

- [Examples](https://developer.hashicorp.com/terraform/cdktf/examples-and-guides/examples): Reference example projects in every supported language and review explanatory videos and other resources.

## Community

The development team would love your feedback to help guide the project.

- Contribute using the [CONTRIBUTING.md](./CONTRIBUTING.md) guide.
- Ask a question on the [cdk.dev](https://cdk.dev/) community.
- Report a [bug](https://github.com/terraconstructs/terraform-cdk/issues/new?assignees=&labels=bug&template=bug-report.md&title=) or request a new [feature](https://github.com/terraconstructs/terraform-cdk/issues/new?assignees=&labels=enhancement&template=feature-request.md&title=).
- Browse all [open issues](https://github.com/terraconstructs/terraform-cdk/issues).

## Build

For prerequisites, refer to the [following](./CONTRIBUTING.md#prerequisites).

Clone the project repository.

```bash
git clone https://github.com/terraconstructs/terraform-cdk.git
```

Download dependencies.

```bash
cd terraform-cdk/
yarn install
```

Build the project and packages.

```bash
yarn build
```

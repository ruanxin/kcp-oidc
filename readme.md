This GitHub action is to support the scenario which allow workflow to push image to a docker registry deployed in Gardener cluster.

## Prerequisites

- Gardener cluster with OIDC enabled.
- [Serverless](https://github.com/kyma-project/serverless) installed in the Gardener cluster, to provide a Docker registry.
- Ingress controller installed to support external access to the Docker registry.
  - This demo relies on the Gardener default NGINX Ingress addon to simplify the Ingress creation process, but you can use any Ingress controller.
- `OpenIDConnect` and RBAC resources (under [pre-install](pre-install)) configured to provide GitHub action access to the Gardener cluster with the least privileges.

## Scenario

1. Getting the OIDC access token from the cloud provider, check [here](https://github.com/ruanxin/kcp-oidc/blob/main/.github/workflows/kcp-pipeline.yaml#L14-L28)
2. Create an Ingress to expose the Docker registry, check [here](https://github.com/ruanxin/kcp-oidc/blob/main/.github/workflows/kcp-pipeline.yaml#L47-L49)
3. Fetching the Docker registry credentials which persisted in the cluster as secret, check [here](https://github.com/ruanxin/kcp-oidc/blob/main/.github/workflows/kcp-pipeline.yaml#L53-L54)
    - The secret is created by the Serverless module, using the `serverless-registry-config-default` name under the `kyma-system` namespace.
4. Use Kyma CLI to create [template-operator](https://github.com/kyma-project/template-operator) module and push the OCI image to the Docker registry, check [here](https://github.com/ruanxin/kcp-oidc/blob/main/.github/workflows/kcp-pipeline.yaml#L58)
5. Delete Ingress to remove the Docker registry exposure, check [here](https://github.com/ruanxin/kcp-oidc/blob/main/.github/workflows/kcp-pipeline.yaml#L59-L61)

## Benefits
- **Streamlined Process**: 
  - Simplifies the CI/CD pipeline by automating image pushes to the registry.
- **Security**: 
  - Utilizes OIDC for secure authentication to the Gardener cluster.
  - By creating Ingress to expose the Docker registry only when push image required, reduce the change for data breach through an exposed registry.
  - Ensures the Least Privilege for GitHub actions.

# Getting started
This is a guide to help you get started with the project. It assumes that you have already [installed the project](installation.md).

## Setup Open-ID Connect (OIDC) Authentication
The project uses Open-ID Connect (OIDC) for authentication. While this project does come with local authentication, it is recommended to use OIDC for production deployments. To setup OIDC, you need to create an OIDC client in your OIDC provider (e.g. Keycloak). The client should have the following settings:

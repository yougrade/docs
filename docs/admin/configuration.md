# Configuration
## Hub
### Environment Variables
| Name                            | Description                                                                                                                                                                                                                                       |
| :------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `DATABASE_URL`                  | The URL of the database to connect to.                                                                                                                                                                                                            |
| `NEXT_PUBLIC_BASE_URL`          | The base URL of the Hub used for CORS.                                                                                                                                                                                                            |
| `NEXTAUTH_URL`                  | The base URL used by NextAuth. In almost all cases, this should be the same as `NEXT_PUBLIC_BASE_URL`.                                                                                                                                            |
| `NEXT_PUBLIC_INTERNAL_BASE_URL` | The base URL of the Hub used for internal requests. This is the URL passed to the Operator for internal requests. If you have an internal network, you can enter the internal IP address here to avoid unnecessary traffic on the public network. |
| `OIDC_PROVIDER_NAME`            | The name of the OIDC provider.                                                                                                                                                                                                                    |
| `OIDC_ISSUER`                   | The issuer URL of the OIDC provider.                                                                                                                                                                                                              |
| `OIDC_AUTHORIZATION_URL`        | The authorization URL of the OIDC provider.                                                                                                                                                                                                       |
| `OIDC_TOKEN_URL`                | The token URL of the OIDC provider.                                                                                                                                                                                                               |
| `OIDC_USER_INFO_URL`            | The user info URL of the OIDC provider.                                                                                                                                                                                                           |
| `OIDC_CLIENT_ID`                | The client ID to use for the OIDC client.                                                                                                                                                                                                         |
| `OIDC_CLIENT_SECRET`            | The client secret of the OIDC client.                                                                                                                                                                                                             |
| `OIDC_CALLBACK_URL`             | The callback URL to submit to the OIDC provider. It should be the same as `NEXT_PUBLIC_BASE_URL` with `/api/auth/callback` appended, and should not have a trailing slash.                                                                        |
| `OIDC_SCOPE`                    | The scope to request from the OIDC provider. Adjust these for OIDC group mappings, if desired.                                                                                                                                                    |
| `NEXTAUTH_SECRET`               | The secret used by NextAuth. This should be a random string.                                                                                                                                                                                      |
| `OPERATOR_URL`                  | The internal URL of the Operator to connect to. This is used for the websockets connection.                                                                                                                                                       |
| `OPERATOR_API_TOKEN`            | The API token used to authenticate with the Operator.                                                                                                                                                                                             |


## Operator
| Name                    | Description                                                                                                                                                                                                                                                                      |
| :---------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `NODE_ENV`              | The environment to run the Operator in. For development, use `development`. For production, use `production`.                                                                                                                                                                    |
| `OPERATOR_URL`          | The URL of the Operator used for CORS.                                                                                                                                                                                                                                           |
| `INTERNAL_OPERATOR_URL` | The internal URL of the Operator used for internal requests. This is the URL passed to the grading containers. It is recommended to configure the network in a way, where the operator can be reached by the grading containers using this URL, but not from the public network. |
| `MEMORY_LIMIT`          | The total memory limit for all grading containers combined.                                                                                                                                                                                                                      |
| `MAX_CPU_CORES`         | The maximum number of CPU cores that can be used by all grading containers combined.                                                                                                                                                                                             |
| `DATABASE_URL`          | The URL of the database to connect to.                                                                                                                                                                                                                                           |
| `HUB_TOKEN`             | The token that the Hub uses to authenticate with the Operator. Should be the same as the `OPERATOR_API_Token` in the Hub.                                                                                                                                                        |

???+ tip
    Both the `HUB_TOKEN` and the `NEXTAUTH_SECRET` should be random strings. You can generate them using the following commands:
    === "pwgen"
        ```bash
        pwgen -sBv 64
        ```
    === "openssl"
        ```bash
        openssl rand -base64 32
        ```
    Note that spaces and special characters might cause issues in some cases. Therefore, it is recommended to avoid them, if possible.
<!-- NODE_ENV=development
OPERATOR_URL=http://localhost:5000/
INTERNAL_OPERATOR_URL=http://172.21.0.1:5000/
MEMORY_LIMIT=40G
MAX_CPU_CORES=20
# prod
DATABASE_URL=postgresql://admin:admin@localhost:5501/yougrade-operator-dev?schema=public
# local prod
# DATABASE_URL=postgresql://admin:stFrRcb7N9wxpwgTwntgH3hjNVtqghCh@localhost:5001/yougrade-operator?schema=public
# dev
# DATABASE_URL=postgresql://admin:stFrRcb7N9wxpwgTwntgH3hjNVtqghCh@157.90.240.102:5001/yougrade-operator?schema=public
HUB_TOKEN=fnwRPmTgHgfjNdkMqrHhgN97k3zVgbXM3kptCMpbcRm4zLsMtTbswcvpCdjzTFVK
RUNNER_NETWORK_NAME=yougrade_operator_runners -->

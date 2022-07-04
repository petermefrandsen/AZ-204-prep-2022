# Azure App Service

HTTP-based service that can run and scale web applications on Windows and Linux-based environments.

Examples on applications allowed on Azure App Services: Web Apps, API Apps, Mobile Apps, Azure Functions.

#### Built-in Autoscale
- Up/down: number of cores or ram
- In/out: number of machine instances running

Free and Shared tiers cannot scale.

Scaling works as following:
- App runs on all VM instances configured
- If multiple apps => they all share same VMs
- If multiple deployment slots => all slots run on same VMs
- Diagnostic logs, backups or WebJobs use the same CPU cycles on the VMs

Isolate Apps when
- App is resource-intensive
- Independant scaling is necessary
- Geographical requirements

#### Authentication and autorization
##### Built-in Authentication
- Integration with a variety of auth capabilities without implementing them yourself
- Built directly into the platform and no language, SDK, security expertise or code is needed
- Multiple auth providers supported; e.g. Azure AD, Facebook, Google, Twitter.

All incoming HTTP requests passes throught it before being handled by the application code and the module handles:
- User authentication
- Validates, stores and refreshes tokens
- Manages the authentication session
- Injects identity into request headers

Flow:
- Without provider SDK (AKA server-directed flow or server flow)
  - Application delegates federated sign-in
  - Typically browser apps
- With provider SDK (AKA client-directed flow or client flow)
  - Application signs users in to the provider manually and submits the authentication token
  - Typically browser-less apps
  - Applies to REST APIs, Azure Functions, JS browser clients and native mobile apps

##### Identity providers
App Service uses federated identity, in which a third-party identify provider manages the user identities and authentication flow for you.

##### Authorization
In Azure you can configure an App Service with a number of behaviours when a user is not authenticated.
- Allow authenticated requests
- Require authentication: reject any unauthenticated traffic and can redirect or return a ``HTTP 401 Unauthorized`` or ``HTTP 403 Forbidden``

#### CI/CD integration/deployment support
Automated deployment
- Azure DevOps
- GitHub
- Bitbucket

Manual deployment
- Git: App Service web app feature a Git URL that when pushed to will deploy the App
- CLI
- Zip deploy: `` curl `` or similar HTTP utility to send a ZIP files to App Service
- FTP/S

#### Deployment slots
When running standard, premium or isolated App Service Plan you can use seperate deployment slots instead of the *default production* slot.

**App content and configuration elements can be swapped** between two deployment slots.

Swap staging and production slots to eliminate downtime.

#### Linux
Limitations running App Service on Linux:
- Linux not supported on Shared pricing tier
- Windows and Linux cannot be mixed on same App Service plan
- Resource groups created before 21-01-2021 do not support Windows and Linux coexisting (to be rolled out)

#### Networking features
*Front ends*: The roles that handle incoming HTTP or HTTPS requests are handled.

*Workers*: The roles that host the customer workloads.

All roles exist in a multi-tenant network.

App Service Networks cannot connect directly to own networks because there are many different customers in the same App Service scale unit.

To overcome this features are needed to handle the various aspects of application communication.

| Inbound Featuers | Outbound Features |
|---|---|
| App-assigned address | Hybrid Connections |
| Access restrictions | Gateway-required virtual network integration |
| Service endpoints | Virtual network integration |
| Private endpoints | |

Examples of how to use App Service networking features to control traffic inbound.
| Inbound use case | Feature |
| --- | --- |
| Support IP-based SSL needs for your app | App-assigned address |
| Support unshared dedicated inbound address for your app | App-assigned address |
| Restrict access to your app from a set of well-defined addresses | Access restrictions |

##### Default networking behaviour
There are different VM types grouped by the following plans:
- Free, Shared, Basic, Standard and Premium
- PremiumV2
- PremiumV3

Scaling out within these categories will result in the outbound addresses to change.

In older units both inbound and outbound addresses change.

The outbound addresses used by App Services are listed in the properties for the app.

#### App Service Plans
A plan defines
- Region
- Number of VM instances
- Size of VM instances
- Pricing tier

Pricing tiers
- Shared Compute
  - Free and Shared share resource pools with other customers
  - Allocate CPU quotas
  - Cannot scale out
- Dedicated compute
  - Basic, Standard, Premium, PremiumV2 & PremiumV3
  - Apps in same plan share these resources
  - The higher pricing tier the more VM instances available for scaling out
- Isolated
  - Dedicated VMs on dedicated VNets
  - Network isolation on top of compute isolation
  - Maximum scale out capabilities
- Consumption
  - Only availbale to function apps
  - Scales dynamically depending on workload

## Commands
Linux - Supported languages and versions
``` Bash
az webapp list-runtimes --os-type linux
```

Azure CLI manual deployment
``` Bash
az webapp up
```

Find outbound IP Addresses
``` Bash
az webapp show \
    --resource-group <group_name> \
    --name <app_name> \ 
    --query outboundIpAddresses \
    --output tsv
```

Find possible outbound IP Addresses
``` Bash
az webapp show \
    --resource-group <group_name> \ 
    --name <app_name> \ 
    --query possibleOutboundIpAddresses \
    --output tsv
```
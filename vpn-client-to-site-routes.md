---

copyright:
  years: 2021
lastupdated: "2021-08-26"

keywords:

subcollection: vpc

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:term: .term}
{:tip: .tip}
{:beta: .beta}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:table: .aria-labeledby="caption"}
{:external: target="_blank" .external}
{:generic: data-hd-programlang="generic”}
{:download: .download}
{:DomainName: data-hd-keyref="DomainName"}
{:ui: .ph data-hd-interface='ui'}
{:cli: .ph data-hd-interface='cli'}
{:api: .ph data-hd-interface='api'}

# Managing VPN routes (Beta)
{: #vpn-client-to-site-routes}

Client VPN for VPC is available to all IBM Cloud users. After the Beta period ends, you will be given a time period to migrate your VPN servers to the standard pricing plan to avoid disruption of service.
{: beta}

Each client-to-site VPN uses a routing table to list destination network routes, where network traffic is directed. To specify which clients have access to the destination network, configure authorization rules for each client-to-site VPN route.

Private IPs are not static and can change at any time. Do not create routes and try to use the private IP addresses.
{: important}
   
## Using split-tunnel on client-to-site VPNs
{: #vpn-client-to-site-split-tunnel}

When the VPN is established in split-tunnel configuration, all routes in the client-to-site VPN server routing tables are added to the client routing table. You must reset the connection if you add routes later, to ensure that the new route is delivered to the client.

To avoid running out of routes, make note of the client device's route capacity before modifying the client-to-site VPN server routing table.
{: tip}

## Creating a route using the UI
{: #create-route-ui}
{: ui}

Specify how destination network traffic is directed by creating a route. To create a route, the VPN client server must have a health status of "Healthy" and a server status of "Stable".

Add `0.0.0.0/0` as a route to enable clients access to the internet.
{: tip}

1. From your browser, open the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com){: external} and log in to your account.
1. Select the Menu icon ![Menu icon](../icons/icon_hamburger.svg) on the upper left of the page, then click **VPC Infrastructure > VPNs** in the Network section.
1. Select **Client-to-site servers** tab.
1. Select the client-to-site server to which you want to add a route, and in the **VPN server details** page, select the **VPN server routes** tab.
1. Select **Create +**.
1. Give the route a name.
1. Specify a destination CIDR range for the destination network. For example:
   * For internet access, enter `0.0.0.0/0`.
   * For an on-premises network, enter the IPv4 CIDR range of the site-to-site gateway connection.
   * For the VPC subnet, enter the VPC subnet CIDR.
1. Choose an action:
   * **Deliver** - Use when the route destination is in the VPC, or an on-premises private subnet connected using VPN gateway.
   * **Drop** - Use when you want to block traffic from the client, to forward unwanted or undesirable network traffic to a null or "black hole" route.
   * **Translate** - Use to translate the source IP to the VPN server private IP before it is sent out from the VPN server, making your VPN client IP invisible to the destination devices.
1. Click **Save**.

You can select **Edit** from the overflow menu of the VPN server route to change the name of your route.
{: note}

## Deleting a route using the UI
{: #delete-route-ui}
{: ui}

To delete a route using the UI, follow these steps:

1. From your browser, open the [{{site.data.keyword.cloud_notm}} console](https://cloud.ibm.com){: external} and log in to your account.
1. Select the Menu icon ![Menu icon](../icons/icon_hamburger.svg) on the upper left of the page, then click **VPC Infrastructure > VPNs** in the Network section.
1. Select **Client-to-site servers** tab.
1. Select the client-to-site server from which you want to delete a route, and in the overflow menu, select **Delete**.
1. Select **Delete** again to confirm deletion.

## Creating a route using the CLI
{: #create-route-cli}
{: cli}

To create a VPN server route by using the CLI, enter the following command:

```
ibmcloud is vpn-server-route-create VPN_SERVER_ID --destination DESTINATION_CIDR [--action translate | deliver | drop] [--name NAME] [--output JSON] [-q, --quiet]
```
{: pre}

Where:

- **VPN_SERVER_ID**: is the ID of the VPN server.
- **--action**: is the action to perform with a packet that matches the route. One of: **translate**, **deliver**, **drop**. (default: **deliver**).
- **--destination**: is the destination to use for this VPN route in the VPN server. Must be unique within the VPN server. If an incoming packet does not match any destination, it will be dropped.
- **--name**: is the name of the VPN route.
- **--output**: specifies output format, only JSON is supported. One of: **JSON**.
- **-q, --quiet**: suppresses verbose output.

For example:

- `ibmcloud is vpn-server-route-create r134-77e21079-7291-44c2-866a-8f1848bc10f0 --name myroute --action deliver --destination 10.0.0.0/24`
{: screen}
- `ibmcloud is vpn-server-route-create r134-77e21079-7291-44c2-866a-8f1848bc10f0 --name myroute --action drop --destination 10.0.0.0/24`
{: screen}

## Updating a route using the CLI
{: #update-route-cli}
{: cli}

To update a VPN server route by using the CLI, enter the following command:

```
ibmcloud is vpn-server-route-update VPN_SERVER_ID ROUTE_ID [--name NAME] [--output JSON] [-q, --quiet]
```
{: pre}

Where:

- **VPN_SERVER_ID**: is the ID of the VPN server.
- **ROUTE_ID**: is the ID of the VPN route.
- **--name**: is the new name of the VPN route.
- **--output**: specifies output format, only JSON is supported. One of: **JSON**.
- **-q, --quiet**: suppresses verbose output.

For example:

`ibmcloud is vpn-server-route-update r134-77e21079-7291-44c2-866a-8f1848bc10f0 1233a60b-fc95-4dbc-96ab-a976b723bfb0 --name myroute`
{: screen}

## Viewing VPN route details using the CLI
{: #vpn-server-route}
{: cli}

To view details of a VPN route, enter the following command:

```
ibmcloud is vpn-server-route VPN_SERVER_ID ROUTE_ID [--output JSON] [-q, --quiet]
```
{: pre}

Where:

- **VPN_SERVER_ID**: is the ID of the VPN server.
- **ROUTE_ID**: is the ID of the VPN route.
- **--output**: specifies output format, only JSON is supported. One of: **JSON**.
- **-q, --quiet**: suppresses verbose output.

## Viewing all routes using the CLI
{: #view-routes-cli}
{: cli}

To view a list of VPN server routes for a vpn server by using the CLI, enter the following command:

```
ibmcloud is vpn-server-routes VPN_SERVER_ID [--resource-group-id RESOURCE_GROUP_ID | --resource-group-name RESOURCE_GROUP_NAME | --all-resource-groups] [--output JSON] [-q, --quiet]
```
{: pre}

Where:

- **VPN_SERVER_ID**: is the ID of the VPN server.
- **--resource-group-id**: is the ID of the resource group. This option is mutually exclusive with **--resource-group-name**.
- **--resource-group-name**: is the name of the resource group. This option is mutually exclusive with **--resource-group-id**.
- **--all-resource-groups**: queries all resource groups.
- **--output**: specifies output format, only JSON is supported. One of: **JSON**.
- **-q, --quiet**: suppresses verbose output.

## Deleting a route using the CLI
{: #delete-route-cli}
{: cli}

To delete a VPN server route by using the CLI, enter the following command:

```
ibmcloud is vpn-server-route-delete VPN_SERVER_ID (ROUTE_ID1 ROUTE_ID2 ...) [--output JSON] [-f, --force] [-q, --quiet]
```
{: pre}

Where:

- **VPN_SERVER_ID**: is the ID of the VPN server.
- **ROUTE_ID1**: is the ID of the VPN route.
- **ROUTE_ID2**: is the ID of the VPN route.
- **--output**: specifies output format, only JSON is supported. One of: **JSON**.
- **--force, -f**: forces the operation without confirmation.
- **-q, --quiet**: suppresses verbose output.

## Creating a route using the API
{: #create-route-api}
{: api}

To create a VPN route on the VPN server by using the API, follow these steps:

1. Set up your [API environment](/docs/vpc?topic=vpc-set-up-environment#api-prerequisites-setup) with
the right variables.
1. Perform a POST to `/vpn_servers/{vpn_server_id}/routes`.

   ```sh
      curl -X POST "$vpc_api_endpoint/v1/vpn_servers/$vpn_server_id/routes?version=$api_version&maturity&generation=2" \
        -H "Authorization: $iam_token" \
        -d '{
           "name":"my-route-1",
            "destination": "10.10.10.0/24",
            "action": "translate"
         }'
   ```
   {: codeblock}

## Updating a route using the API
{: #update-route-api}
{: api}

To update a route on the VPN server by using the API, follow these steps:

1. Set up your [API environment](/docs/vpc?topic=vpc-set-up-environment#api-prerequisites-setup) with
the right variables.
1. Perform a PATCH on `/vpn_servers/{vpn_server_id}/routes/{id}`

   ```sh
      curl -X PUT "$vpc_api_endpoint/v1/vpn_servers/$vpn_server_id/routes/$route_id?version=$api_version&maturity=beta&generation=2" \
        -H "Authorization: $iam_token" \
        -d '{
           "name":"new-route-name"
         }'
   ```
   {: codeblock}

## Viewing a route using the API
{: #view-routes-api}
{: api}

To view a route on a VPN server by using the API, follow these steps:  

1. Set up your [API environment](/docs/vpc?topic=vpc-set-up-environment#api-prerequisites-setup) with
the right variables.
1. When all variables are initiated, list your routes:

   ```sh
   curl -sS -X GET \
   -H "Authorization: $iam_token" \
   "$vpc_api_endpoint/v1/vpn_servers/$vpn_server_id/routes?version=$api_version&maturity=beta&generation=2" | jq
   ```
   {: codeblock}

1. Perform a GET on `/vpn_servers/{vpn_server_id}/routes/{id}`. For details, see [`list_vpn-server_routes`](/apidocs/vpc-beta#list_vpn_server_routes).

   ```sh
   curl -sS -X GET \
   -H "Authorization: $iam_token" \
   "$vpc_api_endpoint/v1/vpn_servers/$vpn_server_id/routes/$route_id?version=$api_version&maturity=beta&generation=2" | jq
   ```
   {: codeblock}

## Deleting a route using the API
{: #delete-route-api}
{: api}

To delete a route on a VPN server by using the API, follow these steps:

1. Set up your [API environment](/docs/vpc?topic=vpc-set-up-environment#api-prerequisites-setup) with
the right variables.

1. Perform a DELETE on `/vpn_servers/{vpn_server_id}/routes/{id}`. 

   ```sh
   curl -sS -X DELETE \
   -H "Authorization: $iam_token" \
   "$vpc_api_endpoint/v1/vpn_servers/$vpn_server_id/routes/$route_id?version=$api_version&maturity=beta&generation=2"
   ```
   {: codeblock}
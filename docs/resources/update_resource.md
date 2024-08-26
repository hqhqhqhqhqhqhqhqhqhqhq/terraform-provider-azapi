---
page_title: "azapi_update_resource Resource - terraform-provider-azapi"
subcategory: ""
description: |-
  This resource can manage a subset of any existing Azure resource manager resource's properties.
  -> Note This resource is used to add or modify properties on an existing resource. When delete azapi_update_resource, no operation will be performed, and these properties will stay unchanged. If you want to restore the modified properties to some values, you must apply the restored properties before deleting.
---

# azapi_update_resource (Resource)

This resource can manage a subset of any existing Azure resource manager resource's properties.

-> **Note** This resource is used to add or modify properties on an existing resource. When delete `azapi_update_resource`, no operation will be performed, and these properties will stay unchanged. If you want to restore the modified properties to some values, you must apply the restored properties before deleting.

## Example Usage

 ```terraform
 terraform {
   required_providers {
     azapi = {
       source = "Azure/azapi"
     }
   }
 }
 
 provider "azapi" {
 }
 
 provider "azurerm" {
   features {}
 }
 
 resource "azurerm_resource_group" "example" {
   name     = "example-rg"
   location = "west europe"
 }
 
 resource "azurerm_public_ip" "example" {
   name                = "example-ip"
   location            = azurerm_resource_group.example.location
   resource_group_name = azurerm_resource_group.example.name
   allocation_method   = "Static"
 }
 
 resource "azurerm_lb" "example" {
   name                = "example-lb"
   location            = azurerm_resource_group.example.location
   resource_group_name = azurerm_resource_group.example.name
 
   frontend_ip_configuration {
     name                 = "PublicIPAddress"
     public_ip_address_id = azurerm_public_ip.example.id
   }
 }
 
 resource "azurerm_lb_nat_rule" "example" {
   resource_group_name            = azurerm_resource_group.example.name
   loadbalancer_id                = azurerm_lb.example.id
   name                           = "RDPAccess"
   protocol                       = "Tcp"
   frontend_port                  = 3389
   backend_port                   = 3389
   frontend_ip_configuration_name = "PublicIPAddress"
 }
 
 resource "azapi_update_resource" "example" {
   type        = "Microsoft.Network/loadBalancers@2021-03-01"
   resource_id = azurerm_lb.example.id
 
   body = {
     properties = {
       inboundNatRules = [
         {
           properties = {
             idleTimeoutInMinutes = 15
           }
         }
       ]
     }
   }
 
   depends_on = [
     azurerm_lb_nat_rule.example,
   ]
 }
 ```

<!-- schema generated by tfplugindocs -->
## Schema

### Required

- `type` (String) In a format like `<resource-type>@<api-version>`. `<resource-type>` is the Azure resource type, for example, `Microsoft.Storage/storageAccounts`. `<api-version>` is version of the API used to manage this azure resource.

### Optional

- `body` (Dynamic) A dynamic attribute that contains the request body.
- `ignore_casing` (Boolean) Whether ignore the casing of the property names in the response body. Defaults to `false`.
- `ignore_missing_property` (Boolean) Whether ignore not returned properties like credentials in `body` to suppress plan-diff. Defaults to `true`. It's recommend to enable this option when some sensitive properties are not returned in response body, instead of setting them in `lifecycle.ignore_changes` because it will make the sensitive fields unable to update.
- `locks` (List of String) A list of ARM resource IDs which are used to avoid create/modify/delete azapi resources at the same time.
- `name` (String) Specifies the name of the Azure resource. Changing this forces a new resource to be created.
- `parent_id` (String) The ID of the azure resource in which this resource is created. It supports different kinds of deployment scope for **top level** resources:

  - resource group scope: `parent_id` should be the ID of a resource group, it's recommended to manage a resource group by azurerm_resource_group.
	- management group scope: `parent_id` should be the ID of a management group, it's recommended to manage a management group by azurerm_management_group.
	- extension scope: `parent_id` should be the ID of the resource you're adding the extension to.
	- subscription scope: `parent_id` should be like \x60/subscriptions/00000000-0000-0000-0000-000000000000\x60
	- tenant scope: `parent_id` should be /

  For child level resources, the `parent_id` should be the ID of its parent resource, for example, subnet resource's `parent_id` is the ID of the vnet.

  For type `Microsoft.Resources/resourceGroups`, the `parent_id` could be omitted, it defaults to subscription ID specified in provider or the default subscription (You could check the default subscription by azure cli command: `az account show`).
- `read_headers` (Map of String) A mapping of headers to be sent with the read request.
- `read_query_parameters` (Map of List of String) A mapping of query parameters to be sent with the read request.
- `resource_id` (String) The ID of an existing Azure source.
- `response_export_values` (List of String) A list of path that needs to be exported from response body. Setting it to `["*"]` will export the full response body. Here's an example. If it sets to `["properties.loginServer", "properties.policies.quarantinePolicy.status"]`, it will set the following HCL object to computed property output.

	```text
	{
		properties = {
			loginServer = "registry1.azurecr.io"
			policies = {
				quarantinePolicy = {
					status = "disabled"
				}
			}
		}
	}
	```
- `retry` (Attributes) The retry block supports the following arguments: (see [below for nested schema](#nestedatt--retry))
- `timeouts` (Block, Optional) (see [below for nested schema](#nestedblock--timeouts))
- `update_headers` (Map of String) A mapping of headers to be sent with the update request.
- `update_query_parameters` (Map of List of String) A mapping of query parameters to be sent with the update request.

### Read-Only

- `id` (String) The ID of the Azure resource.
- `output` (Dynamic) The output HCL object containing the properties specified in `response_export_values`. Here are some examples to use the values.

	```terraform
	// it will output "registry1.azurecr.io"
	output "login_server" {
		value = azapi_update_resource.example.output.properties.loginServer
	}

	// it will output "disabled"
	output "quarantine_policy" {
		value = azapi_update_resource.example.output.properties.policies.quarantinePolicy.status
	}
	```

<a id="nestedatt--retry"></a>
### Nested Schema for `retry`

Required:

- `error_message_regex` (List of String) A list of regular expressions to match against error messages. If any of the regular expressions match, the error is considered retryable.

Optional:

- `interval_seconds` (Number) The base number of seconds to wait between retries. Default is `10`.
- `max_interval_seconds` (Number) The maximum number of seconds to wait between retries. Default is `180`.
- `multiplier` (Number) The multiplier to apply to the interval between retries. Default is `1.5`.
- `randomization_factor` (Number) The randomization factor to apply to the interval between retries. The formula for the randomized interval is: `RetryInterval * (random value in range [1 - RandomizationFactor, 1 + RandomizationFactor])`. Therefore set to zero `0.0` for no randomization. Default is `0.5`.


<a id="nestedblock--timeouts"></a>
### Nested Schema for `timeouts`

Optional:

- `create` (String) A string that can be [parsed as a duration](https://pkg.go.dev/time#ParseDuration) consisting of numbers and unit suffixes, such as "30s" or "2h45m". Valid time units are "s" (seconds), "m" (minutes), "h" (hours).
- `delete` (String) A string that can be [parsed as a duration](https://pkg.go.dev/time#ParseDuration) consisting of numbers and unit suffixes, such as "30s" or "2h45m". Valid time units are "s" (seconds), "m" (minutes), "h" (hours). Setting a timeout for a Delete operation is only applicable if changes are saved into state before the destroy operation occurs.
- `read` (String) A string that can be [parsed as a duration](https://pkg.go.dev/time#ParseDuration) consisting of numbers and unit suffixes, such as "30s" or "2h45m". Valid time units are "s" (seconds), "m" (minutes), "h" (hours). Read operations occur during any refresh or planning operation when refresh is enabled.
- `update` (String) A string that can be [parsed as a duration](https://pkg.go.dev/time#ParseDuration) consisting of numbers and unit suffixes, such as "30s" or "2h45m". Valid time units are "s" (seconds), "m" (minutes), "h" (hours).


---
page_title: "azapi_client_config Data Source - terraform-provider-azapi"
subcategory: ""
description: |-
  
---

# azapi_client_config (Data Source)

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
data "azapi_client_config" "current" {
}
output "subscription_id" {
  value = data.azapi_client_config.current.subscription_id
}
output "tenant_id" {
  value = data.azapi_client_config.current.tenant_id
}
```

<!-- schema generated by tfplugindocs -->
## Schema

### Optional

- `timeouts` (Block, Optional) (see [below for nested schema](#nestedblock--timeouts))

### Read-Only

- `id` (String) The ID of this resource.
- `subscription_id` (String)
- `tenant_id` (String)

<a id="nestedblock--timeouts"></a>
### Nested Schema for `timeouts`

Optional:

- `read` (String) A string that can be [parsed as a duration](https://pkg.go.dev/time#ParseDuration) consisting of numbers and unit suffixes, such as "30s" or "2h45m". Valid time units are "s" (seconds), "m" (minutes), "h" (hours). Read operations occur during any refresh or planning operation when refresh is enabled.

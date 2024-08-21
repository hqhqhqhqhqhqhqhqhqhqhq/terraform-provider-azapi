---
# generated by https://github.com/hashicorp/terraform-plugin-docs
page_title: "build_resource_id function - azapi"
subcategory: ""
description: |-
  Builds a generic resource ID.
---

# function: build_resource_id

This function constructs an Azure resource ID given the parent ID, resource type, and resource name. It is useful for creating resource IDs for top-level and nested resources within a specific scope.

## Example Usage

```terraform
locals {
  parent_id = "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup"
  resource_type = "Microsoft.Network/virtualNetworks"
  name = "myVNet"
}

output "resource_id" {
  value = provider::azapi::build_resource_id(local.parent_id, local.resource_type, local.name)
}
```

## Signature

<!-- signature generated by tfplugindocs -->
```text
build_resource_id(parent_id string, resource_type string, name string) string
```

## Arguments

<!-- arguments generated by tfplugindocs -->
1. `parent_id` (String) The parent ID of the Azure resource.
1. `resource_type` (String) The resource type of the Azure resource.
1. `name` (String) The name of the Azure resource.

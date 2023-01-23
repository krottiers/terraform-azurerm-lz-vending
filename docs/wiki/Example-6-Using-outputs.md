<!-- markdownlint-disable MD041 -->
If you want to deploy additional resources into the subscription that are not supported by this module,
you can do so using hte module outputs.

In this example we deploy a subnet to the created virtual network.

```terraform
# Create a new subscription using the module
# Add a virtual network to the subscription
module "lz_vending" {
 source  = "Azure/lz-vending/azurerm"
  version = "<version>" # change this to your desired version, https://www.terraform.io/language/expressions/version-constraints

  location = "northeurope"

  # subscription variables
  subscription_alias_enabled = true
  subscription_billing_scope = "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/123456"
  subscription_display_name  = "mysub"
  subscription_alias_name    = "mysub"
  subscription_workload      = "Production"

  # virtual network variables
  virtual_network_enabled = true
  virtual_networks = {
    vnet1 = {
      name                    = "spoke"
      address_space           = ["192.168.2.0/24"]
      resource_group_name     = "rg-networking"
    }
  }
}

# Create a subnet in the virtual network created by the module
# Use the module output to get the resource id of the virtual network
resource "azapi_resource" "subnet" {
  type = "Microsoft.Network/virtualNetworks/subnets@2022-07-01"
  name = "subnet1"
  parent_id = module.lz_vending.outputs.virtual_network_resource_ids.vnet1
  body = jsonencode({
    properties = {
      addressPrefix = "192.168.2.0/26"
    }
  })
}

```

Back to [Examples](Examples)

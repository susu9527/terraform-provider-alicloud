---
subcategory: "VPN Gateway"
layout: "alicloud"
page_title: "Alicloud: alicloud_ssl_vpn_server"
sidebar_current: "docs-alicloud-resource-ssl-vpn-server"
description: |-
  Provides a Alicloud SSL VPN server resource.
---

# alicloud_ssl_vpn_server

Provides a SSL VPN server resource. [Refer to details](https://www.alibabacloud.com/help/doc-detail/64960.htm)

-> **NOTE:** Terraform will auto build ssl vpn server while it uses `alicloud_ssl_vpn_server` to build a ssl vpn server resource.

-> **NOTE:** Available since v1.15.0.

## Example Usage

Basic Usage

```terraform
variable "name" {
  default = "terraform-example"
}

data "alicloud_zones" "default" {
  available_resource_creation = "VSwitch"
}
resource "alicloud_vpc" "default" {
  vpc_name   = var.name
  cidr_block = "10.0.0.0/8"
}
resource "alicloud_vswitch" "default" {
  vswitch_name = var.name
  cidr_block   = "10.1.0.0/16"
  vpc_id       = alicloud_vpc.default.id
  zone_id      = data.alicloud_zones.default.zones.0.id
}

resource "alicloud_vpn_gateway" "default" {
  name                 = var.name
  vpc_id               = alicloud_vpc.default.id
  bandwidth            = "10"
  enable_ssl           = true
  description          = var.name
  instance_charge_type = "PrePaid"
  vswitch_id           = alicloud_vswitch.default.id
}

resource "alicloud_ssl_vpn_server" "default" {
  name           = var.name
  vpn_gateway_id = alicloud_vpn_gateway.default.id
  client_ip_pool = cidrsubnet(alicloud_vpc.default.cidr_block, 8, 8)
  local_subnet   = cidrsubnet(alicloud_vpc.default.cidr_block, 8, 8)
  protocol       = "UDP"
  cipher         = "AES-128-CBC"
  port           = "1194"
  compress       = "false"
}
```
## Argument Reference

The following arguments are supported:

* `name` - (Optional) The name of the SSL-VPN server.
* `vpn_gateway_id` - (Required, ForceNew) The ID of the VPN gateway.
* `client_ip_pool` - (Required) The CIDR block from which access addresses are allocated to the virtual network interface card of the client.
* `local_subnet` - (Required) The CIDR block to be accessed by the client through the SSL-VPN connection. It supports to set multi CIDRs by comma join ways, like `10.0.1.0/24,10.0.2.0/24,10.0.3.0/24`.
* `protocol` - (Optional) The protocol used by the SSL-VPN server. Valid value: UDP(default) |TCP
* `cipher` - (Optional) The encryption algorithm that is used in the SSL-VPN connection. Valid values: `AES-128-CBC`,`AES-192-CBC`,`AES-256-CBC`,`none`. Default value: `AES-128-CBC`.
  * `AES-128-CBC` - the AES-128-CBC algorithm.
  * `AES-192-CBC` - the AES-192-CBC algorithm.
  * `AES-256-CBC` - the AES-256-CBC algorithm.
  * `none` - If you select this option, no encryption algorithm is used.
* `port` - (Optional) The port used by the SSL-VPN server. The default value is `1194`.The following ports cannot be used: [22, 2222, 22222, 9000, 9001, 9002, 7505, 80, 443, 53, 68, 123, 4510, 4560, 500, 4500].
* `compress`  - (Optional) Specifies whether to enable data compression. Valid values: `true`,`false`. Default value: `false`
  * `true` - enables data compression.
  * `false` - disables data compression.

## Attributes Reference

The following attributes are exported:

* `id` - The ID of the SSL-VPN server.
* `internet_ip` - The internet IP of the SSL-VPN server.
* `connections` - The number of current connections.
* `max_connections` - The maximum number of connections.

## Import

SSL-VPN server can be imported using the id, e.g.

```shell
$ terraform import alicloud_ssl_vpn_server.example vss-abc123456
```



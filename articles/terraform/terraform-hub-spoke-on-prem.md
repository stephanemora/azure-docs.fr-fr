---
title: Tutoriel - Créer un réseau virtuel local dans Azure avec Terraform
description: Didacticiel montrant comment implémenter un réseau virtuel local dans Azure, qui héberge les ressources locales
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 361f9919fdd406a1fef6bbf2b7512dbc20266a54
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74159222"
---
# <a name="tutorial-create-on-premises-virtual-network-in-azure-using-terraform"></a>Tutoriel : Créer un réseau virtuel local dans Azure avec Terraform

Ce tutoriel montre comment implémenter un réseau local à l’aide d’un réseau virtuel (VNet) Azure. Vous pouvez remplacer un réseau virtuel Azure par votre propre réseau privé virtuel. Pour ce faire, mappez les adresses IP appropriées dans les sous-réseaux.

Les tâches suivantes sont expliquées :

> [!div class="checklist"]
> * Utiliser HCL (HashiCorp Language) pour implémenter un réseau virtuel local dans une topologie hub-and-spoke
> * Utiliser Terraform pour créer des ressources d’appliance réseau hub
> * Utiliser Terraform pour créer une machine virtuelle locale
> * Utiliser Terraform pour créer une passerelle de réseau privé virtuel local

## <a name="prerequisites"></a>Prérequis

1. [Créez une topologie de réseau hybride hub-and-spoke avec Terraform dans Azure](./terraform-hub-spoke-introduction.md).

## <a name="create-the-directory-structure"></a>Créer la structure de répertoire

Pour simuler un réseau local, créez un réseau virtuel Azure. Le réseau virtuel de démonstration prend la place d’un réseau privé local réel. Pour faire de même avec votre réseau local existant, mappez les adresses IP appropriées dans les sous-réseaux.

1. Accédez au [portail Azure](https://portal.azure.com).

1. Ouvrez [Azure Cloud Shell](/azure/cloud-shell/overview). Si vous n’avez pas sélectionné d’environnement précédemment, choisissez **Bash** comme votre environnement.

    ![Invite Cloud Shell](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Déplacez-vous dans le répertoire `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Déplacez-vous dans le nouveau répertoire :

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-on-premises-vnet"></a>Déclarer le réseau virtuel local

Créez le fichier de configuration Terraform qui déclare le réseau virtuel local.

1. Dans Cloud Shell, ouvrez un nouveau fichier nommé `on-prem.tf`.

    ```bash
    code on-prem.tf
    ```

1. Collez le code suivant dans l’éditeur :

    ```hcl
    locals {
      onprem-location       = "SouthCentralUS"
      onprem-resource-group = "onprem-vnet-rg"
      prefix-onprem         = "onprem"
    }

    resource "azurerm_resource_group" "onprem-vnet-rg" {
      name     = local.onprem-resource-group
      location = local.onprem-location
    }

    resource "azurerm_virtual_network" "onprem-vnet" {
      name                = "onprem-vnet"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name
      address_space       = ["192.168.0.0/16"]

      tags {
        environment = local.prefix-onprem
      }
    }

    resource "azurerm_subnet" "onprem-gateway-subnet" {
      name                 = "GatewaySubnet"
      resource_group_name  = azurerm_resource_group.onprem-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.onprem-vnet.name
      address_prefix       = "192.168.255.224/27"
    }

    resource "azurerm_subnet" "onprem-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.onprem-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.onprem-vnet.name
      address_prefix       = "192.168.1.128/25"
    }

    resource "azurerm_public_ip" "onprem-pip" {
        name                         = "${local.prefix-onprem}-pip"
        location            = azurerm_resource_group.onprem-vnet-rg.location
        resource_group_name = azurerm_resource_group.onprem-vnet-rg.name
        allocation_method   = "Dynamic"

        tags {
            environment = local.prefix-onprem
        }
    }

    resource "azurerm_network_interface" "onprem-nic" {
      name                 = "${local.prefix-onprem}-nic"
      location             = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name  = azurerm_resource_group.onprem-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-onprem
        subnet_id                     = azurerm_subnet.onprem-mgmt.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.onprem-pip.id
      }
    }

    # Create Network Security Group and rule
    resource "azurerm_network_security_group" "onprem-nsg" {
        name                = "${local.prefix-onprem}-nsg"
        location            = azurerm_resource_group.onprem-vnet-rg.location
        resource_group_name = azurerm_resource_group.onprem-vnet-rg.name

        security_rule {
            name                       = "SSH"
            priority                   = 1001
            direction                  = "Inbound"
            access                     = "Allow"
            protocol                   = "Tcp"
            source_port_range          = "*"
            destination_port_range     = "22"
          source_address_prefix      = "*"
            destination_address_prefix = "*"
        }

        tags {
            environment = "onprem"
        }
    }

    resource "azurerm_subnet_network_security_group_association" "mgmt-nsg-association" {
      subnet_id                 = azurerm_subnet.onprem-mgmt.id
      network_security_group_id = azurerm_network_security_group.onprem-nsg.id
    }

    resource "azurerm_virtual_machine" "onprem-vm" {
      name                  = "${local.prefix-onprem}-vm"
      location              = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name   = azurerm_resource_group.onprem-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.onprem-nic.id]
      vm_size               = var.vmsize

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-onprem}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = local.prefix-onprem
      }
    }

    resource "azurerm_public_ip" "onprem-vpn-gateway1-pip" {
      name                = "${local.prefix-onprem}-vpn-gateway1-pip"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name

      allocation_method = "Dynamic"
    }

    resource "azurerm_virtual_network_gateway" "onprem-vpn-gateway" {
      name                = "onprem-vpn-gateway1"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name

      type     = "Vpn"
      vpn_type = "RouteBased"

      active_active = false
      enable_bgp    = false
      sku           = "VpnGw1"

      ip_configuration {
        name                          = "vnetGatewayConfig"
        public_ip_address_id          = azurerm_public_ip.onprem-vpn-gateway1-pip.id
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = azurerm_subnet.onprem-gateway-subnet.id
      }
      depends_on = ["azurerm_public_ip.onprem-vpn-gateway1-pip"]

    }
    ```

1. Enregistrez le fichier et quittez l’éditeur.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer un réseau virtuel hub avec Terraform dans Azure](./terraform-hub-spoke-hub-network.md)
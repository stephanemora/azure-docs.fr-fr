---
title: Comment créer une affectation de configuration invité à l’aide de modèles
description: Découvrez comment déployer des configurations sur des machines directement à partir d’Azure Resource Manager.
ms.date: 08/09/2021
ms.topic: how-to
ms.openlocfilehash: aa7938a9421a9e7680af34af475585c4dc1c818a
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773038"
---
# <a name="how-to-create-a-guest-configuration-assignment-using-templates"></a>Comment créer une affectation de configuration invité à l’aide de modèles

La meilleure façon d’[attribuer des packages de configuration invité](../concepts/guest-configuration-assignments.md) à plusieurs machines consiste à utiliser [Azure Policy](./guest-configuration-create-definition.md). Vous pouvez également attribuer des packages de configuration invité à une seule machine.

## <a name="built-in-and-custom-configurations"></a>Configurations intégrées et personnalisées

Pour attribuer un package de configuration invité à une seule machine, modifiez les exemples suivants. Il existe deux scénarios.

- Appliquez une configuration personnalisée à une machine à l’aide d’un lien vers un package que vous avez [publié](./guest-configuration-create-publish.md).
- Appliquez une configuration [intégrée](../samples/built-in-packages.md) à une machine, comme une ligne de base Azure.

## <a name="extending-other-resource-types-such-as-arc-enabled-servers"></a>Extension d’autres types de ressources, tels que les serveurs Arc

Dans chacune des sections suivantes, l’exemple inclut une propriété **type** où le nom commence par `Microsoft.Compute/virtualMachines`. Le fournisseur de ressources de la configuration invité `Microsoft.GuestConfiguration` est une [ressource d’extension](../../../azure-resource-manager/management/extension-resource-types.md) qui doit référencer un type parent.

Pour modifier l’exemple pour d’autres types de ressources, tels que les [serveurs Arc](../../../azure-arc/servers/overview.md), remplacez le type parent par le nom du fournisseur de ressources.
Pour les serveurs Arc, le fournisseur de ressources est `Microsoft.HybridCompute/machines`.

Remplacez les champs « <> » suivants par les valeurs propres à votre environnement :

- **<vm_name>**  : nom de la ressource machine où la configuration sera appliquée
- **<configuration_name>**  : nom de la configuration à appliquer
- **<vm_location>**  : région Azure où l’affectation de configuration invité sera créée
- **<Url_to_Package.zip>**  : pour un package de contenu personnalisé, un lien HTTPS vers le fichier .zip
- **<SHA256_hash_of_package.zip>**  : pour un package de contenu personnalisé, un code de hachage SHA256 du fichier .zip

## <a name="assign-a-configuration-using-an-azure-resource-manager-template"></a>Attribuer une configuration en utilisant un modèle Azure Resource Manager

Vous pouvez déployer un [modèle Azure Resource Manager](../../../azure-resource-manager/templates/deployment-tutorial-local-template.md?tabs=azure-powershell) contenant des ressources d’affectation de configuration invité.

L’exemple suivant attribue une configuration personnalisée.

```json
{
            "apiVersion": "2020-06-25",
            "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
            "name": "<vm_name>/Microsoft.GuestConfiguration/<configuration_name>",
            "location": "<vm_location>",
            "dependsOn": [
                "Microsoft.Compute/virtualMachines/<vm_name>"
            ],
            "properties": {
                "guestConfiguration": {
                    "name": "<configuration_name>",
                    "contentUri": "<Url_to_Package.zip>",
                    "contentHash": "<SHA256_hash_of_package.zip>",
                    "assignmentType": "ApplyAndMonitor"
                }
            }
        }
```

L’exemple suivant attribue la configuration personnalisée `AzureWindowBaseline`.

```json
{
            "apiVersion": "2020-06-25",
            "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
            "name": "<vm_name>/Microsoft.GuestConfiguration/<configuration_name>",
            "location": "<vm_location>",
            "dependsOn": [
                "Microsoft.Compute/virtualMachines/<vm_name>"
            ],
            "properties": {
                "guestConfiguration": {
                    "name": "AzureWindowsBaseline",
                    "version": "1.*",
                    "assignmentType": "ApplyAndMonitor",
                    "configurationParameter": [
                    {
                        "name": "Minimum Password Length;ExpectedValue",
                        "value": "16"
                    },
                    {
                        "name": "Minimum Password Length;RemediateValue",
                        "value": "16"
                    },
                    {
                        "name": "Maximum Password Age;ExpectedValue",
                        "value": "75"
                    },
                    {
                        "name": "Maximum Password Age;RemediateValue",
                        "value": "75"
                    }
                ]
                }
            }
        }
```

## <a name="assign-a-configuration-using-bicep"></a>Attribuer une configuration à l’aide de Bicep

Vous pouvez utiliser [Azure Bicep](../../../azure-resource-manager/bicep/overview.md) pour déployer des affectations de configuration invité.

L’exemple suivant attribue une configuration personnalisée.

```Bicep
resource myVM 'Microsoft.Compute/virtualMachines@2021-03-01' existing = {
  name: '<vm_name>'
}

resource myConfiguration 'Microsoft.GuestConfiguration/guestConfigurationAssignments@2020-06-25' = {
  name: '<configuration_name>'
  scope: myVM
  location: resourceGroup().location
  properties: {
    guestConfiguration: {
      name: '<configuration_name>'
      contentUri: '<Url_to_Package.zip>'
      contentHash: '<SHA256_hash_of_package.zip>'
      version: '1.*'
      assignmentType: 'ApplyAndMonitor'
    }
  }
}
```

L’exemple suivant attribue la configuration personnalisée `AzureWindowBaseline`.

```Bicep
resource myWindowsVM 'Microsoft.Compute/virtualMachines@2021-03-01' existing = {
  name: '<vm_name>'
}

resource AzureWindowsBaseline 'Microsoft.GuestConfiguration/guestConfigurationAssignments@2020-06-25' = {
  name: 'AzureWindowsBaseline'
  scope: myWindowsVM
  location: resourceGroup().location
  properties: {
    guestConfiguration: {
      name: 'AzureWindowsBaseline'
      version: '1.*'
      assignmentType: 'ApplyAndMonitor'
      configurationParameter: [
        {
          name: 'Minimum Password Length;ExpectedValue'
          value: '16'
        }
        {
          name: 'Minimum Password Length;RemediateValue'
          value: '16'
        }
        {
          name: 'Maximum Password Age;ExpectedValue'
          value: '75'
        }
        {
          name: 'Maximum Password Age;RemediateValue'
          value: '75'
        }
      ]
    }
  }
}
```

## <a name="assign-a-configuration-using-terraform"></a>Attribuer une configuration à l’aide de Terraform

Vous pouvez utiliser [Terraform](https://www.terraform.io/) pour [déployer](/azure/developer/terraform/get-started-windows-powershell) des affectations de configuration invité.

> [!IMPORTANT]
> Le fournisseur Terraform [azurerm_policy_virtual_machine_configuration_assignment](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/virtual_machine_configuration_policy_assignment) n’a pas été mis à jour pour prendre en charge la propriété `assignmentType`, et donc seules les configurations qui réalisent des audits sont prises en charge.

L’exemple suivant attribue une configuration personnalisée.

```Terraform
resource "azurerm_virtual_machine_configuration_policy_assignment" "<configuration_name>" {
  name               = "<configuration_name>"
  location           = azurerm_windows_virtual_machine.example.location
  virtual_machine_id = azurerm_windows_virtual_machine.example.id
  configuration {
    name            = "<configuration_name>"
    contentUri      =  '<Url_to_Package.zip>'
    contentHash     =  '<SHA256_hash_of_package.zip>'
    version         = "1.*"
    assignmentType  = "ApplyAndMonitor
  }
}
```

L’exemple suivant attribue la configuration personnalisée `AzureWindowBaseline`.

```Terraform
resource "azurerm_virtual_machine_configuration_policy_assignment" "AzureWindowsBaseline" {
  name               = "AzureWindowsBaseline"
  location           = azurerm_windows_virtual_machine.example.location
  virtual_machine_id = azurerm_windows_virtual_machine.example.id
  configuration {
    name    = "AzureWindowsBaseline"
    version = "1.*"
    parameter {
      name  = "Minimum Password Length;ExpectedValue"
      value = "16"
    }
    parameter {
      name  = "Minimum Password Length;RemediateValue"
      value = "16"
    }
    parameter {
      name  = "Minimum Password Age;ExpectedValue"
      value = "75"
    }
    parameter {
      name  = "Minimum Password Age;RemediateValue"
      value = "75"
    }
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Lisez la [Présentation de la configuration invité](../concepts/guest-configuration.md).
- Configurez un [environnement de développement](../how-to/guest-configuration-create-setup.md) de package de configuration invité personnalisé.
- [Créez un artefact de package](../how-to/guest-configuration-create.md) pour la configuration invité.
- [Testez l’artefact de package](../how-to/guest-configuration-create-test.md) à partir de votre environnement de développement.
- [Publiez l’artefact de package](./guest-configuration-create-publish.md) afin qu’il soit accessible à vos machines.
- Utilisez le module `GuestConfiguration` afin de [créer une définition Azure Policy](../how-to/guest-configuration-create-definition.md) pour une gestion à l’échelle de votre environnement.
- [Attribuez votre définition de stratégie personnalisée](../assign-policy-portal.md) à l’aide du Portail Azure.
- Découvrez comment visualiser les [informations relatives à la conformité pour les attributions de stratégie de la configuration invité](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration).

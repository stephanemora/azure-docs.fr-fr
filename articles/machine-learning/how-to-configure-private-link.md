---
title: Configurer Azure Private Link
titleSuffix: Azure Machine Learning
description: Utilisez Azure Private Link pour accéder en toute sécurité à votre espace de travail Azure Machine Learning à partir d’un réseau virtuel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/13/2020
ms.openlocfilehash: 5428f24ea5ab780c4b51e0af37908077ddc32232
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891368"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Configurer Azure Private Link pour un espace de travail Azure Machine Learning (préversion)

Dans ce document, vous allez apprendre à utiliser Azure Private Link avec votre espace de travail Azure Machine Learning. Cette capacité est actuellement disponible en version préliminaire dans les régions USA Est, USA Ouest 2 et USA Centre Sud. 

Azure Private Link vous permet de vous connecter à votre espace de travail à l’aide d’un point de terminaison privé. Le point de terminaison privé est un ensemble d’adresses IP privées au sein de votre réseau virtuel. Vous pouvez alors limiter l’accès à votre espace de travail pour qu’il ne se fasse que sur les adresses IP privées. Azure Private Link permet de réduire le risque d’exfiltration des données. Pour plus d’informations sur les points de terminaison privés, consultez l’article [Azure Private Link](/azure/private-link/private-link-overview).

> [!IMPORTANT]
> Azure Private Link n’a pas d’effet sur le plan de contrôle Azure (opérations de gestion), comme la suppression de l’espace de travail ou la gestion des ressources de calcul. Par exemple, la création, la mise à jour ou la suppression d’une cible de calcul. Ces opérations sont effectuées sur l’Internet public comme d’habitude.
>
> La préversion des instances de calcul Azure Machine Learning n’est pas prise en charge dans un espace de travail où le service Liaison privée est activé.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Créer un espace de travail qui utilise un point de terminaison privé

Actuellement, nous prenons uniquement en charge l’activation d’un point de terminaison privé lors de la création d’un espace de travail Azure Machine Learning. Les modèles suivants sont fournis pour plusieurs configurations courantes :

> [!TIP]
> L’approbation automatique permet de contrôler l’accès automatisé à la ressource compatible avec Azure Private Link. Pour plus d’informations, consultez [Qu’est-ce que le service Azure Private Link ?](../private-link/private-link-service-overview.md)

* [Espace de travail avec clés gérées par le client et approbation automatique pour Azure Private Link](#cmkaapl)
* [Espace de travail avec clés gérées par le client et approbation manuelle pour Azure Private Link](#cmkmapl)
* [Espace de travail avec clés gérées par Microsoft et approbation automatique pour Azure Private Link](#mmkaapl)
* [Espace de travail avec clés gérées par Microsoft et approbation manuelle pour Azure Private Link](#mmkmapl)

Lors du déploiement d’un modèle, vous devez fournir les informations suivantes :

* Nom de l’espace de travail
* Région Azure dans laquelle créer les ressources
* Édition de l’espace de travail (De base ou Entreprise)
* Si les paramètres de confidentialité élevée de l’espace de travail doivent être activés
* Si le chiffrement de l’espace de travail avec une clé gérée par le client doit être activé, et les valeurs associées pour la clé
* Nom du réseau virtuel et du sous-réseau créés par le modèle

Une fois qu’un modèle a été soumis et que l’approvisionnement est terminé, le groupe de ressources qui contient votre espace de travail contient trois nouveaux types d’artefacts associés à Azure Private Link :

* Point de terminaison privé
* interface réseau
* Zone DNS privée

L’espace de travail contient également une instance Réseau virtuel Microsoft Azure qui peut communiquer avec l’espace de travail sur le point de terminaison privé.

### <a name="deploy-the-template-using-the-azure-portal"></a>Déployer le modèle à l’aide du Portail Azure

1. Suivez la procédure indiquée dans [Déployer des ressources à partir d’un modèle personnalisé](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Lorsque vous arrivez à l’écran __Modifier le modèle__, collez l’un des modèles figurant à la fin de ce document.
1. Sélectionnez __Enregistrer__ pour utiliser le modèle. Fournissez les informations suivantes et acceptez les conditions répertoriées :

   * Abonnement : Sélectionnez l’abonnement Azure à utiliser pour ces ressources.
   * Groupe de ressources : Sélectionnez ou créez un groupe de ressources pour contenir les services.
   * Nom de l’espace de travail : Nom à utiliser pour l’espace de travail Azure Machine Learning qui va être créé. Le nom de l'espace de travail doit contenir entre 3 et 33 caractères. Il ne peut contenir que des caractères alphanumériques et « - ».
   * Localisation : Sélectionnez l’emplacement de création des ressources.

Pour plus d’informations, consultez [Déployer des ressources à partir d’un modèle personnalisé](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

### <a name="deploy-the-template-using-azure-powershell"></a>Déployer le modèle à l’aide d’Azure PowerShell

Cet exemple suppose que vous avez enregistré l’un des modèles figurant à la fin de ce document dans un fichier nommé `azuredeploy.json` dans le répertoire actuel :

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Pour plus d’informations, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) et [Déployer un modèle Resource Manager privé avec un jeton SAP et Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

### <a name="deploy-the-template-using-the-azure-cli"></a>Déployer le modèle à l’aide d’Azure CLI

Cet exemple suppose que vous avez enregistré l’un des modèles figurant à la fin de ce document dans un fichier nommé `azuredeploy.json` dans le répertoire actuel :

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Pour plus d’informations, consultez [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure CLI](../azure-resource-manager/templates/deploy-cli.md) et [Déployer un modèle Resource Manager privé avec un jeton SAP et Azure CLI](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="using-a-workspace-over-a-private-endpoint"></a>Utilisation d’un espace de travail sur un point de terminaison privé

Étant donné que les communications avec l’espace de travail sont autorisées uniquement à partir du réseau virtuel, tous les environnements de développement qui utilisent l’espace de travail doivent être faire partie du réseau virtuel. Par exemple, une machine virtuelle du réseau virtuel ou un ordinateur connecté au réseau virtuel à l’aide d’une passerelle VPN.

> [!IMPORTANT]
> Pour éviter toute interruption temporaire de la connectivité, Microsoft recommande de vider le cache DNS sur les ordinateurs qui se connectent à l’espace de travail après avoir activé Azure Private Link. 

Pour plus d’informations sur Machines virtuelles Microsoft Azure, consultez la [documentation relative à Machines Virtuelles](/azure/virtual-machines/).

Pour plus d’informations sur les passerelles VPN, consultez [Qu’est-ce qu’une passerelle VPN ?](/azure/vpn-gateway/vpn-gateway-about-vpngateways)

## <a name="using-azure-storage"></a>Utilisation de Stockage Azure

Pour sécuriser le compte Stockage Azure utilisé par votre espace de travail, placez-le dans le réseau virtuel.

Pour plus d’informations sur la façon de placer le compte de stockage dans le réseau virtuel, consultez [Utiliser un compte de stockage pour votre espace de travail](how-to-enable-virtual-network.md#use-a-storage-account-for-your-workspace).

## <a name="using-azure-key-vault"></a>Utilisation d’Azure Key Vault

Pour sécuriser l’instance Azure Key Vault utilisée par votre espace de travail, vous pouvez la placer dans le réseau virtuel ou activer Azure Private Link pour elle.

Pour plus d’informations sur la façon de placer le coffre de clés dans le réseau virtuel, consultez [Utiliser un coffre de clés pour votre espace de travail](how-to-enable-virtual-network.md#key-vault-instance).

Pour plus d’informations sur l’activation d’Azure Private Link pour le coffre de clés, consultez [Intégrer Key Vault avec Azure Private Link](/azure/key-vault/private-link-service).

## <a name="using-azure-kubernetes-services"></a>Utilisation d’Azure Kubernetes Services

Pour sécuriser les services Azure Kubernetes utilisés par votre espace de travail, placez-le dans un réseau virtuel. Pour plus d’informations, consultez [Utiliser Azure Kubernetes Services avec votre espace de travail](how-to-enable-virtual-network.md#aksvnet).

> [!WARNING]
> Azure Machine Learning ne prend pas en charge l'utilisation d'une instance d'Azure Kubernetes Service pour laquelle une liaison privée est activée.

## <a name="azure-container-registry"></a>Azure Container Registry

Pour plus d’informations sur la sécurisation d’Azure Container Registry dans le réseau virtuel, consultez [Utilisation d’Azure Container Registry](how-to-enable-virtual-network.md#azure-container-registry).

> [!IMPORTANT]
> Si vous utilisez le service Liaison privée pour votre espace de travail Azure Machine Learning et que vous placez l'instance Azure Container Registry de votre espace de travail dans un réseau virtuel, vous devez également appliquer le modèle Azure Resource Manager suivant. Ce modèle permet à votre espace de travail de communiquer avec ACR via le service Liaison privée.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "keyVaultArmId": {
      "type": "string"
      },
      "workspaceName": {
      "type": "string"
      },
      "containerRegistryArmId": {
      "type": "string"
      },
      "applicationInsightsArmId": {
      "type": "string"
      },
      "storageAccountArmId": {
      "type": "string"
      },
      "location": {
      "type": "string"
      }
  },
  "resources": [
      {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "identity": {
          "type": "SystemAssigned"
      },
      "sku": {
          "tier": "enterprise",
          "name": "enterprise"
      },
      "properties": {
          "sharedPrivateLinkResources":
  [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
          "keyVault": "[parameters('keyVaultArmId')]",
          "containerRegistry": "[parameters('containerRegistryArmId')]",
          "applicationInsights": "[parameters('applicationInsightsArmId')]",
          "storageAccount": "[parameters('storageAccountArmId')]"
      }
      }
  ]
}
```

## <a name="azure-resource-manager-templates"></a>Modèles Microsoft Azure Resource Manager

<a id="cmkaapl"></a>
### <a name="workspace-with-customer-managed-keys-and-auto-approval-for-private-link"></a>Espace de travail avec clés gérées par le client et approbation automatique pour Azure Private Link

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "eastus",
        "southcentralus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku":{
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "hbi_workspace":{
      "type": "string",
      "defaultValue": "false",
      "allowedValues": [
        "false",
        "true"
      ],
      "metadata": {
        "description": "Specifies that the Azure Machine Learning workspace holds highly confidential data."
      }
    },
    "encryption_status":{
      "type": "string",
      "defaultValue": "Disabled",
      "allowedValues": [
        "Enabled",
        "Disabled"
      ],
      "metadata": {
        "description": "Specifies if the Azure Machine Learning workspace should be encrypted with customer managed key."
      }
    },
    "cmk_keyvault":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyVault id."
      }
    },
    "resource_cmk_uri":{
      "type": "string",
      "metadata": {
        "description": "Specifies if the customer managed keyvault key uri."
      }
    },
    "subnetName": {
        "type": "string"
      },
      "vnetName": {
        "type": "string"
      }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
    "privateDnsGuid": "[guid(resourceGroup().id, deployment().name)]"    
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2018-05-01-preview",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2020-01-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]",
         "encryption": {
                "status": "[parameters('encryption_status')]",
                "keyVaultProperties": {
                    "keyVaultArmId": "[parameters('cmk_keyvault')]",
                    "keyIdentifier": "[parameters('resource_cmk_uri')]"
                  }
            },
        "hbi_workspace": "[parameters('hbi_workspace')]"
      }
    },
    {
        "type": "Microsoft.Network/virtualNetworks",
        "apiVersion": "2019-09-01",
        "name": "[parameters('vnetName')]",
        "location": "[parameters('location')]",
        "properties": {
            "addressSpace": {
                "addressPrefixes": [
                    "10.0.0.0/27"
                ]
            },
            "virtualNetworkPeerings": [],
            "enableDdosProtection": false,
            "enableVmProtection": false
        }
      },
    {
        "type": "Microsoft.Network/virtualNetworks/subnets",
        "apiVersion": "2019-09-01",
        "name": "[concat(parameters('vnetName'), '/', parameters('subnetName'))]",
        "dependsOn": [
            "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
        ],
        "properties": {
            "addressPrefix": "10.0.0.0/27",
            "delegations": [],
            "privateEndpointNetworkPolicies": "Disabled",
            "privateLinkServiceNetworkPolicies": "Enabled"
        }
    },
    {
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
      "type": "Microsoft.Network/privateEndpoints",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
      ],
      "properties": {
        "privateLinkServiceConnections": [
          {
            "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
              "groupIds": [
                "amlworkspace"
              ]
            }
          }
        ],
        "manualPrivateLinkServiceConnections": [],
        "subnet": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "[concat('PrivateDns-', variables('privateDnsGuid'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateEndpoints', concat(parameters('workspaceName'), '-PrivateEndpoint'))]"
      ],
      "properties": {
          "mode": "Incremental",
          "template": {
              "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
              "contentVersion": "1.0.0.0",
              "resources": [
                  {
                      "type": "Microsoft.Network/privateDnsZones",
                      "apiVersion": "2018-09-01",
                      "name": "privatelink.api.azureml.ms",
                      "location": "global",
                      "tags": {},
                      "properties": {}
                  },
                  {
                      "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
                      "apiVersion": "2018-09-01",
                      "name": "[concat('privatelink.api.azureml.ms', '/', uniqueString(resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))))]",
                      "location": "global",
                      "dependsOn": [
                          "privatelink.api.azureml.ms"
                      ],
                      "properties": {
                          "virtualNetwork": {
                              "id": "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
                          },
                          "registrationEnabled": false
                      }
                  },
                  {
                      "apiVersion": "2017-05-10",
                      "name": "[concat('EndpointDnsRecords-', variables('privateDnsGuid'))]",
                      "type": "Microsoft.Resources/deployments",
                      "dependsOn": [
                          "privatelink.api.azureml.ms"
                      ],
                      "properties": {
                          "mode": "Incremental",
                          "templatelink": {
                              "contentVersion": "1.0.0.0",
                              "uri": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpoint.json"
                          },
                          "parameters": {
                              "privateDnsName": {
                                  "value": "privatelink.api.azureml.ms"
                              },
                              "privateEndpointNicResourceId": {
                                  "value": "[reference(resourceId('Microsoft.Network/privateEndpoints',concat(parameters('workspaceName'), '-PrivateEndpoint'))).networkInterfaces[0].id]"
                              },
                              "nicRecordsTemplateUri": {
                                  "value": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpointNic.json"
                              },
                              "ipConfigRecordsTemplateUri": {
                                  "value": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpointIpConfig.json"
                              },
                              "uniqueId": {
                                  "value": "[variables('privateDnsGuid')]"
                              },
                              "existingRecords": {
                                  "value": {}
                              }
                          }
                      }
                  }
              ]
          }
      },
      "resourceGroup": "[resourceGroup().name]"
  }
  ]
}
```

<a id="cmkmapl"></a>
### <a name="workspace-with-customer-managed-keys-and-manual-approval-for-private-link"></a>Espace de travail avec clés gérées par le client et approbation manuelle pour Azure Private Link

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "eastus",
        "southcentralus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku":{
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "hbi_workspace":{
      "type": "string",
      "defaultValue": "false",
      "allowedValues": [
        "false",
        "true"
      ],
      "metadata": {
        "description": "Specifies that the Azure Machine Learning workspace holds highly confidential data."
      }
    },
    "encryption_status":{
      "type": "string",
      "defaultValue": "Disabled",
      "allowedValues": [
        "Enabled",
        "Disabled"
      ],
      "metadata": {
        "description": "Specifies if the Azure Machine Learning workspace should be encrypted with customer managed key."
      }
    },
    "cmk_keyvault":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyVault id."
      }
    },
    "resource_cmk_uri":{
      "type": "string",
      "metadata": {
        "description": "Specifies if the customer managed keyvault key uri."
      }
    },
    "subnetName": {
        "type": "string"
      },
      "vnetName": {
        "type": "string"
      }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2018-05-01-preview",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2020-01-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]",
         "encryption": {
                "status": "[parameters('encryption_status')]",
                "keyVaultProperties": {
                    "keyVaultArmId": "[parameters('cmk_keyvault')]",
                    "keyIdentifier": "[parameters('resource_cmk_uri')]"
                  }
            },
        "hbi_workspace": "[parameters('hbi_workspace')]"
      }
    },
    {
        "type": "Microsoft.Network/virtualNetworks",
        "apiVersion": "2019-09-01",
        "name": "[parameters('vnetName')]",
        "location": "[parameters('location')]",
        "properties": {
            "addressSpace": {
                "addressPrefixes": [
                    "10.0.0.0/27"
                ]
            },
            "virtualNetworkPeerings": [],
            "enableDdosProtection": false,
            "enableVmProtection": false
        }
      },
    {
        "type": "Microsoft.Network/virtualNetworks/subnets",
        "apiVersion": "2019-09-01",
        "name": "[concat(parameters('vnetName'), '/', parameters('subnetName'))]",
        "dependsOn": [
            "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
        ],
        "properties": {
            "addressPrefix": "10.0.0.0/27",
            "delegations": [],
            "privateEndpointNetworkPolicies": "Disabled",
            "privateLinkServiceNetworkPolicies": "Enabled"
        }
    },
    {
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
      "type": "Microsoft.Network/privateEndpoints",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
      ],
      "properties": {
        "privateLinkServiceConnections": [],
        "manualPrivateLinkServiceConnections": [
          {
            "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
              "groupIds": [
                "amlworkspace"
              ]
            }
          }
        ],
        "subnet": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
        }
      }
    }
  ]
}
```

<a id="mmkaapl"></a>
### <a name="workspace-with-microsoft-managed-keys-and-auto-approval-for-private-link"></a>Espace de travail avec clés gérées par Microsoft et approbation automatique pour Azure Private Link

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "eastus",
        "southcentralus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku": {
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "subnetName": {
      "type": "string"
    },
    "vnetName": {
      "type": "string"
    }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
    "privateDnsGuid": "[guid(resourceGroup().id, deployment().name)]"
    
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2018-05-01-preview",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
        "tier": "[parameters('sku')]",
        "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
      }
    },
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vnetName')]",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "10.0.0.0/27"
          ]
        },
        "virtualNetworkPeerings": [],
        "enableDdosProtection": false,
        "enableVmProtection": false
      }
    },
    {
      "type": "Microsoft.Network/virtualNetworks/subnets",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('vnetName'), '/', parameters('subnetName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
      ],
      "properties": {
        "addressPrefix": "10.0.0.0/27",
        "delegations": [],
        "privateEndpointNetworkPolicies": "Disabled",
        "privateLinkServiceNetworkPolicies": "Enabled"
      }
    },
    {
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
      "type": "Microsoft.Network/privateEndpoints",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
      ],
      "properties": {
        "privateLinkServiceConnections": [
          {
            "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
              "groupIds": [
                "amlworkspace"
              ]
            }
          }
        ],
        "subnet": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "[concat('PrivateDns-', variables('privateDnsGuid'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/privateEndpoints', concat(parameters('workspaceName'), '-PrivateEndpoint'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Network/privateDnsZones",
              "apiVersion": "2018-09-01",
              "name": "privatelink.api.azureml.ms",
              "location": "global",
              "tags": {},
              "properties": {}
            },
            {
              "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
              "apiVersion": "2018-09-01",
              "name": "[concat('privatelink.api.azureml.ms', '/', uniqueString(resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))))]",
              "location": "global",
              "dependsOn": [
                "privatelink.api.azureml.ms"
              ],
              "properties": {
                "virtualNetwork": {
                  "id": "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
                },
                "registrationEnabled": false
              }
            },
            {
              "apiVersion": "2017-05-10",
              "name": "[concat('EndpointDnsRecords-', variables('privateDnsGuid'))]",
              "type": "Microsoft.Resources/deployments",
              "dependsOn": [
                "privatelink.api.azureml.ms"
              ],
              "properties": {
                "mode": "Incremental",
                "templatelink": {
                  "contentVersion": "1.0.0.0",
                  "uri": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpoint.json"
                },
                "parameters": {
                  "privateDnsName": {
                    "value": "privatelink.api.azureml.ms"
                  },
                  "privateEndpointNicResourceId": {
                    "value": "[reference(resourceId('Microsoft.Network/privateEndpoints',concat(parameters('workspaceName'), '-PrivateEndpoint'))).networkInterfaces[0].id]"
                  },
                  "nicRecordsTemplateUri": {
                    "value": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpointNic.json"
                  },
                  "ipConfigRecordsTemplateUri": {
                    "value": "https://network.hosting.portal.azure.net/network/Content/4.13.392.925/DeploymentTemplates/PrivateDnsForPrivateEndpointIpConfig.json"
                  },
                  "uniqueId": {
                    "value": "[variables('privateDnsGuid')]"
                  },
                  "existingRecords": {
                    "value": {}
                  }
                }
              }
            }
          ]
        }
      },
      "resourceGroup": "[resourceGroup().name]"
    }
  ]
}
```

<a id="mmkmapl"></a>
### <a name="workspace-with-microsoft-managed-keys-and-manual-approval-for-private-link"></a>Espace de travail avec clés gérées par Microsoft et approbation manuelle pour Azure Private Link

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "allowedValues": [
        "eastus",
        "southcentralus",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku": {
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "subnetName": {
      "type": "string"
    },
    "vnetName": {
      "type": "string"
    }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2018-05-01-preview",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
        "tier": "[parameters('sku')]",
        "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
      }
    },
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vnetName')]",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "10.0.0.0/27"
          ]
        },
        "virtualNetworkPeerings": [],
        "enableDdosProtection": false,
        "enableVmProtection": false
      }
    },
    {
      "type": "Microsoft.Network/virtualNetworks/subnets",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('vnetName'), '/', parameters('subnetName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/virtualNetworks', parameters('vnetName'))]"
      ],
      "properties": {
        "addressPrefix": "10.0.0.0/27",
        "delegations": [],
        "privateEndpointNetworkPolicies": "Disabled",
        "privateLinkServiceNetworkPolicies": "Enabled"
      }
    },
    {
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
      "type": "Microsoft.Network/privateEndpoints",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
        "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
      ],
      "properties": {
        "privateLinkServiceConnections": [],
        "manualPrivateLinkServiceConnections": [
          {
            "name": "[concat(parameters('workspaceName'), '-PrivateEndpoint')]",
            "properties": {
              "privateLinkServiceId": "[resourceId('Microsoft.MachineLearningServices/workspaces', parameters('workspaceName'))]",
              "groupIds": [
                "amlworkspace"
              ]
            }
          }
        ],
        "subnet": {
          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('vnetName'), parameters('subnetName') )]"
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la sécurisation de votre espace de travail Azure Machine Learning, consultez l’article [Sécurité des entreprises](concept-enterprise-security.md).

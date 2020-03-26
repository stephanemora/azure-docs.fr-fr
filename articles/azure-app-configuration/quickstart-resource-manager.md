---
title: 'Démarrage rapide : Déploiement automatique d’une machine virtuelle avec Azure App Configuration'
description: Ce guide de démarrage rapide vous montre comment utiliser le module Azure PowerShell et les modèles Azure Resource Manager pour déployer un magasin Azure App Configuration. Il vous montre ensuite comment déployer une machine virtuelle en utilisant les valeurs stockées dans le magasin.
author: lisaguthrie
ms.author: lcozzens
ms.date: 03/05/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: c45f6855c33dff2790ced306fd7f049b98dd1387
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79126387"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template"></a>Démarrage rapide : Déploiement automatique d’une machine virtuelle avec un modèle Resource Manager et App Configuration

Le module Azure PowerShell est utilisé pour créer et gérer des ressources Azure à l’aide des applets de commande ou des scripts PowerShell. Ce guide de démarrage rapide vous montre comment utiliser Azure PowerShell et les modèles Azure Resource Manager pour déployer un magasin Azure App Configuration. Ensuite, il vous explique comment déployer une machine virtuelle en utilisant les paires clé-valeur stockées dans le magasin.

Vous utilisez le modèle prérequis pour créer un magasin App Configuration, puis vous ajoutez des paires clé-valeur dans le magasin à l’aide du Portail Azure ou d’Azure CLI. Le modèle principal référence des configurations de paires clé-valeur existantes qui proviennent d’un magasin de configurations déjà créé. Les valeurs récupérées sont ensuite utilisées pour définir les propriétés des ressources créées par le modèle, comme une machine virtuelle dans cet exemple.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/).

* Ce démarrage rapide nécessite le module Azure PowerShell. Exécutez `Get-Module -ListAvailable Az` pour rechercher la version installée sur votre ordinateur local. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Connect-AzAccount`, puis entrez vos informations d’identification Azure dans le navigateur contextuel :

```azurepowershell-interactive
# Connect to your Azure account
Connect-AzAccount
```

Si vous avez plusieurs abonnements, sélectionnez l’abonnement que vous souhaitez utiliser dans ce guide de démarrage rapide en exécutant les applets de commande suivantes. N’oubliez pas de remplacer `<your subscription name>` par le nom de votre abonnement :

```azurepowershell-interactive
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

```azurepowershell-interactive
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="deploy-an-azure-app-configuration-store"></a>Déployer un magasin Azure App Configuration

Avant de pouvoir appliquer des paires clé-valeur sur la machine virtuelle, vous devez disposer d’un magasin Azure App Configuration existant. Cette section explique en détail comment déployer un magasin Azure App Configuration à l’aide d’un modèle Azure Resource Manager. Si vous avez déjà un magasin App Configuration, vous pouvez passer à la section suivante de cet article. 

1. Copiez et collez le code JSON suivant dans un nouveau fichier nommé *prereq.azuredeploy.json*.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "type": "string",
        "metadata": {
          "description": "Specifies the name of the app configuration store."
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Specifies the Azure location where the app configuration store should be created."
        }
      },
      "skuName": {
        "type": "string",
        "defaultValue": "standard",
        "metadata": {
          "description": "Specifies the SKU of the app configuration store."
        }
      }
    },
    "resources": [
      {
        "type": "Microsoft.AppConfiguration/configurationStores",
        "name": "[parameters('configStoreName')]",
        "apiVersion": "2019-10-01",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('skuName')]"
        }
      }
    ]
   }
   ```

1. Copiez et collez le code JSON ci-dessous dans un nouveau fichier nommé *prereq.azuredeploy.parameters.json*. Remplacez **GET-UNIQUE** par un nom unique pour votre magasin de configuration.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "value": "GET-UNIQUE"
      }
    }
   }
   ```

1. Dans la fenêtre PowerShell, exécutez la commande suivante pour déployer le magasin Azure App Configuration. N’oubliez pas de remplacer les variables (pour le nom du groupe de ressources, le chemin du fichier de modèle et le chemin du fichier de paramètres du modèle) par vos propres valeurs.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

## <a name="add-vm-configuration-key-values"></a>Ajouter les paires clé-valeur sur la machine virtuelle

Vous pouvez créer un magasin App Configuration à partir d’un modèle Azure Resource Manager, mais vous devez ensuite ajouter les paires clé-valeur en utilisant le portail Azure ou Azure CLI. Dans ce guide de démarrage rapide, vous ajoutez les paires clé-valeur en utilisant le portail Azure.

1. Une fois le déploiement terminé, accédez au nouveau magasin App Configuration dans le [portail Azure](https://portal.azure.com).

1. Sélectionnez **Paramètres** > **Clés d’accès**. Notez la chaîne de connexion de la clé primaire en lecture seule. Vous utilisez cette chaîne de connexion plus tard pour configurer votre application, afin qu’elle communique avec le magasin App Configuration que vous avez créé.

1. Sélectionnez **Explorateur de configurations** > **Créer** pour ajouter les paires clé-valeur suivantes :

   |Clé|Valeur|
   |-|-|
   |windowsOsVersion|2019-Datacenter|
   |diskSizeGB|1023|
  
   Entrez *modèle* comme **Étiquette**, mais laissez le champ **Type de contenu** vide.

## <a name="deploy-vm-using-stored-key-values"></a>Déployer une machine virtuelle en utilisant les paires clé-valeur

Maintenant que vous avez ajouté les paires clé-valeur au magasin, vous êtes prêt à déployer une machine virtuelle à l’aide d’un modèle Azure Resource Manager. Le modèle référence les clés **windowsOsVersion** et **diskSizeGB** que vous avez créées.

1. Copiez et collez le code JSON suivant dans un nouveau fichier nommé *azuredeploy.json*, ou téléchargez le fichier à partir des [modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.json).

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "Admin user name."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "appConfigStoreName": {
            "type": "string",
            "metadata": {
                "description": "App configuration store name."
            }
        },
        "appConfigStoreResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Name of the resource group for the app config store."
            }
        },
        "domainNameLabel": {
            "type": "string",
            "metadata": {
                "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_D2_v3",
            "metadata": {
                "description": "Size of the VM"
            }
        },
        "vmSkuKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM windows sku"
            }
        },
        "diskSizeKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM disk size"
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "The name of the storage account."
            }
        }
    },
    "variables": {
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressName": "myPublicIP",
        "vmName": "SimpleWinVM",
        "virtualNetworkName": "MyVNET",
        "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]",
        "appConfigRef": "[resourceId(parameters('appConfigStoreResourceGroup'), 'Microsoft.AppConfiguration/configurationStores', parameters('appConfigStoreName'))]",
        "windowsOSVersionParameters": {
            "key": "[parameters('vmSkuKey')]",
            "label": "template"
        },
        "diskSizeGBParameters": {
            "key": "[parameters('diskSizeKey')]",
            "label": "template"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-11-01",
            "name": "[parameters('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[variables('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('domainNameLabel')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2018-11-01",
            "name": "[variables('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2018-11-01",
            "name": "[variables('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[resourceId('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2018-10-01",
            "name": "[variables('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computerName": "[variables('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('windowsOSVersionParameters')).value]",
                        "version": "latest"
                    },
                    "osDisk": {
                        "createOption": "FromImage"
                    },
                    "dataDisks": [
                        {
                            "diskSizeGB": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('diskSizeGBParameters')).value]",
                            "lun": 0,
                            "createOption": "Empty"
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                        }
                    ]
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))).primaryEndpoints.blob]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "hostname": {
            "type": "string",
            "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]"
        }
    }
   }
   ```

1. Copiez et collez le code JSON ci-dessous dans un nouveau fichier nommé *azuredeploy.parameters.json*, ou téléchargez le fichier à partir des [modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.parameters.json).

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminPassword": {
        "value": "GEN-PASSWORD"
      },
      "appConfigStoreName":{
        "value": "GEN-APPCONFIGSTORE-NAME"
      },
      "appConfigStoreResourceGroup": {
         "value": "GEN-APPCONFIGSTORE-RESOURCEGROUP-NAME"
      },
      "vmSkuKey":{
        "value": "GEN-APPCONFIGSTORE-WINDOWSOSVERSION"
      },
      "diskSizeKey" :{
         "value": "GEN-APPCONFIGSTORE-DISKSIZEGB"
      },
      "adminUsername":{
        "value": "GEN-UNIQUE"
      },
      "storageAccountName":{
        "value": "GEN-UNIQUE"
      },
      "domainNameLabel":{
        "value": "GEN-UNIQUE"
      }
    }
   }
   ```

   Remplacez les valeurs des paramètres dans le modèle par les valeurs suivantes :

   |Paramètre|Valeur|
   |-|-|
   |adminPassword|Mot de passe d'administrateur pour la machine virtuelle.|
   |appConfigStoreName|Nom de votre magasin Azure App Configuration.|
   |appConfigStoreResourceGroup|Groupe de ressources qui contient votre magasin App Configuration.|
   |vmSkuKey|*windowsOSVersion*|
   |diskSizeKey|*diskSizeGB*|
   |adminUsername|Nom d’administrateur pour la machine virtuelle.|
   |storageAccountName|Nom unique d’un compte de stockage associé à la machine virtuelle.|
   |domainNameLabel|Nom de domaine unique.|

1. Dans la fenêtre PowerShell, exécutez la commande suivante pour déployer le magasin Azure App Configuration. N’oubliez pas de remplacer les variables (pour le nom du groupe de ressources, le chemin du fichier de modèle et le chemin du fichier de paramètres du modèle) par vos propres valeurs.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

Félicitations ! Vous avez déployé une machine virtuelle avec les configurations stockées dans Azure App Configuration.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, le magasin App Configuration, la machine virtuelle et toutes les ressources associées. Si vous envisagez de réutiliser le magasin App Configuration ou la machine virtuelle plus tard, ne les supprimez pas. Si vous n’avez plus besoin de ce travail, supprimez toutes les ressources créées dans le cadre de ce guide de démarrage rapide en exécutant l’applet de commande suivante :

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une machine virtuelle en utilisant un modèle Azure Resource Manager et des paires clé-valeur du magasin Azure App Configuration.

Pour découvrir comment créer d’autres applications avec Azure App Configuration, passez à l’article suivant :

> [!div class="nextstepaction"]
> [Démarrage rapide : Créer une application ASP.NET Core avec Azure App Configuration](quickstart-aspnet-core-app.md)

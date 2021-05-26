---
title: Questions courantes sur les machines virtuelles dans Place de marché Azure
description: Questions courantes rencontrées lors de la création d’une machine virtuelle dans Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: kriti-ms
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 867b9dfefd47e38b5bcfd54ae54aab607e7acea5
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109814907"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Questions courantes sur les machines virtuelles dans Place de marché Azure

Ces questions fréquentes couvrent les problèmes courants que vous pouvez rencontrer lors de la création d’une offre de machine virtuelle dans Place de marché Azure.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Comment configurer un réseau privé virtuel (VPN) pour qu’il fonctionne avec mes machines virtuelles ?

Si vous utilisez le modèle de déploiement Azure Resource Manager, vous disposez de trois options :

- [Créer une passerelle VPN basée sur des itinéraires à l’aide du Portail Azure](../vpn-gateway/tutorial-create-gateway-portal.md)
- [Créer une passerelle VPN basée sur un itinéraire à l’aide d’Azure PowerShell](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [Créer une passerelle VPN basée sur des itinéraires à l’aide de l’interface CLI](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quelles sont les stratégies de prise en charge de Microsoft pour exécuter des logiciels serveur Microsoft sur des machines virtuelles Azure ?

Pour plus d’informations, consultez [Prise en charge des logiciels serveurs Microsoft pour les machines virtuelles Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Dans une machine virtuelle, comment gérer l’extension de script personnalisé lors de la tâche de démarrage ?

Pour plus d’informations sur l’utilisation de l’extension de script personnalisé avec le module Azure PowerShell, sur les modèles Azure Resource Manager et sur les étapes de dépannage des systèmes Windows, consultez [Extension de script personnalisé pour Windows](../virtual-machines/extensions/custom-script-windows.md).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Les services et les applications 32 bits sont-ils pris en charge dans la Place de marché Azure ?

Non. Les systèmes d’exploitation et services standard pris en charge pour les machines virtuelles Azure sont tous au format 64 bits. Bien que la plupart des systèmes d’exploitation 64 bits prennent en charge les versions 32 bits des applications à des fins de compatibilité descendante, l’utilisation d’applications 32 bits dans le cadre de votre solution de machine virtuelle n’est pas prise en charge et est fortement déconseillée. Vous devez recréer votre application sous la forme d’un projet 64 bits.

Pour plus d’informations, voir les articles suivants :

- [Running 32-bit applications](/windows/desktop/WinProg64/running-32-bit-applications) (Exécution d’applications 32 bits)
- [Prise en charge pour les systèmes d’exploitation 32 bits sur des machines virtuelles Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Prise en charge des logiciels serveurs Microsoft pour les machines virtuelles Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Erreur : Le disque dur virtuel est déjà inscrit auprès du référentiel d’images en tant que ressource

Chaque fois que j’essaie de créer une image à partir de mes disques durs virtuels, j’obtiens l’erreur « VHD is already registered with image repository as the resource » (Le disque dur virtuel est déjà inscrit auprès du référentiel d’images en tant que ressource) dans Azure PowerShell. Or, je n’ai pas créé d’image, et je n’ai pas non plus trouvé d’image portant ce nom dans Azure. Comment résoudre ce problème ?

Ce problème se produit généralement si vous avez créé une machine virtuelle à partir d’un disque dur virtuel verrouillé. Vérifiez qu’aucune machine virtuelle n’est allouée à partir de ce disque dur virtuel, puis tentez à nouveau l’opération. Si le problème persiste, ouvrez un ticket de support. Consultez [Prise en charge de l’Espace partenaires](support.md).

## <a name="how-do-i-create-a-vm-from-a-generalized-vhd"></a>Comment créer une machine virtuelle à partir d’un disque dur virtuel généralisé ?

### <a name="prepare-an-azure-resource-manager-template"></a>Préparer un modèle Azure Resource Manager

Cette section décrit comment créer et déployer une image de machine virtuelle fournie par l’utilisateur. Pour ce faire, vous pouvez fournir des images de disque dur virtuel du système d’exploitation et de disque de données à partir d’un disque dur virtuel déployé par Azure. Ces étapes déploient la machine virtuelle à l’aide du disque dur virtuel généralisé.

1. Connectez-vous au portail Azure.
2. Chargez votre disque dur virtuel de système d’exploitation généralisé et vos disques durs virtuels de disques de données sur votre compte de stockage Azure.
3. Dans la page d’accueil, sélectionnez Créer une ressource, recherchez « Déploiement de modèle », puis sélectionnez Créer.
4. Choisissez Créer votre propre modèle dans l’éditeur.

   :::image type="content" source="media/vm/template-deployment.png" alt-text="Affiche la sélection d’un modèle":::

5. Collez le modèle JSON suivant dans l’éditeur, puis sélectionnez Enregistrer.
 ```json
  {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "userStorageAccountName": {
               "type": "String"
           },
           "userStorageContainerName": {
               "defaultValue": "vhds",
               "type": "String"
           },
           "dnsNameForPublicIP": {
               "type": "String"
           },
           "adminUserName": {
               "defaultValue": "isv",
               "type": "String"
           },
           "adminPassword": {
               "defaultValue": "",
               "type": "SecureString"
           },
           "osType": {
               "defaultValue": "windows",
               "allowedValues": [
                   "windows",
                   "linux"
               ],
               "type": "String"
           },
           "subscriptionId": {
               "type": "String"
           },
           "location": {
               "type": "String"
           },
           "vmSize": {
               "type": "String"
           },
           "publicIPAddressName": {
               "type": "String"
           },
           "vmName": {
               "type": "String"
           },
           "virtualNetworkName": {
               "type": "String"
           },
           "nicName": {
               "type": "String"
           },
           "vhdUrl": {
               "type": "String",
               "metadata": {
                   "description": "VHD Url..."
               }
           }
       },
       "variables": {
           "addressPrefix": "10.0.0.0/16",
           "subnet1Name": "Subnet-1",
           "subnet2Name": "Subnet-2",
           "subnet1Prefix": "10.0.0.0/24",
           "subnet2Prefix": "10.0.1.0/24",
           "publicIPAddressType": "Dynamic",
           "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
           "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
           "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
           "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
       },
       "resources": [
           {
               "type": "Microsoft.Network/publicIPAddresses",
               "apiVersion": "2015-06-15",
               "name": "[parameters('publicIPAddressName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                   "dnsSettings": {
                       "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                   }
               }
           },
           {
               "type": "Microsoft.Network/virtualNetworks",
               "apiVersion": "2015-06-15",
               "name": "[parameters('virtualNetworkName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "addressSpace": {
                       "addressPrefixes": [
                           "[variables('addressPrefix')]"
                       ]
                   },
                   "subnets": [
                       {
                           "name": "[variables('subnet1Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet1Prefix')]"
                           }
                       },
                       {
                           "name": "[variables('subnet2Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet2Prefix')]"
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Network/networkInterfaces",
               "apiVersion": "2015-06-15",
               "name": "[parameters('nicName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                   "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
               ],
               "properties": {
                   "ipConfigurations": [
                       {
                           "name": "ipconfig1",
                           "properties": {
                               "privateIPAllocationMethod": "Dynamic",
                               "publicIPAddress": {
                                   "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                               },
                               "subnet": {
                                   "id": "[variables('subnet1Ref')]"
                               }
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Compute/virtualMachines",
               "apiVersion": "2015-06-15",
               "name": "[parameters('vmName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
               ],
               "properties": {
                   "hardwareProfile": {
                       "vmSize": "[parameters('vmSize')]"
                   },
                   "osProfile": {
                       "computername": "[parameters('vmName')]",
                       "adminUsername": "[parameters('adminUsername')]",
                       "adminPassword": "[parameters('adminPassword')]"
                   },
                   "storageProfile": {
                       "osDisk": {
                           "name": "[concat(parameters('vmName'),'-osDisk')]",
                           "osType": "[parameters('osType')]",
                           "caching": "ReadWrite",
                           "image": {
                               "uri": "[parameters('vhdUrl')]"
                           },
                           "vhd": {
                               "uri": "[variables('osDiskVhdName')]"
                           },
                           "createOption": "FromImage"
                       }
                   },
                   "networkProfile": {
                       "networkInterfaces": [
                           {
                               "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                           }
                       ]
                   }
               }
           }
       ]
   }
   ```


6. Indiquez les valeurs des paramètres pour les pages de propriétés Déploiement personnalisé affichées.

 **TABLEAU 1**

| **ResourceGroupName** | **Nom du groupe de ressources Azure existant. En général, vous devez utiliser le même groupe de ressources que votre coffre de clés.** |
| --- | --- |
| TemplateFile | Chemin complet du fichier VHDtoImage.json. |
| userStorageAccountName | Nom du compte de stockage. |
| dnsNameForPublicIP | Nom DNS de l’adresse IP publique. Doit être en minuscules. |
| subscriptionId | Identificateur de l’abonnement Azure. |
| Emplacement | Sélectionnez un emplacement géographique Azure standard pour le groupe de ressources. |
| vmName | Nom de la machine virtuelle. |
| vhdUrl | Adresse web du disque dur virtuel. |
| vmSize | Taille de l’instance de machine virtuelle. |
| publicIPAddressName | Nom de l’adresse IP publique. |
| virtualNetworkName | Nom du réseau virtuel. |
| nicName | Nom de la carte d’interface réseau du réseau virtuel. |
| adminUsername | Nom d’utilisateur du compte administrateur. |
| adminPassword | Mot de passe de l’administrateur. |
|

7. Après avoir indiqué ces valeurs, sélectionnez Achat.

Azure commence le déploiement. Une nouvelle machine virtuelle est créée avec le disque dur virtuel non managé spécifié, au chemin du compte de stockage spécifié. Pour en suivre la progression dans le portail Azure, sélectionnez Machines virtuelles sur le côté gauche du portail. Une fois la machine virtuelle créée, l’état passe de Démarrage à En cours d’exécution.

Pour le déploiement de machines virtuelles de 2e génération, utilisez ce modèle :
 ```json
 {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "userStorageAccountName": {
              "type": "String"
          },
          "userStorageContainerName": {
              "defaultValue": "vhds",
              "type": "String"
          },
          "dnsNameForPublicIP": {
              "type": "String"
          },
          "adminUserName": {
              "defaultValue": "isv",
              "type": "String"
          },
          "adminPassword": {
              "defaultValue": "",
              "type": "SecureString"
          },
          "osType": {
              "defaultValue": "windows",
              "allowedValues": [
                  "windows",
                  "linux"
              ],
              "type": "String"
          },
          "subscriptionId": {
              "type": "String"
          },
          "location": {
              "type": "String"
          },
          "vmSize": {
              "type": "String"
          },
          "publicIPAddressName": {
              "type": "String"
          },
          "vmName": {
              "type": "String"
          },
          "virtualNetworkName": {
              "type": "String"
          },
          "nicName": {
              "type": "String"
          },
          "vhdUrl": {
              "type": "String",
              "metadata": {
                  "description": "VHD Url..."
              }
          }
      },
      "variables": {
          "addressPrefix": "10.0.0.0/16",
          "subnet1Name": "Subnet-1",
          "subnet2Name": "Subnet-2",
          "subnet1Prefix": "10.0.0.0/24",
          "subnet2Prefix": "10.0.1.0/24",
          "publicIPAddressType": "Dynamic",
          "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
          "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
          "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
      },
      "resources": [
          {
              "type": "Microsoft.Network/publicIPAddresses",
              "apiVersion": "2015-06-15",
              "name": "[parameters('publicIPAddressName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                  "dnsSettings": {
                      "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                  }
              }
          },
          {
              "type": "Microsoft.Network/virtualNetworks",
              "apiVersion": "2015-06-15",
              "name": "[parameters('virtualNetworkName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "addressSpace": {
                      "addressPrefixes": [
                          "[variables('addressPrefix')]"
                      ]
                  },
                  "subnets": [
                      {
                          "name": "[variables('subnet1Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet1Prefix')]"
                          }
                      },
                      {
                          "name": "[variables('subnet2Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet2Prefix')]"
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Network/networkInterfaces",
              "apiVersion": "2015-06-15",
              "name": "[parameters('nicName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                  "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
              ],
              "properties": {
                  "ipConfigurations": [
                      {
                          "name": "ipconfig1",
                          "properties": {
                              "privateIPAllocationMethod": "Dynamic",
                              "publicIPAddress": {
                                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                              },
                              "subnet": {
                                  "id": "[variables('subnet1Ref')]"
                              }
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2015-06-15",
              "name": "[parameters('vmName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
              ],
              "properties": {
                  "hardwareProfile": {
                      "vmSize": "[parameters('vmSize')]"
                  },
                  "osProfile": {
                      "computername": "[parameters('vmName')]",
                      "adminUsername": "[parameters('adminUsername')]",
                      "adminPassword": "[parameters('adminPassword')]"
                  },
                  "storageProfile": {
                      "osDisk": {
                          "name": "[concat(parameters('vmName'),'-osDisk')]",
                          "osType": "[parameters('osType')]",
                          "caching": "ReadWrite",
                          "image": {
                              "uri": "[parameters('vhdUrl')]"
                          },
                          "vhd": {
                              "uri": "[variables('osDiskVhdName')]"
                          },
                          "createOption": "FromImage"
                      }
                  },
                  "networkProfile": {
                      "networkInterfaces": [
                          {
                              "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                          }
                      ]
                  }
              }
          }
      ]
  }
  ```


### <a name="deploy-an-azure-vm-using-powershell"></a>Déployer une machine virtuelle Azure à l’aide de PowerShell

Copiez et modifiez le script suivant pour indiquer les valeurs des variables `$storageaccount` et `$vhdUrl`. Exécutez-le pour créer une ressource de machine virtuelle Azure à partir de votre disque dur virtuel généralisé existant.

```powershell
# storage account of existing generalized VHD
$storageaccount = "testwinrm11815"
# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName"
```

## <a name="how-do-i-test-a-hidden-preview-image"></a>Comment tester une image d’aperçu masquée ?

Vous pouvez déployer des images d’aperçu masquées à l’aide de modèles de démarrage rapide.
Pour déployer une image d’aperçu,
1. Accédez au modèle de démarrage rapide approprié pour [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-simple-linux/) ou [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows), puis sélectionnez « Déployer sur Azure ». Cela devrait vous amener au portail Azure.
2. Sur le Portail Azure, sélectionnez « Modifier le modèle ».
3. Dans le modèle JSON, recherchez imageReference et mettez à jour les valeurs publisherid, offerid, skuid, et version de l’image. Pour tester l’image d’aperçu, ajoutez « -PREVIEW » à offerid.
 ![image](https://user-images.githubusercontent.com/79274470/110191995-71c7d500-7de0-11eb-9f3c-6a42f55d8f03.png)
4. Cliquez sur Enregistrer.
5. Renseignez les autres détails. Vérifier et créer


## <a name="next-steps"></a>Étapes suivantes

- [Résolution des problèmes de certification de machines virtuelles](azure-vm-create-certification-faq.md)

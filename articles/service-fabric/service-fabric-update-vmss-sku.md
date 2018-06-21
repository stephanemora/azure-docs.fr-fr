---
title: Procédure de mise à niveau/migration de la référence (SKU) pour PrimaryNodeType vers des références supérieures | Microsoft Docs
description: Découvrez comment mettre à niveau/migrer la référence (SKU) pour PrimaryNodeType vers des références supérieures à l’aide des commandes PowerShell et CLI.
services: service-fabric
documentationcenter: .net
author: v-rachiw
manager: navya
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2018
ms.author: v-rachiw
ms.openlocfilehash: 96956543a44b6d5d967e3bae3fd833b08baf3d6f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642731"
---
# <a name="upgrademigrate-the-sku-for-primary-node-type-to-higher-sku"></a>Mettre à niveau/migrer la référence (SKU) pour le Type de nœud principal vers des références supérieures

Cet article décrit comment effectuer une mise à niveau/migration de la référence (SKU) du Type de nœud principal du cluster Service Fabric vers une référence (SKU) supérieure à l’aide d’Azure PowerShell.

## <a name="add-a-new-virtual-machine-scale-set"></a>Ajouter un nouveau groupe de machines virtuelles identiques

Déployez un nouveau groupe de machines virtuelles identiques et équilibreur de charge. La configuration d’extension Service Fabric (surtout le type de nœud) du nouveau groupe de machines virtuelles identiques doit être identique à celle de l’ancien groupe de machines virtuelles identiques que vous tentez de mettre à niveau. Dans l’explorateur Service Fabric, vérifiez que vos nouveaux nœuds sont disponibles.

#### <a name="azure-powershell"></a>Azure PowerShell

L’exemple suivant utilise Azure PowerShell pour déployer le modèle Resource Manager mis à jour *template.json* à l’aide du groupe de ressources nommé *myResourceGroup* :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile template.json -TemplateParameterFile parameters.json
```

#### <a name="azure-cli"></a>Azure CLI

La commande suivante utilise l’interface de ligne de commande Azure Service Fabric pour déployer le modèle Resource Manager mis à jour *template.json* à l’aide du groupe de ressources nommé *myResourceGroup* :

```CLI
az group deployment create --resource-group myResourceGroup --template-file template.json --parameters parameters.json
```

Consultez l’exemple suivant pour modifier le modèle json afin d’ajouter la nouvelle ressource de groupe de machines virtuelles identiques avec le type de nœud Principal dans un cluster existant.

```json
        {
            "apiVersion": "[variables('vmssApiVersion')]",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[parameters('vmNodeType2Name')]",
            "location": "[parameters('computeLocation')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType2Name')))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "overprovision": "[parameters('overProvision')]",
                "upgradePolicy": {
                    "mode": "Automatic"
                },
                "virtualMachineProfile": {
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "[concat(parameters('vmNodeType2Name'),'_ServiceFabricNode')]",
                                "properties": {
                                    "type": "ServiceFabricNode",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                        "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                                    },
                                    "publisher": "Microsoft.Azure.ServiceFabric",
                                    "settings": {
                                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                                        "dataPath": "D:\\\\SvcFab",
                                        "durabilityLevel": "Bronze",
                                        "enableParallelJobs": true,
                                        "nicPrefixOverride": "[parameters('subnet0Prefix')]",
                                        "certificate": {
                                            "thumbprint": "[parameters('certificateThumbprint')]",
                                            "x509StoreName": "[parameters('certificateStoreValue')]"
                                        }
                                    },
                                    "typeHandlerVersion": "1.0"
                                }
                            },
                            {
                                "name": "[concat('VMDiagnosticsVmExt','_vmNodeType2Name')]",
                                "properties": {
                                    "type": "IaaSDiagnostics",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                                        "storageAccountEndPoint": "https://core.windows.net/"
                                    },
                                    "publisher": "Microsoft.Azure.Diagnostics",
                                    "settings": {
                                        "WadCfg": {
                                            "DiagnosticMonitorConfiguration": {
                                                "overallQuotaInMB": "50000",
                                                "EtwProviders": {
                                                    "EtwEventSourceProviderConfiguration": [
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Actors",
                                                            "scheduledTransferKeywordFilter": "1",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableActorEventTable"
                                                            }
                                                        },
                                                        {
                                                            "provider": "Microsoft-ServiceFabric-Services",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricReliableServiceEventTable"
                                                            }
                                                        }
                                                    ],
                                                    "EtwManifestProviderConfiguration": [
                                                        {
                                                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                                                            "scheduledTransferLogLevelFilter": "Information",
                                                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                                            "scheduledTransferPeriod": "PT5M",
                                                            "DefaultEvents": {
                                                                "eventDestination": "ServiceFabricSystemEventTable"
                                                            }
                                                        }
                                                    ]
                                                }
                                            }
                                        },
                                        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                                    },
                                    "typeHandlerVersion": "1.5"
                                }
                            }
                        ]
                    },
                    "networkProfile": {
                        "networkInterfaceConfigurations": [
                            {
                                "name": "[concat(parameters('nicName'), '-2')]",
                                "properties": {
                                    "ipConfigurations": [
                                        {
                                            "name": "[concat(parameters('nicName'),'-',2)]",
                                            "properties": {
                                                "loadBalancerBackendAddressPools": [
                                                    {
                                                        "id": "[variables('lbPoolID2')]"
                                                    }
                                                ],
                                                "loadBalancerInboundNatPools": [
                                                    {
                                                        "id": "[variables('lbNatPoolID2')]"
                                                    }
                                                ],
                                                "subnet": {
                                                    "id": "[variables('subnet0Ref')]"
                                                }
                                            }
                                        }
                                    ],
                                    "primary": true
                                }
                            }
                        ]
                    },
                    "osProfile": {
                        "adminPassword": "[parameters('adminPassword')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "computernamePrefix": "[parameters('vmNodeType2Name')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[parameters('sourceVaultValue')]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateStore": "[parameters('certificateStoreValue')]",
                                        "certificateUrl": "[parameters('certificateUrlValue')]"
                                    }
                                ]
                            }
                        ]
                    },
                    "storageProfile": {
                        "imageReference": {
                            "publisher": "[parameters('vmImagePublisher')]",
                            "offer": "[parameters('vmImageOffer')]",
                            "sku": "[parameters('vmImageSku')]",
                            "version": "[parameters('vmImageVersion')]"
                        },
                        "osDisk": {
                            "caching": "ReadOnly",
                            "createOption": "FromImage",
                            "managedDisk": {
                                "storageAccountType": "[parameters('storageAccountType')]"
                            }
                        }
                    }
                }
            },
            "sku": {
                "name": "[parameters('vmNodeType2Size')]",
                "capacity": "[parameters('nt2InstanceCount')]",
                "tier": "Standard"
            },
            "tags": {
                "resourceType": "Service Fabric",
                "clusterName": "[parameters('clusterName')]"
            }
        },
```

## <a name="remove-old-virtual-machine-scale-set"></a>Supprimer l’ancien groupe de machines virtuelles identiques

1. Désactivez les instances de machines virtuelles de l’ancien groupe de machines virtuelles identiques avec l’intention de supprimer le nœud. Cette opération peut prendre du temps. Vous pouvez les désactiver tous en même temps, et ils seront mis en file d’attente. Attendez que tous les nœuds soient désactivés. 
#### <a name="azure-powershell"></a>Azure PowerShell
L’exemple suivant utilise Azure Service Fabric PowerShell pour désactiver l’instance de nœud nommée *NTvm1_0* à partir de l’ancien groupe de machines virtuelles identiques nommé *NTvm1* :
```powershell
Disable-ServiceFabricNode -NodeName NTvm1_0 -Intent RemoveNode
```
#### <a name="azure-cli"></a>Azure CLI
La commande suivante utilise l’interface de ligne de commande Azure Service Fabric pour désactiver l’instance de nœud nommée *NTvm1_0* à partir de l’ancien groupe de machines virtuelles identiques nommé *NTvm1* :
```CLI
sfctl node disable --node-name "_NTvm1_0" --deactivation-intent RemoveNode
```
2. Supprimez le groupe de machines virtuelles identiques complet. Attendez que l’état de provisionnement du groupe de machines virtuelles identiques indique que l’opération a réussi.
#### <a name="azure-powershell"></a>Azure PowerShell
L’exemple suivant utilise Azure PowerShell pour supprimer le groupe de machines virtuelles identiques complet nommé *NTvm1* du groupe de ressources nommé *myResourceGroup* :
```powershell
Remove-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName NTvm1
```
#### <a name="azure-cli"></a>Azure CLI
La commande suivante utilise l’interface de ligne de commande Azure Service Fabric pour supprimer la totalité du groupe identique nommé *NTvm1* du groupe de ressources nommé *myResourceGroup* :

```CLI
az vmss delete --name NTvm1 --resource-group myResourceGroup
```

## <a name="remove-load-balancer-related-to-old-scale-set"></a>Supprimer l’équilibreur de charge lié à l’ancien groupe de machines virtuelles identiques

Supprimez l’équilibreur de charge lié à l’ancien groupe de machines virtuelles identiques. Cette étape entraîne un bref temps d’arrêt du cluster.

#### <a name="azure-powershell"></a>Azure PowerShell

L’exemple suivant utilise Azure PowerShell pour supprimer l’équilibreur de charge nommé *LB-myCluster-NTvm1* associé à l’ancien groupe de machines virtuelles identiques nommé *myResourceGroup* :

```powershell
Remove-AzureRmLoadBalancer -Name LB-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

#### <a name="azure-cli"></a>Azure CLI

La commande suivante utilise l’interface de ligne de commande Azure Service Fabric pour supprimer l’équilibreur de charge nommé *LB-myCluster-NTvm1* associé à l’ancien groupe identique nommé *myResourceGroup* :

```CLI
az network lb delete --name LB-myCluster-NTvm1 --resource-group myResourceGroup
```

## <a name="remove-public-ip-related-to-old-scale-set"></a>Supprimer l’adresse IP publique associée à l’ancien groupe de machines virtuelles identiques

Stockez les paramètres DNS de l’adresse IP publique associée à l’ancien groupe de machines virtuelles identiques dans la variable, puis supprimez l’adresse IP publique.

#### <a name="azure-powershell"></a>Azure PowerShell

L’exemple suivant utilise Azure PowerShell pour stocker les paramètres DNS de l’adresse IP publique nommée *LBIP-myCluster-NTvm1* dans la variable et supprimer l’adresse IP :

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1  -ResourceGroupName myResourceGroup
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn
Remove-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

#### <a name="azure-cli"></a>Azure CLI

La commande suivante utilise l’interface de ligne de commande Azure Service Fabric pour obtenir les paramètres DNS de l’adresse IP publique nommée *LBIP-myCluster-NTvm1* et supprimer l’adresse IP :

```CLI
az network public-ip show --name LBIP-myCluster-NTvm1 --resource-group myResourceGroup
az network public-ip delete --name LBIP-myCluster-NTvm1 --resource-group myResourceGroup
```

## <a name="update-public-ip-address-related-to-new-scale-set"></a>Mettre à jour l’adresse IP publique associée au nouveau groupe de machines virtuelles identiques

Mettez à jour les paramètres DNS de l’adresse IP publique associée au nouveau groupe de machines virtuelles identiques avec les paramètres DNS de l’adresse IP publique associée à l’ancien groupe de machines virtuelles identiques.

#### <a name="azure-powershell"></a>Azure PowerShell
L’exemple suivant utilise Azure PowerShell pour mettre à jour les paramètres DNS de l’adresse IP publique nommée *LBIP-myCluster-NTvm3* avec les paramètres DNS stockés dans des variables à l’étape précédente :

```powershell
$PublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm3  -ResourceGroupName myResourceGroup
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP
```

#### <a name="azure-cli"></a>Azure CLI

La commande suivante utilise l’interface de ligne de commande Azure Service Fabric pour mettre à jour les paramètres DNS de l’adresse IP publique nommée *LBIP-myCluster-NTvm3* avec les paramètres DNS de l’ancienne adresse IP publique recueillis à l’étape précédente :

```CLI
az network public-ip update --name LBIP-myCluster-NTvm3 --resource-group myResourceGroup --dns-name myCluster
```

## <a name="remove-knowledge-of-service-fabric-node-from-fm"></a>Supprimer la connaissance du nœud Service Fabric dans Failover Manager

Signalez à Service Fabric que les nœuds qui sont désactivés ont été retirés du cluster. (Exécutez cette commande pour toutes les instances de machines virtuelles de l’ancien groupe de machines virtuelles identiques.) (Si le niveau de durabilité de l’ancien groupe de machines virtuelles identiques était Argent ou Or, cette étape ne sera peut-être pas nécessaire, car cette étape est exécutée automatiquement par le système.)

#### <a name="azure-powershell"></a>Azure PowerShell
L’exemple suivant utilise Azure Service Fabric PowerShell pour signaler à Service Fabric que le nœud nommé *NTvm1_0* a été supprimé :

```powershell
Remove-ServiceFabricNodeState -NodeName NTvm1_0
```

#### <a name="azure-cli"></a>Azure CLI

La commande suivante utilise l’interface de ligne de commande Azure Service Fabric pour signaler à Service Fabric que le nœud nommé *NTvm1_0* a été supprimé :

```CLI
sfctl node remove-state --node-name _NTvm1_0
```

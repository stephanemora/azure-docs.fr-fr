---
title: Mise en réseau pour des groupes de machines virtuelles identiques Azure
description: Procédure de configuration de certaines propriétés de mise en réseau avancées pour des groupes de machines virtuelles identiques Azure.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 06/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: ea17d86bb4e5e4a6b5c5106c7d831c6691018b12
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130214735"
---
# <a name="networking-for-azure-virtual-machine-scale-sets"></a>Mise en réseau pour des groupes de machines virtuelles identiques Azure

**S’applique à :** :heavy_check_mark: Groupes identiques uniformes

Lorsque vous déployez un groupe de machines virtuelles identiques Azure via le portail, certaines propriétés de réseau sont définies par défaut, comme un équilibrage de charge Azure avec des règles NAT entrantes. Cet article explique comment utiliser certaines des fonctionnalités avancées de mise en réseau, que vous pouvez configurer avec les groupes identiques.

Vous pouvez configurer toutes les fonctionnalités abordées dans cet article à l’aide des modèles Azure Resource Manager. Des exemples d’interfaces de ligne de commande Azure et Powershell sont également inclus pour les fonctionnalités sélectionnées.

## <a name="accelerated-networking"></a>Mise en réseau accélérée
La mise en réseau accélérée Azure améliore les performances du réseau en activant la virtualisation d’E/S de racine unique (SR-IOV) sur une machine virtuelle. Pour plus d’informations sur la mise en réseau accélérée, consultez Mise en réseau accélérée pour machines virtuelles [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) ou [Linux](../virtual-network/create-vm-accelerated-networking-cli.md). Pour utiliser la mise en réseau accélérée avec des groupes identiques, définissez enableAcceleratedNetworking sur **true** dans les paramètres networkInterfaceConfigurations du groupe identique. Par exemple :

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
      "name": "niconfig1",
      "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
          ...
        ]
      }
    }
   ]
}
```

## <a name="azure-virtual-machine-scale-sets-with-azure-load-balancer"></a>Groupes de machines virtuelles identiques avec Azure Load Balancer
Consultez [Azure Load Balancer et Virtual Machine Scale Sets](../load-balancer/load-balancer-standard-virtual-machine-scale-sets.md) pour en savoir plus sur comment configurer votre Standard Load Balancer avec Virtual Machine Scale Sets en fonction de votre scénario.

## <a name="create-a-scale-set-that-references-an-application-gateway"></a>Créer un groupe identique qui fait référence à une passerelle d’application
Pour créer un groupe identique qui utilise une passerelle d’application, référencez le pool d’adresses principal de la passerelle d’application dans la section ipConfigurations de votre groupe identique, comme dans cette configuration de modèle ARM :

```json
"ipConfigurations": [{
  "name": "{config-name}",
  "properties": {
  "subnet": {
    "id": "{subnet-id}"
  },
  "ApplicationGatewayBackendAddressPools": [{
    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/applicationGateways/{gateway-name}/backendAddressPools/{pool-name}"
  }]
}]
```

>[!NOTE]
> La passerelle d’application doit se trouver dans le même réseau virtuel que le groupe identique, mais elle doit être dans un sous-réseau différent.


## <a name="configurable-dns-settings"></a>Paramètres DNS configurables
Par défaut, les groupes identiques adoptent les paramètres DNS spécifiques du réseau virtuel et du sous-réseau dans lesquels ils ont été créés. Toutefois, vous pouvez configurer les paramètres DNS directement pour un groupe identique.

### <a name="creating-a-scale-set-with-configurable-dns-servers"></a>Création d’un groupe identique avec les serveurs DNS configurables
Pour créer un groupe identique avec une configuration DNS personnalisée à l’aide d’Azure CLI, ajoutez l’argument **--dns-servers** à la commande **vmss create**, suivi des adresses IP des serveurs, séparées par un espace. Par exemple :

```bash
--dns-servers 10.0.0.6 10.0.0.5
```

Pour configurer des serveurs DNS personnalisés dans un modèle Azure, ajoutez une propriété dnsSettings à la section networkInterfaceConfigurations du groupe identique. Par exemple :

```json
"dnsSettings":{
    "dnsServers":["10.0.0.6", "10.0.0.5"]
}
```

### <a name="creating-a-scale-set-with-configurable-virtual-machine-domain-names"></a>Création d’un groupe identique avec des noms de domaine de machines virtuelles configurables
Pour créer un groupe identique avec un nom DNS personnalisé pour des machines virtuelles avec l’interface CLI, ajoutez l’argument **--vm-domain-name** à la commande **créer un groupe de machines virtuelles identiques**, suivi d’une chaîne représentant le nom de domaine.

Pour définir le nom de domaine dans un modèle Azure, ajoutez une propriété **dnsSettings** à la section **networkInterfaceConfigurations** du groupe identique. Par exemple :

```json
"networkProfile": {
  "networkInterfaceConfigurations": [
    {
    "name": "nic1",
    "properties": {
      "primary": true,
      "ipConfigurations": [
      {
        "name": "ip1",
        "properties": {
          "subnet": {
            "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
          },
          "publicIPAddressconfiguration": {
            "name": "publicip",
            "properties": {
            "idleTimeoutInMinutes": 10,
              "dnsSettings": {
                "domainNameLabel": "[parameters('vmssDnsName')]"
              }
            }
          }
        }
      }
    ]
    }
}
```

Pour le nom dns d’une machine virtuelle individuelle, le résultat devrait être similaire à :

```output
<vm><vmindex>.<specifiedVmssDomainNameLabel>
```

## <a name="public-ipv4-per-virtual-machine"></a>IPv4 publique par machine virtuelle
En règle générale, les machines virtuelles des groupes identiques Azure ne nécessitent pas leurs propres adresses IP publiques. Dans la plupart des cas, l’association d’une adresse IP publique à un équilibrage de charge ou à une machine virtuelle individuelle (ou jumpbox) qui achemine les connexions entrantes vers les machines virtuelles de groupes identiques selon les besoins (par exemple, via des règles NAT entrantes) constitue une solution plus économique et plus sûre.

Toutefois, dans certains cas, les machines virtuelles de groupes identiques doivent posséder leurs propres adresses IP publiques. Par exemple, dans le cas des jeux vidéo, lorsqu’une console doit être directement connectée à une machine virtuelle sur Cloud qui procède à un traitement physique du jeu. Autre exemple : lorsque des machines virtuelles doivent établir des connexions externes entre elles, dans différentes régions, dans une base de données distribuée.

### <a name="creating-a-scale-set-with-public-ip-per-virtual-machine"></a>Création d’un groupe identique avec IP public par machine virtuelle
Pour créer un groupe identique qui attribue une adresse IP publique à chaque machine virtuelle avec l’interface CLI, ajoutez le paramètre **--public-ip-per-vm** à la commande **vmss create**.

Pour créer un groupe identique à l’aide d’un modèle Azure, assurez-vous que la version API de la ressource Microsoft.Compute/virtualMachineScaleSets correspond au moins à la version du **30/03/2017**, et ajoutez une propriété JSON **publicIpAddressConfiguration** à la section ipConfigurations du groupe identique. Par exemple :

```json
"publicIpAddressConfiguration": {
    "name": "pub1",
    "properties": {
      "idleTimeoutInMinutes": 15
    }
}
```

Exemple de modèle : [vmss-public-ip-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vmss-public-ip-linux)

### <a name="querying-the-public-ip-addresses-of-the-virtual-machines-in-a-scale-set"></a>Interrogation des adresses IP publiques des machines virtuelles dans un groupe identique
Pour répertorier les adresses IP publiques attribuées à des machines virtuelles d’un groupe identique avec l’interface CLI, utilisez la commande **az vmss list-instance-public-ips**.

Pour lister les adresses IP publiques d’un groupe identique à l’aide de PowerShell, utilisez la commande _Get-AzPublicIpAddress_. Par exemple :

```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -VirtualMachineScaleSetName myvmss
```

Vous pouvez également interroger les adresses IP publiques en référençant directement l’ID de ressource de la configuration d’adresse IP publique. Par exemple :

```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -Name myvmsspip
```

Vous pouvez également afficher les adresses IP publiques attribuées aux machines virtuelles d’un groupe identique en utilisant [Azure Resource Explorer](https://resources.azure.com) ou l’API REST Azure avec la version **30/03/2017** ou ultérieure.

Pour interroger [Azure Resource Explorer](https://resources.azure.com), procédez comme suit :

1. Ouvrez [Azure Resource Explorer](https://resources.azure.com) dans votre navigateur web.
1. Développez vos *abonnements* sur le côté gauche en cliquant sur les commandes *+* en regard de celle-ci. Si vous ne disposez que d’un élément dans vos *abonnements*, il est possible qu’il soit déjà développé.
1. Développez votre abonnement.
1. Développez votre groupe de ressources.
1. Développez les *fournisseurs*.
1. Développez *Microsoft.Compute*.
1. Développez *virtualMachineScaleSets*.
1. Développez votre groupe identique.
1. Cliquez sur *publicipaddresses*.

Pour interroger l’API REST Azure, procédez comme suit :

```bash
GET https://management.azure.com/subscriptions/{your sub ID}/resourceGroups/{RG name}/providers/Microsoft.Compute/virtualMachineScaleSets/{scale set name}/publicipaddresses?api-version=2017-03-30
```

Exemple de sortie provenant d’[Azure Resource Explorer](https://resources.azure.com) et de l’API REST Azure :

```json
{
  "value": [
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/pipvmss/virtualMachines/0/networkInterfaces/pipvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"a64060d5-4dea-4379-a11d-b23cd49a3c8d\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "ee8cb20f-af8e-4cd6-892f-441ae2bf701f",
        "ipAddress": "13.84.190.11",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/0/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    },
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"5f6ff30c-a24c-4818-883c-61ebd5f9eee8\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "036ce266-403f-41bd-8578-d446d7397c2f",
        "ipAddress": "13.84.159.176",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    }
```

## <a name="multiple-ip-addresses-per-nic"></a>Plusieurs adresses IP par carte réseau
Une ou plusieurs configurations IP peuvent être associées à chaque carte réseau attachée à une machine virtuelle, dans un groupe identique. Une adresse IP privée est affectée à chaque configuration. Une ressource d’adresse IP publique peut également être associée à chaque configuration. Pour comprendre combien d’adresses IP peuvent être attribuées à une carte réseau et combien d’adresses IP publiques vous pouvez utiliser dans un abonnement Azure, consultez [Limites Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="multiple-nics-per-virtual-machine"></a>Plusieurs cartes réseau par machine virtuelle
Chaque machine virtuelle peut compter jusqu’à 8 cartes réseau, en fonction de la taille de la machine. Le nombre maximal de cartes réseau par machine est indiqué dans l’[article sur la taille des machines virtuelles](../virtual-machines/sizes.md). Toutes les cartes réseau connectées à une instance de machine virtuelle doivent se connecter au même réseau virtuel. Les cartes réseau peuvent se connecter à différents sous-réseaux, mais tous les sous-réseaux doivent faire partie du même réseau virtuel.

L’exemple suivant est un profil réseau de groupe identique illustrant plusieurs entrées de cartes réseau et plusieurs adresses IP publiques par machine virtuelle :

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
        "name": "nic1",
        "properties": {
            "primary": true,
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        },
        {
        "name": "nic2",
        "properties": {
            "primary": false,
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        }
    ]
}
```

## <a name="nsg--asgs-per-scale-set"></a>Groupes de sécurité réseau et d’application par groupe identique
Des [groupes de sécurité réseau](../virtual-network/network-security-groups-overview.md) vous permettent de filtrer le trafic à destination et en provenance des ressources Azure dans un réseau virtuel Azure à l’aide de règles de sécurité. Des [groupes de sécurité d’application](../virtual-network/network-security-groups-overview.md#application-security-groups) vous permettent de gérer la sécurité du réseau de ressources Azure et de les regrouper pour former une extension de la structure de votre application.

Des groupes de sécurité réseau peuvent être appliqués directement à un groupe identique en ajoutant une référence à la section de configuration de l’interface réseau des propriétés des machines virtuelles du groupe identique.

Des groupes de sécurité d’application peuvent également être spécifiés directement à un groupe identique en ajoutant une référence à la section de configuration ip de l’interface réseau des propriétés des machines virtuelles du groupe identique.

Par exemple :

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": true,
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "applicationSecurityGroups": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/applicationSecurityGroups/', variables('asgName'))]"
                                }
                            ],
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

Pour vérifier que votre groupe de sécurité réseau est associé à votre groupe identique, utilisez la commande `az vmss show`. L’exemple ci-dessous utilise `--query` pour filtrer les résultats et afficher uniquement la section appropriée de la sortie.

```azurecli
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].networkSecurityGroup

[
  {
    "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/nsgName",
    "resourceGroup": "myResourceGroup"
  }
]
```

Pour vérifier que le groupe de sécurité de votre application est associé à votre groupe identique; utilisez la commande `az vmss show`. L’exemple ci-dessous utilise `--query` pour filtrer les résultats et afficher uniquement la section appropriée de la sortie.

```azurecli
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].ipConfigurations[].applicationSecurityGroups

[
  [
    {
      "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationSecurityGroups/asgName",
      "resourceGroup": "myResourceGroup"
    }
  ]
]
```

## <a name="make-networking-updates-to-specific-instances"></a>Effectuer des mises à jour réseau sur des instances spécifiques

Vous pouvez effectuer des mises à jour réseau sur des instances spécifiques du groupe de machines virtuelles identiques.

Vous pouvez effectuer un `PUT` sur l’instance pour mettre à jour la configuration réseau. Vous pouvez l’utiliser pour effectuer des opérations comme l’ajout ou la suppression de cartes réseau, ou la suppression d’une instance d’un pool back-end.

```
PUT https://management.azure.com/subscriptions/.../resourceGroups/vmssnic/providers/Microsoft.Compute/virtualMachineScaleSets/vmssnic/virtualMachines/1/?api-version=2019-07-01
```

L’exemple suivant montre comment ajouter une deuxième configuration IP à votre carte réseau.

1. Obtenez (`GET`) les détails d’une instance de groupe de machines virtuelles identiques.

    ```
    GET https://management.azure.com/subscriptions/.../resourceGroups/vmssnic/providers/Microsoft.Compute/virtualMachineScaleSets/vmssnic/virtualMachines/1/?api-version=2019-07-01
    ```

    *La commande suivante a été simplifiée pour afficher uniquement les paramètres réseau de cet exemple.*

    ```json
    {
      ...
      "properties": {
        ...
        "networkProfileConfiguration": {
          "networkInterfaceConfigurations": [
            {
              "name": "vmssnic-vnet-nic01",
              "properties": {
                "primary": true,
                "enableAcceleratedNetworking": false,
                "networkSecurityGroup": {
                  "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/networkSecurityGroups/basicNsgvmssnic-vnet-nic01"
                },
                "dnsSettings": {
                  "dnsServers": []
                },
                "enableIPForwarding": false,
                "ipConfigurations": [
                  {
                    "name": "vmssnic-vnet-nic01-defaultIpConfiguration",
                    "properties": {
                      "publicIPAddressConfiguration": {
                        "name": "publicIp-vmssnic-vnet-nic01",
                        "properties": {
                          "idleTimeoutInMinutes": 15,
                          "ipTags": [],
                          "publicIPAddressVersion": "IPv4"
                        }
                      },
                      "primary": true,
                      "subnet": {
                        "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/virtualNetworks/vmssnic-vnet/subnets/default"
                      },
                      "privateIPAddressVersion": "IPv4"
                    }
                  }
                ]
              }
            }
          ]
        },
        ...
      }
    }
    ```

2. Effectuez un `PUT` sur l’instance, ce qui entraîne une mise à jour pour ajouter la configuration IP supplémentaire. Cette étape est similaire pour ajouter un `networkInterfaceConfiguration` supplémentaire.


    ```
    PUT https://management.azure.com/subscriptions/.../resourceGroups/vmssnic/providers/Microsoft.Compute/virtualMachineScaleSets/vmssnic/virtualMachines/1/?api-version=2019-07-01
    ```

    *La commande suivante a été simplifiée pour afficher uniquement les paramètres réseau de cet exemple.*

    ```json
      {
      ...
      "properties": {
        ...
        "networkProfileConfiguration": {
          "networkInterfaceConfigurations": [
            {
              "name": "vmssnic-vnet-nic01",
              "properties": {
                "primary": true,
                "enableAcceleratedNetworking": false,
                "networkSecurityGroup": {
                  "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/networkSecurityGroups/basicNsgvmssnic-vnet-nic01"
                },
                "dnsSettings": {
                  "dnsServers": []
                },
                "enableIPForwarding": false,
                "ipConfigurations": [
                  {
                    "name": "vmssnic-vnet-nic01-defaultIpConfiguration",
                    "properties": {
                      "publicIPAddressConfiguration": {
                        "name": "publicIp-vmssnic-vnet-nic01",
                        "properties": {
                          "idleTimeoutInMinutes": 15,
                          "ipTags": [],
                          "publicIPAddressVersion": "IPv4"
                        }
                      },
                      "primary": true,
                      "subnet": {
                        "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/virtualNetworks/vmssnic-vnet/subnets/default"
                      },
                      "privateIPAddressVersion": "IPv4"
                    }
                  },
                  {
                    "name": "my-second-config",
                    "properties": {
                      "subnet": {
                        "id": "/subscriptions/123a1a12-a123-1ab1-12a1-12a1a1234ab1/resourceGroups/vmssnic/providers/Microsoft.Network/virtualNetworks/vmssnic-vnet/subnets/default"
                      },
                      "privateIPAddressVersion": "IPv4"
                    }
                  }
                ]
              }
            }
          ]
        },
        ...
      }
    }
    ```


## <a name="explicit-network-outbound-connectivity-for-flexible-scale-sets"></a>Connectivité réseau sortante explicite pour les groupes identiques Flexibles 

Pour améliorer la sécurité réseau par défaut, les [groupes de machines virtuelles identiques avec une orchestration Flexible](..\virtual-machines\flexible-virtual-machine-scale-sets.md) requièrent que les instances créées implicitement via le profil de mise à l’échelle automatique disposent d’une connectivité sortante définie explicitement à l’aide de l’une des méthodes suivantes : 

- Pour la plupart des scénarios, nous recommandons la [Passerelle NAT attachée au sous-réseau](../virtual-network/nat-gateway/tutorial-create-nat-gateway-portal.md).
- Pour les scénarios assortis d’exigences de sécurité élevées ou lors de l’utilisation d’un Pare-feu Azure ou d’une Appliance virtuelle réseau (NVA), vous pouvez spécifier un Itinéraire personnalisé défini par l’utilisateur en tant que tronçon suivant via le pare-feu. 
- Les instances se trouvent dans le pool principal d’un Équilibreur de charge Azure de Référence SKU standard. 
- Attachez une adresse IP publique à l’interface réseau de l’instance. 

Avec des machines virtuelles à instance unique et des groupes de machines virtuelles identiques avec orchestration uniforme, la connectivité sortante est fournie automatiquement. 

Les scénarios courants nécessitant une connectivité sortante explicite sont les suivants : 

- L’activation d’une machine virtuelle Windows nécessite que vous ayez défini une connectivité sortante à partir de l’instance de machine virtuelle vers le Service de gestion des clés d’activation Windows (KMS). Pour plus d’informations, consultez [Résoudre des problèmes liés à l’activation de machines virtuelles Windows Azure](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems).  
- Accédez aux comptes de stockage ou au Coffre de clés. La connectivité aux services Azure peut également être établie via une [Liaison privée](../private-link/private-link-overview.md). 

Pour plus d’informations sur la définition de connexions sortantes sécurisées, consultez [Accès sortant par défaut dans Azure](../virtual-network/ip-services/default-outbound-access.md).


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les réseaux virtuels Azure, consultez la page [Présentation du réseau virtuel Azure](../virtual-network/virtual-networks-overview.md).
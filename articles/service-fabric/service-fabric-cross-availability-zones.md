---
title: Déployer un cluster Azure Service Fabric entre des Zones de disponibilité | Microsoft Docs
description: Découvrez comment créer un cluster Azure Service Fabric entre des Zones de disponibilité.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: b664c3d655ab45c89a65a0aea31622f57ddc8d9e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "65077454"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Déployer un cluster Azure Service Fabric entre des Zones de disponibilité
Zones de disponibilité dans Azure est une offre à haute disponibilité qui protège vos applications et données contre les défaillances de centre de données. Une Zone de disponibilité est un unique emplacement physique équipé d’alimentation, refroidissement et de mise en réseau dans une région Azure.

Service Fabric prend en charge les clusters qui s’étendent sur les Zones de disponibilité en déployant des types de nœuds qui sont épinglées sur des zones spécifiques. Cela garantit la haute disponibilité de vos applications. Zones de disponibilité Azure sont uniquement disponibles dans certaines régions. Pour plus d’informations, consultez [vue d’ensemble de Zones de disponibilité Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).

Exemples de modèles sont disponibles : [Service Fabric Cross-modèle de la zone de disponibilité](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Recommandé de topologie pour le type de nœud principal de la répartition entre les Zones de disponibilité des clusters Azure Service Fabric
Un cluster Service Fabric répartie entre les Zones de disponibilité garantit la haute disponibilité de l’état du cluster. Pour couvrir un cluster Service Fabric entre des zones, vous devez créer un type de nœud principal dans chaque Zone de disponibilité pris en charge par la région. Cela sera répartir nœuds initiaux sur chacun des types de nœud principal.

La topologie recommandée pour le type de nœud principal nécessite des ressources décrites ci-dessous :

* Le niveau de fiabilité de cluster défini sur la platine.
* Trois Types de nœud marqué comme principales.
    * Chaque Type de nœud doit être mappé à son propre jeu de mise à l’échelle de machine virtuelle situé dans différentes zones.
    * Chaque jeu de mise à l’échelle de machine virtuelle doit avoir au moins cinq nœuds (durabilité Silver).
* Une seule ressource IP publique qu’à l’aide de la référence (SKU) Standard.
* Une seule ressource Load Balancer à l’aide de la référence (SKU) Standard.
* Un groupe de sécurité réseau, référencé par le sous-réseau dans lequel vous déployez vos machines virtuelles identiques.

>[!NOTE]
> Les machines virtuelles identiques propriété de groupe de placement unique doit être définie sur true, étant donné que Service Fabric ne prend pas en charge un ensemble d’échelle de machine virtuelle unique qui couvre les zones.

 ![Architecture de la Zone de disponibilité d’Azure Service Fabric][sf-architecture]

## <a name="networking-requirements"></a>Configuration requise du réseau
### <a name="public-ip-and-load-balancer-resource"></a>Adresse IP publique et la ressource d’équilibrage de charge
Pour activer les zones de propriété sur une échelle de machine virtuelle définie de ressources, l’équilibreur de charge et les ressources IP référencé par ce jeu de mise à l’échelle de machine virtuelle doivent tous deux utiliser un *Standard* référence (SKU). Création d’un équilibreur de charge ou de la ressource d’adresse IP sans la propriété de référence (SKU) créera un SKU de base, qui ne prend pas en charge les Zones de disponibilité. Un équilibreur de charge de référence (SKU) Standard bloque tout le trafic depuis l’extérieur par défaut ; Pour autoriser le trafic à l’extérieur, un groupe de sécurité réseau doit être déployé sur le sous-réseau.

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> Il n’est pas possible d’effectuer une modification sur place de référence (SKU) sur les ressources d’équilibrage IP et de charge publics. Si vous effectuez une migration à partir des ressources existantes qui ont une référence SKU de base, consultez la section de la migration de cet article.

### <a name="virtual-machine-scale-set-nat-rules"></a>Règles NAT de machines virtuelles identiques
L’équilibrage de charge des règles NAT entrantes doivent correspondre les pools NAT à partir du jeu de mise à l’échelle de machine virtuelle. Chaque jeu de mise à l’échelle de machine virtuelle doit avoir son propre pool NAT entrant.

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Règles de trafic sortant de la référence SKU de Load Balancer standards
Standard de Load Balancer et d’adresse IP publique Standard présentent les nouvelles fonctionnalités et des comportements différents à la connectivité sortante par rapport à l’aide de références SKU de base. Si vous souhaitez une connectivité sortante lorsque vous travaillez avec des références SKU Standard, vous devez explicitement la définir avec des adresses IP publiques Standard ou l’équilibreur de charge public Standard. Pour plus d’informations, consultez [les connexions sortantes](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#snatexhaust) et [Azure Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

>[!NOTE]
> Le modèle standard fait référence à un groupe de sécurité réseau qui autorise tout le trafic sortant par défaut. Le trafic entrant est limité aux ports qui sont requis pour les opérations de gestion de Service Fabric. Les règles de groupe de sécurité réseau peuvent être modifiés pour répondre à vos besoins.

### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>L’activation de zones sur une échelle de machine virtuelle définie
Pour activer une zone, sur une échelle de machine virtuelle définie vous doit inclure les trois valeurs suivantes dans la ressource de jeu de mise à l’échelle de machine virtuelle.

* La première valeur est la **zones** propriété qui spécifie quelle Zone de disponibilité que le jeu de mise à l’échelle de machine virtuelle sera déployé sur.
* La deuxième valeur est la propriété « singlePlacementGroup », qui doit être définie sur true.
* La troisième valeur est la propriété « faultDomainOverride » dans l’extension de jeu de mise à l’échelle de machine virtuelle Service Fabric. La valeur de cette propriété doit inclure la région et la zone dans laquelle ce jeu de mise à l’échelle de machine virtuelle sera placé. Exemple : « faultDomainOverride » : « eastus/az1 », toutes les machines virtuelles identiques ressources doivent être placés dans la même région car les clusters Azure Service Fabric n’ont pas entre la prise en charge de la région.

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric.Test",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "eastus/az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>L’activation de plusieurs Types de nœud principal dans la ressource de Cluster Service Fabric
Pour définir un ou plusieurs types de nœud en tant que principal dans une ressource de cluster, définissez la propriété « isPrimary » sur « true ». Lorsque vous déployez un cluster Service Fabric entre des Zones de disponibilité, vous devez avoir trois types de nœuds dans des zones distinctes.

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Migrer à l’aide de Zones de disponibilité à partir d’un cluster à l’aide d’un équilibreur de charge d’une référence (SKU) et une adresse IP de base référence (SKU)
Pour migrer un cluster, ce qui a été en utilisant un équilibreur de charge et IP avec une référence SKU de base, vous devez d’abord créer une ressource d’équilibreur de charge et l’adresse IP entièrement nouvelle à l’aide de la référence SKU standard. Il n’est pas possible de mettre à jour de ces ressources sur place.

Le nouvel équilibreur de charge et IP doivent être référencée dans les types de nœuds de Zone de disponibilité entre nouveau que vous souhaitez utiliser. Dans l’exemple ci-dessus, trois nouvelles machines virtuelles identiques, jeu de ressources ont été ajoutées dans les zones 1, 2 et 3. Ces machines virtuelles identiques définit la référence de l’équilibreur de charge et les IP nouvellement créé et sont marquées en tant que types de nœud principal dans la ressource de Cluster Service Fabric.

Pour commencer, vous devez ajouter les nouvelles ressources à votre modèle Resource Manager existant. Ces ressources incluent :
* Une ressource IP publique à l’aide de la référence (SKU) Standard.
* Une ressource d’équilibreur de charge à l’aide de la référence (SKU) Standard.
* Un groupe de sécurité réseau, référencé par le sous-réseau dans lequel vous déployez vos machines virtuelles identiques.
* Trois types de nœud marqué comme principales.
    * Chaque type de nœud doit être mappé à son propre jeu de mise à l’échelle de machine virtuelle situé dans différentes zones.
    * Chaque jeu de mise à l’échelle de machine virtuelle doit avoir au moins cinq nœuds (durabilité Silver).

Vous trouverez un exemple de ces ressources dans le [exemple de modèle](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure).

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

Une fois les déploiement des ressources terminé, vous pouvez commencer à désactiver les nœuds dans le type de nœud principal du cluster d’origine. Comme les nœuds sont désactivés, les services système migre vers le nouveau type de nœud principal qui a été déployé à l’étape précédente.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Une fois que tous les nœuds sont désactivés, les services système seront exécuteront sur le type de nœud principal, qui est réparti entre les zones. Vous pouvez ensuite supprimer les nœuds de désactivé à partir du cluster. Une fois que les nœuds sont supprimés, vous pouvez supprimer l’adresse IP d’origine, un équilibreur de charge, et les ressources de machines virtuelles identiques.

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Vous devez ensuite supprimer les références à ces ressources à partir du modèle Resource Manager que vous aviez déployé.

La dernière étape implique la mise à jour le nom DNS et l’adresse IP publique.

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png

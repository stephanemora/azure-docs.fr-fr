---
title: Meilleures pratiques de mise en réseau Azure Service Fabric
description: Meilleures pratiques et considérations relatives à la conception pour la gestion de la connectivité réseau à l’aide d’Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 853e53d32f87f81e5db587de2654f83037930da7
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261143"
---
# <a name="networking"></a>Mise en réseau

Lorsque vous créez et gérez des clusters Azure Service Fabric, vous assurez une connectivité réseau à vos nœuds et à vos applications. Les ressources de mise en réseau comprennent les plages d'adresses IP, les réseaux virtuels, les équilibreurs de charge et les groupes de sécurité réseau. Dans cet article, vous découvrirez les meilleures pratiques liées à ces ressources.

Consultez [Modèles de mise en réseau de Service Fabric](./service-fabric-patterns-networking.md) Azure pour savoir comment créer des clusters utilisant les fonctionnalités suivantes : réseau virtuel ou sous-réseau existant, adresse IP publique statique, équilibreur de charge interne uniquement, ou équilibreur de charge interne et externe.

## <a name="infrastructure-networking"></a>Mise en réseau de l’infrastructure
Optimisez les performances de votre machine virtuelle avec la mise en réseau accélérée, en déclarant la propriété enableAcceleratedNetworking dans votre modèle Resource Manager. L'extrait suivant fait partie d'un groupe de machines virtuelles identiques NetworkInterfaceConfigurations qui permet la mise en réseau accélérée :

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
Un cluster Service Fabric peut être configuré sur [Linux avec mise en réseau accélérée](../virtual-network/create-vm-accelerated-networking-cli.md), et [Windows avec mise en réseau accélérée](../virtual-network/create-vm-accelerated-networking-powershell.md).

La mise en réseau accélérée est prise en charge pour les références SKU de la série de machines virtuelles Azure : D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 et Ms/Mms. La mise en réseau accélérée a été testée avec succès à l’aide de la référence SKU Standard_DS8_v3 pour un cluster Service Fabric Windows le 23/01/2019 et à l'aide de la référence SKU Standard_DS12_v2 pour un cluster Service Fabric Linux le 29/01/2019.

Pour activer la mise en réseau accélérée sur un cluster Service Fabric existant, vous devez d’abord [mettre à l'échelle ce cluster en ajoutant un groupe de machines virtuelles identiques](./virtual-machine-scale-set-scale-node-type-scale-out.md), afin d'effectuer ce qui suit :
1. Approvisionner un type de nœud avec mise en réseau accélérée activée
2. Migrer vos services et leur état vers le type de nœud approvisionné avec mise en réseau accélérée activée

La mise à l’échelle de l’infrastructure est requise pour activer la mise en réseau accélérée sur un cluster existant. En effet, l'activation de la mise en réseau accélérée peut entraîner un temps d’arrêt, car elle implique que toutes les machines virtuelles d'un groupe à haute disponibilité soient [arrêtées et libérées avant d'activer la mise en réseau accélérée sur une carte réseau existante](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Mise en réseau de cluster

* Pour déployer des clusters Service Fabric dans un réseau virtuel existant, suivez la procédure décrite dans [Modèles de mise en réseau de Service Fabric](./service-fabric-patterns-networking.md).

* Les groupes de sécurité réseau (NSG) sont recommandés pour les types de nœuds qui limitent le trafic entrant et sortant de leur cluster. Assurez-vous que les ports nécessaires sont ouverts dans les groupes de sécurité réseau. Par exemple : ![Règles de groupe de sécurité réseau Service Fabric][NSGSetup]

* Le type de nœud principal, qui contient les services système Service Fabric n'est pas tenu d’être exposé via l’équilibreur de charge externe et peut être exposé par un [équilibreur de charge interne](./service-fabric-patterns-networking.md#internal-only-load-balancer).

* Utilisez une [adresse IP publique statique](./service-fabric-patterns-networking.md#static-public-ip-address-1) pour votre cluster.

## <a name="application-networking"></a>Mise en réseau de l’application

* Pour exécuter des charges de travail de conteneur Windows, utilisez le [mode de mise en réseau ouvert](./service-fabric-networking-modes.md#set-up-open-networking-mode) afin de simplifier la communication de service à service.

* Utilisez un proxy inversé tel que [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) ou le [proxy inversé Service Fabric](./service-fabric-reverseproxy.md) pour exposer les ports d’application courants tels que 80 ou 443.

* Pour les conteneurs Windows hébergés sur des machines isolées sous « air gap » qui ne peuvent pas extraire des couches de base du stockage cloud Azure, remplacez le comportement de couche étrangère, en utilisant l’indicateur [--allow-nondistributable-artifacts](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) dans le démon Docker.

## <a name="next-steps"></a>Étapes suivantes

* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Windows Server : [Création de clusters Service Fabric pour Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Linux : [Créer un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* En savoir plus sur les [options de prise en charge de Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png

---
title: Meilleures pratiques de mise en réseau Azure Service Fabric | Microsoft Docs
description: Meilleures pratiques de gestion de la mise en réseau Service Fabric
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: d221b828624e649a0d04a89c4394fe5a7fa857dd
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237320"
---
# <a name="networking"></a>Mise en réseau

Lorsque vous créez et gérez des clusters Azure Service Fabric, vous assurez une connectivité réseau à vos nœuds et à vos applications. Les ressources de mise en réseau comprennent les plages d'adresses IP, les réseaux virtuels, les équilibreurs de charge et les groupes de sécurité réseau. Dans cet article, vous découvrirez les meilleures pratiques liées à ces ressources.

Consultez [Modèles de mise en réseau de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking) Azure pour savoir comment créer des clusters utilisant les fonctionnalités suivantes : réseau virtuel ou sous-réseau existant, adresse IP publique statique, équilibreur de charge interne uniquement, ou équilibreur de charge interne et externe.

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
Un cluster Service Fabric peut être configuré sur [Linux avec mise en réseau accélérée](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli), et [Windows avec mise en réseau accélérée](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).

La mise en réseau accélérée est prise en charge pour les références SKU de la série de machines virtuelles Azure : D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 et Ms/Mms. La mise en réseau accélérée a été testée avec succès à l’aide de la référence SKU Standard_DS8_v3 pour un cluster Service Fabric Windows le 23/01/2019 et à l'aide de la référence SKU Standard_DS12_v2 pour un cluster Service Fabric Linux le 29/01/2019.

Pour activer la mise en réseau accélérée sur un cluster Service Fabric existant, vous devez d’abord [mettre à l'échelle ce cluster en ajoutant un groupe de machines virtuelles identiques](https://docs.microsoft.com/azure/service-fabric/virtual-machine-scale-set-scale-node-type-scale-out), afin d'effectuer ce qui suit :
1. Approvisionner un type de nœud avec mise en réseau accélérée activée
2. Migrer vos services et leur état vers le type de nœud approvisionné avec mise en réseau accélérée activée

La mise à l’échelle de l’infrastructure est requise pour activer la mise en réseau accélérée sur un cluster existant. En effet, l'activation de la mise en réseau accélérée peut entraîner un temps d’arrêt, car elle implique que toutes les machines virtuelles d'un groupe à haute disponibilité soient [arrêtées et libérées avant d'activer la mise en réseau accélérée sur une carte réseau existante](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Mise en réseau de cluster

* Pour déployer des clusters Service Fabric dans un réseau virtuel existant, suivez la procédure décrite dans [Modèles de mise en réseau de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

* Les groupes de sécurité réseau (NSG) sont recommandés pour les types de nœuds qui limitent le trafic entrant et sortant de leur cluster. Assurez-vous que les ports nécessaires sont ouverts dans les groupes de sécurité réseau. Exemple : ![Règles de groupe de sécurité réseau Service Fabric][NSGSetup]

* Le type de nœud principal, qui contient les services système Service Fabric n'est pas tenu d’être exposé via l’équilibreur de charge externe et peut être exposé par un [équilibreur de charge interne](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#internal-only-load-balancer).

* Utilisez une [adresse IP publique statique](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking#static-public-ip-address-1) pour votre cluster.

## <a name="application-networking"></a>Mise en réseau de l’application

* Pour exécuter des charges de travail de conteneur Windows, utilisez le [mode de mise en réseau ouvert](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) afin de simplifier la communication de service à service.

* Utilisez un proxy inversé tel que [Traefik](https://docs.traefik.io/configuration/backends/servicefabric/) ou le [proxy inversé Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy) pour exposer les ports d’application courants tels que 80 ou 443.

* Pour les conteneurs Windows hébergés sur des machines air-ENTROUVERTE qui ne peut pas extraire les calques de base à partir du stockage cloud Azure, remplacer le comportement de la couche étrangère, à l’aide de la [--artefacts nondistributable autoriser](https://docs.microsoft.com/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) indicateur dans le démon Docker.

## <a name="next-steps"></a>Étapes suivantes

* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Windows Server : [Création de clusters Service Fabric pour Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Linux : [Créer un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* En savoir plus sur les [options de prise en charge de Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png

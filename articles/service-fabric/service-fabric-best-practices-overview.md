---
title: Bonnes pratiques relatives aux applications et aux clusters Azure Service Fabric
description: Bonnes pratiques et considérations relatives à la conception pour développer des clusters, applications et services à l’aide d’Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551775"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Bonnes pratiques relatives aux applications et aux clusters Azure Service Fabric

Cet article fournit des liens vers les bonnes pratiques de gestion des clusters et des applications Azure Service Fabric. Nous vous recommandons vivement d’implémenter ces pratiques afin d’optimiser la fiabilité de votre environnement de production. Utilisez l’un des [modèles de cluster Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates) pour commencer à concevoir votre solution de production, ou mettez à jour votre modèle existant pour incorporer ces pratiques.

## <a name="security"></a>Sécurité

* [Bonnes pratiques de sécurité](service-fabric-best-practices-security.md)

## <a name="networking"></a>Mise en réseau

* [Bonnes pratiques relatives aux réseaux](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Planification et mise à l’échelle de la capacité de calcul

* [Bonnes pratiques relatives à la mise à l’échelle de la capacité de calcul](service-fabric-best-practices-capacity-scaling.md)
* [Planification de la capacité de calcul](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infrastructure as code

* [Bonnes pratiques relatives à l’implémentation de l’infrastructure sous forme de code](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Surveillance et diagnostics

* [Bonnes pratiques relatives à la supervision et au diagnostic des clusters](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Conception des applications

* [Bonnes pratiques relatives à la conception des applications](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Liste de contrôle

Après avoir implémenté les pratiques suggérées dans les sections précédentes, vérifiez que vous avez intégré toutes les bonnes pratiques figurant dans la liste de contrôle de disponibilité de production :
* [Liste de contrôle de disponibilité de production Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Étapes suivantes

* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Windows Server : [Création de clusters Service Fabric pour Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Linux : [Créer un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Résoudre les problèmes liés à Service Fabric : [Guides de résolution des problèmes](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
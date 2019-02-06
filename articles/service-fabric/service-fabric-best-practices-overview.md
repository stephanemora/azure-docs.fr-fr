---
title: Bonnes pratiques relatives aux applications et aux clusters Azure Service Fabric | Microsoft Docs
description: Bonnes pratiques relatives à la gestion des clusters et des applications Service Fabric
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: jeanpaul.connock
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 06240ac08a12b67e95b4cb9b9a33fcca32de45a8
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54914427"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Bonnes pratiques relatives aux applications et aux clusters Azure Service Fabric

Pour la gestion de vos clusters et de vos applications Azure Service Fabric, il est fortement recommandé d’effectuer certaines opérations dans le but d’optimiser la fiabilité de votre environnement de production. Effectuez les opérations décrites dans ce document, puis sélectionnez l’un de nos [exemples de modèles de clusters Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates) pour concevoir votre solution de production ou modifier votre modèle existant afin d’incorporer ces pratiques.

## <a name="security"></a>Sécurité 

* [Bonnes pratiques de sécurité](service-fabric-best-practices-security.md)

## <a name="networking"></a>Réseau

* [Bonnes pratiques relatives aux réseaux](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Planification et mise à l’échelle de la capacité de calcul

* [Bonnes pratiques relatives à la mise à l’échelle de la capacité de calcul](service-fabric-best-practices-capacity-scaling.md)
* [Planification de la capacité de calcul](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infrastructure as code

* [Bonnes pratiques relatives à l’implémentation de l’infrastructure sous forme de code](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Surveillance et diagnostics

* [Bonnes pratiques relatives à la supervision et au diagnostic des clusters](service-fabric-best-practices-monitoring.md)

## <a name="checklist"></a>Liste de contrôle

Une fois que vous aurez lu toutes les sections ci-dessus, vérifiez que vous avez bien intégré toutes les bonnes pratiques de la check-list de la préparation à la production :
* [Check-list de la préparation à la production Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Étapes suivantes

* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Windows Server : [Création de clusters Service Fabric pour Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Linux : [Créer un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Résolution des problèmes : [Guide de résolution des problèmes liés à Service Fabric](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
---
title: Bonnes pratiques relatives aux applications et aux clusters Azure Service Fabric | Microsoft Docs
description: Bonnes pratiques relatives à la gestion des clusters et des applications Service Fabric
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
ms.openlocfilehash: a0174a5442de7a10e45e9dc2a2f43f5f401d1a69
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803173"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Bonnes pratiques relatives aux applications et aux clusters Azure Service Fabric

Pour la gestion de vos clusters et de vos applications Azure Service Fabric, il est fortement recommandé d’effectuer certaines opérations dans le but d’optimiser la fiabilité de votre environnement de production. Effectuez les opérations décrites dans ce document, puis sélectionnez l’un de nos [exemples de modèles de clusters Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates) pour concevoir votre solution de production ou modifier votre modèle existant afin d’incorporer ces pratiques.

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

## <a name="checklist"></a>Liste de contrôle

Une fois que vous aurez lu toutes les sections ci-dessus, vérifiez que vous avez bien intégré toutes les bonnes pratiques de la check-list de la préparation à la production :
* [Check-list de la préparation à la production Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Étapes suivantes

* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Windows Server : [Création de clusters Service Fabric pour Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Linux : [Créer un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Résolution des problèmes : [Guide de résolution des problèmes liés à Service Fabric](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
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
ms.date: 04/24/2019
ms.author: pepogors
ms.openlocfilehash: 051d6b1129724ce4e8a67bde4e56ebe61cd832f3
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231371"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Bonnes pratiques relatives aux applications et aux clusters Azure Service Fabric

Pour gérer des clusters et des applications Azure Service Fabric avec succès, il existe des opérations que nous vous recommandons vivement de que procéder pour optimiser la fiabilité de votre environnement de production ; Veuillez effectuer les opérations définies dans ce document et sélectionnez une de nos [modèles de Cluster Azure exemples Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates) pour commencer à concevoir votre solution de production ou de modifier votre modèle existant pour incorporer ces pratiques.

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

* [Meilleures pratiques pour la conception de l’application](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Liste de contrôle

Une fois que vous aurez lu toutes les sections ci-dessus, vérifiez que vous avez bien intégré toutes les bonnes pratiques de la check-list de la préparation à la production :
* [Check-list de la préparation à la production Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Étapes suivantes

* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Windows Server : [Création de clusters Service Fabric pour Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Créez un cluster sur des machines virtuelles ou des ordinateurs exécutant Linux : [Créer un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Résolution des problèmes : [Guide de résolution des problèmes liés à Service Fabric](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
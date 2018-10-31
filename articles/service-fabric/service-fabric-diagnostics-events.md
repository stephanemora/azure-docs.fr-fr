---
title: Événements Azure Service Fabric | Microsoft Docs
description: En savoir plus sur les événements Service Fabric fournis pour vous aider à surveiller votre cluster Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: ca63d67f6d7c19b4ca6928c4cc0f9ccb06eace2b
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49402979"
---
# <a name="service-fabric-events"></a>Événements de Service Fabric 

La plateforme Service Fabric écrit plusieurs événements structurés pour les activités opérationnelles clés qui se déroulent à l’intérieur de votre cluster. Cela va des mises à niveau de cluster aux décisions de placement des réplicas. Chaque événement exposé par Service Fabric est mappé à l’une des entités suivantes dans le cluster :
* Cluster
* Application
* de diffusion en continu
* Partition
* Réplica 
* Conteneur

Pour afficher la liste complète des événements exposés par la plateforme : [Liste des événements Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

Voici quelques exemples de scénarios importants pour lesquels vous devez voir des événements dans votre cluster. 
1. Événements de cycle de vie de nœud : à mesure que les nœuds montent, descendent, sont activés/désactivés ou redémarrés, les événements sont exposés vous montrant ce qui s’est passé. Ils vous aident à identifier s’il existe un problème lié à la machine proprement dite ou si une API a été appelée par le biais de Service Fabric pour modifier l’état d’un nœud.
1. Mise à niveau de cluster : lorsque le cluster est mis à niveau (modification de la configuration ou de la version de Service Fabric), vous pouvez observer le lancement de la mise à niveau, le passage par chacun de vos domaines de mise à niveau et l’achèvement (ou restauration). 
1. Mises à niveau de l’application : comme pour les mises à niveau de cluster, un ensemble complet d’événements se produisent au cours de la mise à niveau. Ces événements peuvent être utiles pour comprendre quand une mise à niveau a été planifiée, l’état actuel d’une mise à niveau et la séquence globale d’événements. Cela est utile pour vérifier les mises à niveau qui ont été déployées avec succès.
1. Déploiement/Suppression de service/application : des événements sont créés ou supprimés pour chaque application, service et conteneur.
1. Déplacements de partition (reconfiguration) : à chaque fois qu’une partition avec état subit une reconfiguration (modification dans le jeu de réplicas), un événement est consigné. Cela est utile si vous essayez de comprendre la fréquence à laquelle votre jeu de réplicas de partition est modifié, ou de suivre le nœud qui exécutait votre réplica principal à un moment dans le temps.
1. Événements de chaos : lors de l’utilisation du service [Chaos](service-fabric-controlled-chaos.md) de Service Fabric, vous voyez des événements à chaque démarrage ou arrêt du service ou lorsqu’une erreur est injectée dans le système.
1. Événements d’intégrité : Service Fabric expose des événements d’intégrité à chaque fois qu’un rapport d’intégrité de type Avertissement ou Erreur est créé, qu’une entité repasse à un état d’intégrité OK ou qu’un rapport d’intégrité arrive à expiration. Ces événements sont très utiles pour effectuer le suivi de l’historique des statistiques d’intégrité d’une entité. 

## <a name="how-to-access-events"></a>Accès à des événements

Vous pouvez accéder aux événements Service Fabric de différentes façons :
* Via le canal opérationnel. Ils peuvent être collectés via l’extension Azure Diagnostics et envoyés à une table de stockage pour consommation ou ingestion dans un outil tel qu’Azure Log Analytics. Lorsque l’option « Diagnostics » est activée pour un cluster, l’agent Azure Diagnostics est déployé sur votre cluster et il est par défaut configuré pour lire les journaux à partir du canal opérationnel. Pour savoir comment modifier la configuration des diagnostics de votre cluster afin de sélectionner plus de journaux ou de compteurs de performances, consultez [Agent Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md). 
* Via les API Rest du service EventStore qui vous permettent d’interroger le cluster, directement ou via la bibliothèque de client Service Fabric. Consultez [Query EventStore APIs for cluster events](service-fabric-diagnostics-eventstore-query.md) (Interroger les API EventStore pour rechercher les événements de cluster).

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la surveillance du cluster, consultez [Monitoring du cluster et de la plateforme](service-fabric-diagnostics-event-generation-infra.md).
* Pour en savoir plus sur le service EventStore, consultez [Vue d’ensemble du service EventStore](service-fabric-diagnostics-eventstore.md).

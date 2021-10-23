---
title: 'Surveiller les machines virtuelles avec Azure Monitor : Sécurité'
description: Découvrez les services de surveillance de la sécurité des machines virtuelles et leur relation avec Azure Monitor.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 793c4a365f2dc04d0bb138e816a6b31aa68042e8
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177381"
---
# <a name="monitor-virtual-machines-with-azure-monitor-security-monitoring"></a>Surveiller les machines virtuelles avec Azure Monitor : Surveillance de la sécurité
Cet article fait partie du scénario [Surveiller les machines virtuelles et leurs charges de travail dans Azure Monitor](monitor-virtual-machine.md). Il décrit les services Azure qui permettent de surveiller la sécurité de vos machines virtuelles et leur relation avec Azure Monitor. Azure Monitor a été conçu pour surveiller la disponibilité et les performances de vos machines virtuelles et d'autres ressources cloud. Tandis que les données opérationnelles stockées dans Azure Monitor peuvent servir à l'examen des incidents de sécurité, d'autres services Azure permettent de surveiller la sécurité. 

> [!IMPORTANT]
> Les services de sécurité génèrent leurs propres coûts, indépendamment d'Azure Monitor. Avant de configurer ces services, consultez les informations tarifaires correspondantes afin de déterminer l'investissement à effectuer pour les utiliser.

## <a name="azure-services-for-security-monitoring"></a>Services Azure dédiés à la surveillance de la sécurité
Azure Monitor se concentre sur les données opérationnelles telles que les journaux d'activité, les métriques et les sources prises en charge par Log Analytics, comme les événements Windows (à l'exception des événements de sécurité), les compteurs de performances, les journaux et Syslog. Dans Azure, la surveillance de la sécurité est assurée par Azure Security Center et Azure Sentinel. Chacun de ces services génère des coûts supplémentaires. Vous devez donc déterminer la valeur qu'ils peuvent avoir dans votre environnement avant de les implémenter.


## <a name="integration-with-azure-monitor"></a>Intégration avec Azure Monitor
Le tableau suivant répertorie les points d'intégration d'Azure Monitor avec les services de sécurité. Tous les services utilisent le même agent Log Analytics, ce qui réduit la complexité car aucun autre composant n'est déployé sur vos machines virtuelles. Security Center et Azure Sentinel stockent leurs données dans un espace de travail Log Analytics afin que vous puissiez utiliser des requêtes de journal pour mettre en corrélation les données collectées par les différents services. Vous pouvez également créer un classeur personnalisé qui combine les données de sécurité et les données de disponibilité et de performances au sein d'une même vue.

| Point d'intégration       | Azure Monitor | Azure Security Center | Azure Sentinel | Defender pour point de terminaison |
|:---|:---|:---|:---|:---|
| Collecte les événements de sécurité     |   | X | X | X |
| Stocke les données dans un espace de travail Log Analytics | X | X | X |   | 
| Utilise l'agent Log Analytics     | X | X | X | X | 



## <a name="agent-deployment"></a>Déploiement des agents
Vous pouvez configurer Security Center pour déployer automatiquement l'agent Log Analytics sur les machines virtuelles Azure. Bien que cela puisse sembler redondant avec Azure Monitor qui déploie le même agent, vous pouvez activer les deux, car ils effectueront chacun leur propre configuration. Par exemple, si Security Center tente d'approvisionner une machine qui est déjà surveillée par Azure Monitor, il utilisera l'agent déjà installé et ajoutera la configuration de l'espace de travail Security Center.

## <a name="next-steps"></a>Étapes suivantes

* [Analyser les données d’analyse collectées pour les machines virtuelles](monitor-virtual-machine-analyze.md)
* [Créer des alertes à partir de données collectées](monitor-virtual-machine-alerts.md)

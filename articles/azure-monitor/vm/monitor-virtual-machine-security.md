---
title: 'Surveiller les machines virtuelles avec Azure Monitor : Sécurité'
description: Découvrez les services de surveillance de la sécurité des machines virtuelles et leur relation avec Azure Monitor.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 8f32be602ac1ba8ea7bb5805ca8df120b0af917e
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674277"
---
# <a name="monitor-virtual-machines-with-azure-monitor-security-monitoring"></a>Surveiller les machines virtuelles avec Azure Monitor : Surveillance de la sécurité
Cet article fait partie du scénario [Surveiller les machines virtuelles et leurs charges de travail dans Azure Monitor](monitor-virtual-machine.md). Il décrit les services Azure qui permettent de surveiller la sécurité de vos machines virtuelles et leur relation avec Azure Monitor. Azure Monitor a été conçu pour surveiller la disponibilité et les performances de vos machines virtuelles et d'autres ressources cloud. Tandis que les données opérationnelles stockées dans Azure Monitor peuvent servir à l'examen des incidents de sécurité, d'autres services Azure permettent de surveiller la sécurité. 

> [!IMPORTANT]
> Les services de sécurité génèrent leurs propres coûts, indépendamment d'Azure Monitor. Avant de configurer ces services, consultez les informations tarifaires correspondantes afin de déterminer l'investissement à effectuer pour les utiliser.

## <a name="azure-services-for-security-monitoring"></a>Services Azure dédiés à la surveillance de la sécurité
Azure Monitor se concentre sur les données opérationnelles telles que les journaux d'activité, les métriques et les sources prises en charge par Log Analytics, comme les événements Windows (à l'exception des événements de sécurité), les compteurs de performances, les journaux et Syslog. Dans Azure, la surveillance de la sécurité est assurée par Azure Security Center et Azure Sentinel. Chacun de ces services génère des coûts supplémentaires. Vous devez donc déterminer la valeur qu'ils peuvent avoir dans votre environnement avant de les implémenter.

[Azure Security Center](../../security-center/security-center-introduction.md) collecte des informations sur les ressources Azure et les serveurs hybrides. Bien que Security Center puisse collecter des événements de sécurité, il se concentre sur la collecte des données d'inventaire, des résultats des analyses d'évaluation et des audits des stratégies pour mettre en évidence les vulnérabilités et recommander des actions correctives. Parmi les fonctionnalités notables figurent une carte interactive du réseau, l'accès juste-à-temps aux machines virtuelles, le renforcement adaptatif du réseau et les contrôles d'application adaptatifs pour bloquer les exécutables suspects.

[Azure Defender pour les serveurs](../../security-center/azure-defender.md) est la solution d'évaluation des serveurs fournie par Security Center. Defender pour les serveurs peut envoyer des événements de sécurité Windows à Log Analytics. Security Center ne s'appuie pas sur les événements de sécurité Windows pour les alertes ou les analyses. Cette fonctionnalité permet un archivage centralisé des événements à des fins d'examen ou autres.

[Azure Sentinel](../../sentinel/overview.md) est une solution de type SIEM (Security Information and Event Management) et SOAR (Security Orchestrated Automated Response). Sentinel collecte des données de sécurité à partir d'un large éventail de sources Microsoft et tierces pour fournir des alertes, une visualisation et une automatisation. Cette solution vise à consolider le plus grand nombre possible de journaux de sécurité, y compris les événements de sécurité Windows. Azure Sentinel peut également collecter des journaux d'événements de sécurité Windows, et partage généralement un espace de travail Log Analytics avec Security Center. Les événements de sécurité ne peuvent être collectés depuis Azure Sentinel ou Security Center que lorsqu'ils partagent le même espace de travail. Contrairement à Security Center, les événements de sécurité constituent un composant clé des alertes et de l'analyse dans Azure Sentinel.

[Defender pour point de terminaison](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint) est une plateforme dédiée à la sécurité des points de terminaison d'entreprise conçue pour aider les réseaux d'entreprise à prévenir, détecter, examiner et traiter les menaces avancées. Il a été conçu en mettant l'accent sur la protection des appareils des utilisateurs de Windows. Defender pour point de terminaison surveille les stations de travail, les serveurs, les tablettes et les téléphones portables, tous systèmes d'exploitation confondus, pour détecter les problèmes de sécurité et les vulnérabilités. Defender pour point de terminaison s'appuie sur Microsoft Endpoint Manager pour collecter des données et fournir des évaluations de sécurité. La collecte des données repose principalement sur les journaux de suivi ETW, et celles-ci sont stockées dans un espace de travail isolé.

## <a name="integration-with-azure-monitor"></a>Intégration avec Azure Monitor
Le tableau suivant répertorie les points d'intégration d'Azure Monitor avec les services de sécurité. Tous les services utilisent le même agent Log Analytics, ce qui réduit la complexité car aucun autre composant n'est déployé sur vos machines virtuelles. Security Center et Azure Sentinel stockent leurs données dans un espace de travail Log Analytics afin que vous puissiez utiliser des requêtes de journal pour mettre en corrélation les données collectées par les différents services. Vous pouvez également créer un classeur personnalisé qui combine les données de sécurité et les données de disponibilité et de performances au sein d'une même vue.

| Point d'intégration       | Azure Monitor | Azure Security Center | Azure Sentinel | Defender pour point de terminaison |
|:---|:---|:---|:---|:---|
| Collecte les événements de sécurité     |   | X | X | X |
| Stocke les données dans un espace de travail Log Analytics | X | X | X |   | 
| Utilise l'agent Log Analytics     | X | X | X | X | 


## <a name="workspace-design-considerations"></a>Considérations relatives à la conception de l'espace de travail
Comme décrit dans [Surveiller les machines virtuelles avec Azure Monitor : Configurer la surveillance](monitor-virtual-machine-configure.md#create-and-prepare-a-log-analytics-workspace), Azure Monitor et les services de sécurité ont besoin d'un espace de travail Log Analytics. Selon vos exigences particulières, vous pouvez choisir de partager un espace de travail commun ou de séparer vos données de disponibilité et de performances de vos données de sécurité. Pour obtenir des détails complets sur la logique à prendre en compte pour concevoir une configuration d'espace de travail, consultez [Concevoir votre déploiement de journaux Azure Monitor](../logs/design-logs-deployment.md).

## <a name="agent-deployment"></a>Déploiement des agents
Vous pouvez configurer Security Center pour déployer automatiquement l'agent Log Analytics sur les machines virtuelles Azure. Bien que cela puisse sembler redondant avec Azure Monitor qui déploie le même agent, vous pouvez activer les deux, car ils effectueront chacun leur propre configuration. Par exemple, si Security Center tente d'approvisionner une machine qui est déjà surveillée par Azure Monitor, il utilisera l'agent déjà installé et ajoutera la configuration de l'espace de travail Security Center.

## <a name="next-steps"></a>Étapes suivantes

* [Analyser les données de surveillance collectées pour les machines virtuelles](monitor-virtual-machine-analyze.md)
* [Créer des alertes à partir de données collectées](monitor-virtual-machine-alerts.md)

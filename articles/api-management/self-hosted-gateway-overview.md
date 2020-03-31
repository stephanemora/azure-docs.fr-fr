---
title: Vue d’ensemble de la passerelle Gestion des API Azure auto-hébergée | Microsoft Docs
description: Découvrez comment la passerelle Gestion des API Azure auto-hébergée aide les organisations à gérer les API dans les environnements hybrides et multiclouds.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 415f0e209e607a863d715b1a66a2435603a662f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73510554"
---
# <a name="self-hosted-api-management-gateway-overview"></a>Vue d’ensemble de la passerelle Gestion des API auto-hébergée

Cet article explique comment la fonctionnalité de passerelle auto-hébergée active la gestion des API hybrides et multiclouds, présente son architecture de haut niveau et met en évidence ses fonctionnalités fondamentales.

> [!NOTE]
> La fonctionnalité de passerelle auto-hébergée est en version préliminaire. Pendant la version préliminaire, la passerelle auto-hébergée est disponible uniquement pour les niveaux Développeur et Premium, sans frais supplémentaires. Le niveau Développeur est limité à un seul déploiement de passerelle auto-hébergée.

## <a name="hybrid-and-multi-cloud-api-management"></a>Gestion des API hybrides et multiclouds

La fonctionnalité de passerelle auto-hébergée étend la prise en charge Gestion des API pour les environnements hybrides et multiclouds, et permet aux organisations de gérer efficacement et en toute sécurité les API hébergées localement et entre les clouds à partir d’un seul service de gestion des API dans Azure.

Avec la passerelle auto-hébergée, les clients ont la possibilité de déployer une version en conteneur du composant de la passerelle Gestion des API dans les mêmes environnements que ceux où ils hébergent leurs API. Toutes les passerelles auto-hébergées sont managées à partir du service Gestion des API avec lequel elles sont fédérées. Ainsi, les clients bénéficient d’une visibilité et d’une expérience de gestion unifiée sur l’ensemble des API internes et externes. Le placement des passerelles à proximité des API permet aux clients d’optimiser les flux de trafic d’API et de répondre aux exigences de sécurité et de conformité.

Chaque service Gestion des API se compose des éléments clés suivants :

-   Plan de gestion, exposé sous la forme d’une API, utilisé pour configurer le service via le Portail Azure, PowerShell et d’autres mécanismes pris en charge.
-   La passerelle (ou le plan de données) est responsable du proxy des demandes d’API, de l’application des stratégies et de la collecte des données de télémétrie
-   Portail des développeurs utilisé par les développeurs pour découvrir, apprendre et intégrer afin d’utiliser les API

Par défaut, tous ces composants sont déployés dans Azure, ce qui entraîne le flux de tout le trafic API (représenté par des flèches noires pleines sur l’image ci-dessous) dans Azure, quel que soit l’endroit où les serveurs principaux implémentant les API sont hébergés. La simplicité opérationnelle de ce modèle a un coût : une latence accrue, des problèmes de conformité et, dans certains cas, des frais de transfert de données supplémentaires.

![Flux de trafic d’API sans passerelles auto-hébergées](media/self-hosted-gateway-overview/without-gateways.png)

Le déploiement de passerelles auto-hébergées dans les mêmes environnements en tant qu’implémentations d’API back-end et leur ajout au service Gestion des API permet au trafic d’API de circuler directement vers les API back-end. Ceci améliore la latence, optimise les coûts de transfert de données et assure la conformité tout en conservant les avantages d’un point unique de gestion et de découverte de toutes les API au sein de l’organisation, quel que soit l’emplacement d’hébergement de leurs implémentations.

![Flux de trafic d’API avec passerelles auto-hébergées](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Empaquetage et caractéristiques

La passerelle auto-hébergée est une version en conteneur, fonctionnellement équivalente, de la passerelle managée déployée sur Azure dans le cadre de chaque service Gestion des API. La passerelle auto-hébergée est disponible sous la forme d’un conteneur Docker basé sur Linux à partir du Microsoft Container Registry. Il peut être déployé sur Docker, Kubernetes ou toute autre solution d’orchestration de conteneur sur un ordinateur de bureau, un cluster de serveurs ou une infrastructure cloud.

> [!IMPORTANT]
> Certaines fonctionnalités disponibles dans la passerelle managée ne sont pas encore disponibles en version préliminaire. Notamment : Connexion à la stratégie Event Hub, integration de Service Fabric, HTTP/2 en aval. Il n’est pas prévu de rendre un cache intégré disponible dans la passerelle auto-hébergée.

## <a name="connectivity-to-azure"></a>Connectivité à Azure

La passerelle auto-hébergée nécessite une connectivité TCP/IP sortante vers Azure sur le port 443. Chaque passerelle auto-hébergée doit être associée à un seul service Gestion des API et est configurée via son plan de gestion. La passerelle auto-hébergée utilise la connectivité à Azure pour les actions suivantes :

-   Signalement de son état en envoyant des messages de pulsations toutes les minutes
-   Vérification régulière (toutes les 10 secondes) et application des mises à jour de configuration chaque fois qu’elles sont disponibles
-   Envoi de journaux de demandes et de métriques à Azure Monitor, si elle est configurée pour ce faire
-   Envoi d’événements à Application Insights, si elle est configurée pour ce faire

Lorsque la connectivité à Azure est perdue, la passerelle auto-hébergée ne peut plus recevoir de mises à jour de configuration, signaler son état ou charger les données de télémétrie.

La passerelle auto-hébergée est conçue pour « basculer en mode statique » et peut survivre à la perte temporaire de connectivité à Azure. Elle peut être déployée avec ou sans la sauvegarde de configuration locale activée. Dans le premier cas, les passerelles auto-hébergées enregistrent régulièrement une copie de sauvegarde de la configuration sur un volume persistant attaché au conteneur ou au pod.

Lorsque la sauvegarde de la configuration est désactivée et que la connectivité à Azure est interrompue :

-   Les passerelles auto-hébergées qui s’exécutent continuent à fonctionner à l’aide d’une copie en mémoire de la configuration
-   Les passerelles auto-hébergées arrêtées ne peuvent pas démarrer

Lorsque la sauvegarde de la configuration est activée et que la connectivité à Azure est interrompue :

-   Les passerelles auto-hébergées qui s’exécutent continuent à fonctionner à l’aide d’une copie en mémoire de la configuration
-   Les passerelles auto-hébergées arrêtées commencent à utiliser une copie de sauvegarde de la configuration

Lorsque la connectivité est restaurée, chaque passerelle auto-hébergée affectée par la panne se reconnecte automatiquement à son service Gestion des API associé et télécharge toutes les mises à jour de configuration qui se sont produites pendant que la passerelle était « hors connexion ».

## <a name="next-steps"></a>Étapes suivantes

-   [Lire un livre blanc pour plus d’informations sur cette rubrique](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Déployer une passerelle auto-hébergée sur Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [Déployer une passerelle auto-hébergée sur Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)

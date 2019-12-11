---
title: Sécurité des conteneurs dans Azure Security Center | Microsoft Docs
description: Découvrez les fonctionnalités de sécurité des conteneurs d’Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 31e45906dfeafcd2af1651347dde9dc6d3c6fb7c
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769215"
---
# <a name="container-security-in-security-center"></a>Sécurité des conteneurs dans Security Center

Azure Security Center est la solution Azure native pour la sécurité des conteneurs. Security Center constitue aussi un point de contrôle unique de la sécurité de vos charges de travail cloud, machines virtuelles, serveurs et conteneurs.

Cet article décrit comment améliorer, superviser et garantir la sécurité de vos conteneurs et de leurs applications. Il explique de quelle façon Security Center améliore les principaux aspects de la sécurité des conteneurs :

* Gestion des vulnérabilités
* Sécurisation renforcée de l’environnement des conteneurs
* Protection du Runtime

[![Onglet Sécurité des conteneurs d’Azure Security Center](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

## <a name="vulnerability-management---scanning-container-images-preview"></a>Gestion des vulnérabilités : analyse des images conteneurs (préversion)
Pour surveiller votre Azure Container Registry, assurez-vous que vous êtes sur le niveau standard de Security Center (consultez [tarification](https://docs.microsoft.com/azure/security-center/security-center-pricing.md)). Activez ensuite le pack facultatif de registres de conteneurs. Quand une nouvelle image est envoyée (push), Security Center l’analyse avec un scanneur fourni par Qualys, leader des solutions de gestion des vulnérabilités.

Si des problèmes sont détectés par Qualys ou Security Center, vous recevez une notification dans le tableau de bord de Security Center. Pour chaque vulnérabilité, Security Center fournit des recommandations actionnables, accompagnées d’une classification de gravité et de conseils sur la façon de corriger le problème. Pour plus d’informations sur les recommandations de Security Center, consultez la liste des recommandations fournie [ici](recommendations-compute-and-apps.md).

## <a name="environment-hardening"></a>Sécurisation renforcée de l’environnement

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Supervision continue de votre configuration Docker
Azure Security Center identifie les conteneurs non managés qui sont hébergés sur des machines virtuelles IaaS Linux, ou d’autres machines Linux exécutant des conteneurs Docker. Security Center évalue en continu les configurations de ces conteneurs. Il les compare ensuite au document de référence [Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/). 

Security Center inclut la totalité des règles définies dans le CIS Docker Benchmark et vous envoie une alerte si vos conteneurs ne satisfont pas à tous les contrôles. Quand il détecte des configurations incorrectes, Security Center génère des recommandations de sécurité. Accédez à la **page des recommandations** pour lire les recommandations et corriger les problèmes. Les recommandations sont également consultables sous l’onglet **Conteneurs** qui liste toutes les machines virtuelles déployées avec Docker. Quand vous examinez les problèmes de sécurité sur une machine virtuelle, Security Center fournit des informations supplémentaires à propos des conteneurs sur la machine. Il indique notamment la version de Docker et le nombre d’images exécutées sur la machine hôte. Pour plus de détails sur les recommandations, consultez [cet article](https://docs.microsoft.com/azure/security-center/security-center-virtual-machine-protection). 

>[!NOTE]
> Ces vérifications des règles du CIS Benchmark ne sont pas effectuées sur les instances managées par AKS ni sur les machines virtuelles managées par Databricks.

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>Supervision continue de vos clusters Kubernetes (préversion)
Security Center s’utilise conjointement avec Azure Kubernetes Service (AKS), le service d’orchestration de conteneurs managés de Microsoft conçu pour le développement, le déploiement et la gestion d’applications conteneurisées.

AKS fournit des contrôles de sécurité et apporte une visibilité sur la posture de sécurité de vos clusters. Security Center utilise ces fonctionnalités pour :
* Superviser en continu la configuration de vos clusters AKS
* Générer des recommandations de sécurité en adéquation avec les normes du secteur

Pour plus d’informations sur les recommandations de Security Center, consultez [Protection des machines virtuelles](security-center-virtual-machine-protection.md).

## <a name="run-time-protection---real-time-threat-detection"></a>Protection du Runtime : détection des menaces en temps réel

Security Center assure une détection des menaces en temps réel pour vos environnements conteneurisés et génère des alertes en cas d’activités suspectes. Vous pouvez utiliser ces informations pour remédier rapidement aux problèmes de sécurité et améliorer la sécurité de vos conteneurs.

Nous détectons les menaces au niveau de l’hôte et du cluster AKS. Pour plus d’informations, consultez [Service conteneur Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-).


## <a name="to-view-the-security-posture-of-your-container-related-resources"></a>Pour voir la posture de sécurité de vos ressources conteneurisées
1.  Ouvrez la page **Calcul et applications** dans Security Center.
2.  Cliquez sur l’onglet **Conteneurs**. Vous voyez la posture de vos clusters AKS, des registres ACR et des machines virtuelles exécutant Docker.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la sécurité des conteneurs dans Azure Security Center, consultez :
* Détails de l’[intégration à Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* Détails de l’[intégration à Azure Container Registry](azure-container-registry-integration.md)
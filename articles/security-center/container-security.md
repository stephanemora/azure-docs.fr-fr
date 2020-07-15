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
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: c01ed6dbbd6e1f7febfb99df11d2ee67cb1e5465
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800599"
---
# <a name="container-security-in-security-center"></a>Sécurité des conteneurs dans Security Center

Azure Security Center est la solution Azure native pour la sécurisation de vos conteneurs. Security Center peut protéger les types de ressources de conteneur suivants :



|Ressource |Nom  |Détails  |
|:---------:|---------|---------|
|![Hôte de conteneur](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)|Hôtes de conteneur (machines virtuelles qui exécutent Docker)|Security Center analyse vos configurations Docker afin d’en détecter les erreurs en fournissant la liste de toutes les règles ayant échoué qui ont été évaluées. Security Center fournit des instructions pour vous aider à résoudre ces problèmes rapidement sans perdre de temps. Security Center évalue continuellement les configurations Docker pour vous indiquer leur état le plus récent.|
|![Service Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)|Clusters Azure Kubernetes Service (AKS)|Obtenez une meilleure visibilité de vos nœuds, du trafic cloud et des contrôles de sécurité AKS, avec le [bundle AKS facultatif de Security Center](azure-kubernetes-service-integration.md) pour les utilisateurs du niveau standard.|
|![Registre de conteneurs](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)|Registres ACR (Azure Container Registry)|Obtenez une meilleure visibilité des vulnérabilités des images dans vos registres ACR basés sur ARM avec le [bundle ACR facultatif de Security Center](azure-kubernetes-service-integration.md) pour les utilisateurs du niveau standard.|
||||


Cet article décrit comment vous pouvez utiliser ces bundles pour améliorer, superviser et gérer la sécurité de vos conteneurs et de leurs applications. Il explique de quelle façon Security Center améliore les principaux aspects de la sécurité des conteneurs :

- [Gestion des vulnérabilités - analyse des images conteneur](#vulnerability-management---scanning-container-images)
- [Sécurisation renforcée de l’environnement - supervision continue de votre configuration Docker et de vos clusters Kubernetes](#environment-hardening)
- [Protection à l’exécution - détection des menaces en temps réel](#run-time-protection---real-time-threat-detection)

[![Onglet Sécurité des conteneurs d’Azure Security Center](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Pour obtenir des instructions sur l’utilisation de ces fonctionnalités, consultez [Superviser la sécurité de vos conteneurs](monitor-container-security.md).

## <a name="vulnerability-management---scanning-container-images"></a>Gestion des vulnérabilités : analyse des images conteneur
Pour surveiller votre Azure Container Registry basé sur ARM, assurez-vous que vous êtes sur le niveau standard de Security Center (voir [tarification](/azure/security-center/security-center-pricing)). Activez ensuite le pack facultatif de registres de conteneurs. Quand une nouvelle image est envoyée (push), Security Center l’analyse avec un scanneur fourni par Qualys, leader des solutions de gestion des vulnérabilités.

Si des problèmes sont détectés par Qualys ou Security Center, vous recevez une notification dans le tableau de bord de Security Center. Pour chaque vulnérabilité, Security Center fournit des recommandations actionnables, accompagnées d’une classification de gravité et de conseils sur la façon de corriger le problème. Pour plus d’informations concernant les recommandations de Security Center pour les conteneurs, consultez la [liste de référence des recommandations](recommendations-reference.md#recs-containers).

Security Center filtre et classifie les résultats à partir de l’analyseur. Quand une image est saine, Security Center la marque comme telle. Security Center génère des recommandations de sécurité uniquement pour les images qui ont des problèmes à résoudre. En n’avertissant qu’en cas de problème, Security Center réduit le risque d’alertes d’information indésirables.

## <a name="environment-hardening"></a>Sécurisation renforcée de l’environnement

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Supervision continue de votre configuration Docker
Azure Security Center identifie les conteneurs non managés qui sont hébergés sur des machines virtuelles IaaS Linux, ou d’autres machines Linux exécutant des conteneurs Docker. Security Center évalue en continu les configurations de ces conteneurs. Il les compare ensuite au document de référence [Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/).

Security Center inclut la totalité des règles définies dans le CIS Docker Benchmark et vous envoie une alerte si vos conteneurs ne satisfont pas à tous les contrôles. Quand il détecte des configurations incorrectes, Security Center génère des recommandations de sécurité. Accédez à la **page des recommandations** pour lire les recommandations et corriger les problèmes. Les recommandations sont également consultables sous l’onglet **Conteneurs** qui liste toutes les machines virtuelles déployées avec Docker. 

Pour plus d’informations sur les recommandations pertinentes de Security Center qui peuvent apparaître pour cette fonctionnalité, consultez la [section relative aux conteneurs](recommendations-reference.md#recs-containers) de la table de référence des recommandations.

Quand vous examinez les problèmes de sécurité d’une machine virtuelle, Security Center fournit des informations supplémentaires à propos des conteneurs sur la machine. Il indique notamment la version de Docker et le nombre d’images exécutées sur la machine hôte. 

>[!NOTE]
> Ces vérifications des règles du CIS Benchmark ne sont pas effectuées sur les instances managées par AKS ni sur les machines virtuelles managées par Databricks.

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Supervision continue de vos clusters Kubernetes
Security Center s’utilise conjointement avec Azure Kubernetes Service (AKS), le service d’orchestration de conteneurs managés de Microsoft conçu pour le développement, le déploiement et la gestion d’applications conteneurisées.

AKS fournit des contrôles de sécurité et apporte une visibilité sur la posture de sécurité de vos clusters. Security Center utilise ces fonctionnalités pour :
* Superviser en continu la configuration de vos clusters AKS
* Générer des recommandations de sécurité en adéquation avec les normes du secteur

Pour plus d’informations sur les recommandations pertinentes de Security Center qui peuvent apparaître pour cette fonctionnalité, consultez la [section relative aux conteneurs](recommendations-reference.md#recs-containers) de la table de référence des recommandations.

## <a name="run-time-protection---real-time-threat-detection"></a>Protection du Runtime : détection des menaces en temps réel

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]




## <a name="next-steps"></a>Étapes suivantes

Dans cette vue d’ensemble, vous avez découvert des informations sur les principaux éléments de la sécurité des conteneurs dans Azure Security Center. Continuez avec [Guide pratique pour superviser la sécurité de vos conteneurs](monitor-container-security.md).
> [!div class="nextstepaction"]
> [Supervision de la sécurité de vos conteneurs](monitor-container-security.md)
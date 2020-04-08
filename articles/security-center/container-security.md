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
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: d46e2a9820ec0c45d197f135428f1ace712b2fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125142"
---
# <a name="container-security-in-security-center"></a>Sécurité des conteneurs dans Security Center

Azure Security Center est la solution Azure native pour la sécurité des conteneurs. Security Center constitue aussi un point de contrôle unique de la sécurité de vos charges de travail cloud, machines virtuelles, serveurs et conteneurs.

Cet article décrit comment Security Center vous aide à améliorer, superviser et garantir la sécurité de vos conteneurs et de leurs applications. Il explique de quelle façon Security Center améliore les principaux aspects de la sécurité des conteneurs :

* Gestion des vulnérabilités
* Sécurisation renforcée de l’environnement des conteneurs
* Protection du Runtime

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

Security Center assure une détection des menaces en temps réel pour vos environnements conteneurisés et génère des alertes en cas d’activités suspectes. Vous pouvez utiliser ces informations pour remédier rapidement aux problèmes de sécurité et améliorer la sécurité de vos conteneurs.

Nous détectons les menaces au niveau de l’hôte et du cluster AKS. Pour plus d’informations, consultez [Détection des menaces pour les conteneurs Azure](threat-protection.md#azure-containers).


## <a name="container-security-faq"></a>FAQ sur la sécurité des conteneurs

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Quels types d’images Azure Security Center peut-il analyser ?
Security Center analyse des images du système d’exploitation Linux qui fournissent un accès à l’interpréteur de commandes. 

L’analyseur Qualys ne prend pas en charge les images super-minimalistes, telles que les images de [base Docker](https://hub.docker.com/_/scratch/) ou « Distroless » qui contiennent uniquement votre application et ses dépendances de runtime sans gestionnaire de packages, interpréteur de commandes ni système d’exploitation.

### <a name="how-does-azure-security-center-scan-an-image"></a>Comment Azure Security Center analyse une image ?
L’image est extraite du registre. Elle est ensuite exécutée dans un bac à sable isolé à l’aide de l’analyseur Qualys qui extrait une liste de vulnérabilités connues.

Security Center filtre et classifie les résultats à partir de l’analyseur. Quand une image est saine, Security Center la marque comme telle. Security Center génère des recommandations de sécurité uniquement pour les images qui ont des problèmes à résoudre. En n’avertissant qu’en cas de problème, Security Center réduit le risque d’alertes d’information indésirables.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Quelle est la fréquence à laquelle Azure Security Center analyse mes images ?
Les analyses d’images sont déclenchées à chaque envoi (push).

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Puis-je obtenir les résultats de l’analyse via l’API REST ?
Oui. Les résultats se trouvent sous l’[API REST Sub-Assessments](/rest/api/securitycenter/subassessments/list/). De plus, vous pouvez utiliser Azure Resource Graph (ARG), l’API de type Kusto pour toutes vos ressources : une requête peut extraire une analyse spécifique.
 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la sécurité des conteneurs dans Azure Security Center, consultez ces articles connexes :

* Pour afficher la position de sécurité de vos ressources associées à un conteneur, consultez la section relative aux conteneurs intitulée [Protéger vos machines et applications](security-center-virtual-machine-protection.md#containers).

* Détails de l’[intégration à Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* Détails de l’[intégration à Azure Container Registry](azure-container-registry-integration.md)
---
title: Azure Security Center et Azure Kubernetes Service
description: Découvrez comment intégrer Azure Security Center à Azure Kubernetes Service
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
ms.openlocfilehash: 9d8c5f98cfd8b4b3831bcbd7e65285f93e6c323f
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77441950"
---
# <a name="azure-kubernetes-services-integration-with-security-center-preview"></a>Intégration d’Azure Kubernetes Service à Security Center (préversion)
Azure Kubernetes Service (AKS) est le service managé de Microsoft conçu pour le développement, le déploiement et la gestion d’applications conteneurisées. 

Utilisez AKS avec le niveau tarifaire Standard d’Azure Security Center (voir les [tarifs](security-center-pricing.md)) pour avoir une meilleure visibilité des nœuds, du trafic cloud et des contrôles de sécurité dans AKS.

Security Center améliore la sécurité de vos clusters AKS en utilisant les données qui ont déjà collectées par le nœud maître AKS. 

![Vue d’ensemble d’Azure Security Center et d’Azure Kubernetes Service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Ensemble, ces deux outils constituent la meilleure solution de sécurité Kubernetes native dans le cloud. 

## <a name="benefits-of-integration"></a>Avantages de l’intégration

L’utilisation conjointe des deux services apporte les avantages suivants :

* **Recommandations de sécurité** : Security Center identifie vos ressources AKS et les classe par catégorie : des clusters aux machines virtuelles individuelles. Vous pouvez ensuite examiner les recommandations de sécurité applicables à chaque ressource. Pour plus d’informations, consultez les recommandations relatives aux conteneurs dans la [liste de référence des recommandations](recommendations-reference.md#recs-computeapp). 

    > [!NOTE]
    > Quand le nom d’une recommandation de Security Center se termine par l’étiquette « (préversion) », cela fait référence au fait qu’il s’agit d’une préversion de la recommandation, et non de la fonctionnalité elle-même.

* **Sécurisation renforcée de l’environnement** : Security Center supervise en permanence la configuration de vos clusters Kubernetes et vos configurations Docker. Il génère ensuite des recommandations de sécurité qui reflètent les normes du secteur.

* **Protection du Runtime** : par l’analyse continue des ressources AKS suivantes, Security Center vous avertit des menaces et des activités malveillantes qui sont détectées au niveau de l’hôte *et* au niveau du cluster AKS :
    * Événements de sécurité bruts, tels que la création de processus et de données réseau
    * Journal d’audit Kubernetes

    Pour plus d’informations, consultez [Détection des menaces pour les conteneurs Azure](security-center-alerts-compute.md#azure-containers-).

    Pour obtenir la liste des alertes possibles, consultez les sections suivantes dans le tableau de référence des alertes : [Alertes au niveau du cluster AKS](alerts-reference.md#alerts-akscluster) et [Alertes au niveau de l’hôte du conteneur](alerts-reference.md#alerts-containerhost).  

![Azure Security Center et Azure Kubernetes Service (AKS) plus en détail](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Certaines données analysées par Azure Security Center à partir de votre environnement Kubernetes peuvent contenir des informations sensibles.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les fonctionnalités de sécurité des conteneurs de Security Center, consultez :

* [Azure Security Center et sécurité des conteneurs](container-security.md)

* [Intégration à Azure Container Registry](azure-container-registry-integration.md)

* [Gestion des données chez Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) : décrit les stratégies de données des services Microsoft (dont Azure, Intune et Office 365), la gestion des données de Microsoft et les stratégies de conservation qui s’appliquent à vos données.
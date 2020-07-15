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
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 94d1bccc9a7f45d24d8c5b92aecba54d9f7f630a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800173"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Intégration d’Azure Kubernetes Service à Security Center

Azure Kubernetes Service (AKS) est le service managé de Microsoft conçu pour le développement, le déploiement et la gestion d’applications conteneurisées. 

Si vous êtes sur le niveau standard d’Azure Security Center, vous pouvez ajouter le bundle AKS (consultez [tarification](security-center-pricing.md)) pour obtenir une meilleure visibilité des nœuds, du trafic cloud et des contrôles de sécurité dans AKS.

Ensemble, Security Center et AKS constituent la meilleure solution de sécurité Kubernetes native dans le cloud.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Quels sont les composants de la protection Kubernetes de Security Center ?

Les protections de Security Center pour Kubernetes sont fournies par une combinaison de deux éléments :

- **Protection contre les menaces d’Azure Security Center pour les machines virtuelles** : en utilisant le même agent Log Analytics que celui utilisé par Security Center sur d’autres machines virtuelles, Security Center peut vous montrer les problèmes de sécurité de vos nœuds AKS. L’agent supervise également l’analytique spécifique aux conteneurs.

- **Bundle Kubernetes facultatif d’Azure Security Center** : le bundle Kubernetes reçoit des journaux et des informations du sous-système Kubernetes via le service AKS. Ces journaux sont déjà disponibles dans Azure via le service AKS. Quand vous activez le bundle Kubernetes de Security Center, vous accordez à Security Center l’accès aux journaux. Security Center améliore donc la sécurité de vos clusters AKS en utilisant les données qui ont déjà collectées par le nœud maître AKS. Certaines données analysées par Azure Security Center à partir de votre environnement Kubernetes peuvent contenir des informations sensibles.

    ![Vue d’ensemble d’Azure Security Center et d’Azure Kubernetes Service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

## <a name="what-protections-are-provided"></a>Quelles sont les protections fournies ?

L’utilisation conjointe des deux services apporte les avantages suivants :

* **Recommandations de sécurité** : Security Center identifie vos ressources AKS et les classe par catégorie : des clusters aux machines virtuelles individuelles. Vous pouvez ensuite examiner les recommandations de sécurité applicables à chaque ressource. Pour plus d’informations, consultez les recommandations relatives aux conteneurs dans la [liste de référence des recommandations](recommendations-reference.md#recs-containers). 

* **Sécurisation renforcée de l’environnement** : Security Center supervise en permanence la configuration de vos clusters Kubernetes et vos configurations Docker. Il génère ensuite des recommandations de sécurité qui reflètent les normes du secteur.

* **Protection à l’exécution** : via l’analyse continue des ressources AKS suivantes, Security Center vous avertit des menaces et des activités malveillantes qui sont détectées au niveau de l’hôte *et* au niveau du cluster AKS. [En savoir plus sur la protection contre les menaces pour les conteneurs](threat-protection.md#azure-containers).


     

![Azure Security Center et Azure Kubernetes Service (AKS) plus en détail](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)



## <a name="aks-with-security-center-faq"></a>Questions fréquentes (FAQ) sur AKS avec Security Center

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Puis-je quand même bénéficier des protections AKS sans l’agent Log Analytics ?

Comme indiqué ci-dessus, le bundle Kubernetes facultatif fournit des protections au niveau du cluster et l’agent Log Analytics d’Azure Security Center niveau standard protège vos nœuds. 

Nous vous recommandons de déployer les deux pour bénéficier de la protection la plus complète possible.

Si vous choisissez de ne pas installer l’agent sur vos hôtes, vous ne bénéficiez que d’une partie de la protection contre les menaces et des alertes de sécurité. Vous recevrez toujours les alertes liées à l’analyse réseau et aux communications avec des serveurs malveillants.



## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les fonctionnalités de sécurité des conteneurs de Security Center, consultez :

* [Azure Security Center et sécurité des conteneurs](container-security.md)

* [Intégration à Azure Container Registry](azure-container-registry-integration.md)

* [Gestion des données chez Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) : décrit les stratégies de données des services Microsoft (y compris Azure, Intune et Microsoft 365), la gestion des données de Microsoft et les stratégies de conservation qui affectent vos données

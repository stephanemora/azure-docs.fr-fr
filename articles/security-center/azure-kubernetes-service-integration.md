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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: f1444ea31fe693f1c912a3ecb785bbb1e3aa6333
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977276"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Intégration d’Azure Kubernetes Service à Security Center

Azure Kubernetes Service (AKS) est le service managé de Microsoft conçu pour le développement, le déploiement et la gestion d’applications conteneurisées. 

Activez **Azure Defender pour Kubernetes** pour obtenir une visibilité plus poussée sur vos nœuds AKS, le trafic cloud et les contrôles de sécurité.

Ensemble, Security Center et AKS constituent la meilleure solution de sécurité Kubernetes native dans le cloud.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Quels sont les composants de la protection Kubernetes de Security Center ?

Les protections de Security Center pour Kubernetes sont fournies par une combinaison de deux éléments :

- **Protection contre les menaces d’Azure Security Center pour les machines virtuelles** : en utilisant le même agent Log Analytics que celui utilisé par Security Center sur d’autres machines virtuelles, Security Center peut vous montrer les problèmes de sécurité de vos nœuds AKS. L’agent supervise également l’analytique spécifique aux conteneurs.

- **Bundle Kubernetes facultatif d’Azure Security Center** : le forfait Azure Defender pour Kubernetes reçoit des journaux et des informations du sous-système Kubernetes via le service AKS. Ces journaux sont déjà disponibles dans Azure via le service AKS. Lorsque vous activez Azure Defender pour Kubernetes, vous accordez à Security Center l’accès aux journaux. Security Center améliore donc la sécurité de vos clusters AKS en utilisant les données qui ont déjà collectées par le nœud maître AKS. Certaines données analysées par Azure Security Center à partir de votre environnement Kubernetes peuvent contenir des informations sensibles.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les fonctionnalités de sécurité des conteneurs de Security Center, consultez :

* [Azure Security Center et sécurité des conteneurs](container-security.md)

* [Intégration à Azure Container Registry](azure-container-registry-integration.md)

* [Gestion des données chez Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) : décrit les stratégies de données des services Microsoft (y compris Azure, Intune et Microsoft 365), la gestion des données de Microsoft et les stratégies de conservation qui affectent vos données

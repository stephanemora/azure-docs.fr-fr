---
title: Azure Security Center et Azure Container Registry
description: En savoir plus sur l’analyse de vos registres de conteneurs avec Azure Security Center
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
ms.openlocfilehash: 1335b1034304b7efe2b113f7ff2d2927fea41638
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977367"
---
# <a name="azure-container-registry-image-scanning-by-security-center"></a>Analyse d’images Azure Container Registry par Security Center

Azure Container Registry (ACR) est un service de registre Docker privé et géré qui stocke et gère vos images conteneurs pour les déploiements Azure dans un registre central. Il est basé sur le registre Docker open source 2.0.

Activez **Azure Defender pour les registres de conteneurs** afin de profiter d’une meilleure visibilité des vulnérabilités des images de vos registres basés sur Azure Resource Manager. Activez ou désactivez le plan au niveau de l’abonnement pour couvrir tous les registres d’un abonnement. Cette fonctionnalité est facturée par image, comme indiqué dans la [page des tarifs](security-center-pricing.md). L’activation d’Azure Defender garantit que Security Center est prêt à analyser les images qui sont envoyées au registre. 


## <a name="when-are-images-scanned"></a>Quand les images sont-elles analysées ?

Chaque fois qu’une image est envoyée à votre registre, Security Center analyse automatiquement cette image. Pour déclencher l’analyse d’une image, poussez-la vers votre référentiel.

Une fois l’analyse terminée (généralement après environ 2 minutes, mais elle peut prendre jusqu’à 15 minutes), les résultats sont disponibles sous forme de suggestions Security Center comme suit :

[![Exemple de suggestion Azure Security Center sur les vulnérabilités découvertes dans une image hébergée Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Avantages de l’intégration

Security Center identifie les registres ACR basés sur Azure Resource Manager dans votre abonnement et fournit en toute transparence les éléments suivants :

* **Analyse native des vulnérabilités dans Azure** pour toutes les images Linux envoyées. Security Center analyse l’image avec un scanneur fourni par Qualys, leader des solutions de gestion des vulnérabilités. Cette solution native est intégrée de manière transparente par défaut.

* **Recommandations de sécurité** pour les images Linux avec des vulnérabilités connues. Security Center fournit des détails sur chaque vulnérabilité signalée et une classification de gravité. En outre, il fournit des conseils sur la façon de corriger les vulnérabilités spécifiques détectées sur chaque image envoyée au registre.

![Présentation globale d’Azure Security Center et Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>FAQ sur l’analyse des images Azure Container Registry

### <a name="how-does-security-center-scan-an-image"></a>Comment Security Center analyse-t-il une image ?
L’image est extraite du registre. Elle est ensuite exécutée dans un bac à sable isolé à l’aide de l’analyseur Qualys qui extrait une liste de vulnérabilités connues.

Security Center filtre et classifie les résultats à partir de l’analyseur. Quand une image est saine, Security Center la marque comme telle. Security Center génère des recommandations de sécurité uniquement pour les images qui ont des problèmes à résoudre. En n’avertissant qu’en cas de problème, Security Center réduit le risque d’alertes d’information indésirables.

### <a name="how-often-does-security-center-scan-my-images"></a>Quelle est la fréquence à laquelle Security Center analyse mes images ?
Les analyses d’images sont déclenchées à chaque envoi (push).

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Puis-je obtenir les résultats de l’analyse via l’API REST ?
Oui. Les résultats se trouvent sous l’[API REST Sub-Assessments](/rest/api/securitycenter/subassessments/list/). De plus, vous pouvez utiliser Azure Resource Graph (ARG), l’API de type Kusto pour toutes vos ressources : une requête peut extraire une analyse spécifique.
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Quels sont les types de registres analysés ? Quels types sont facturés ?
La section Disponibilité répertorie les types de registres de conteneurs pris en charge par Azure Defender pour les registres de conteneurs. 

Si des registres qui ne sont pas pris en charge sont connectés à votre abonnement Azure, ils ne seront pas analysés et ne vous seront pas facturés.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les fonctionnalités de sécurité des conteneurs de Security Center, consultez :

* [Azure Security Center et sécurité des conteneurs](container-security.md)

* [Intégration à Azure Kubernetes Service](azure-kubernetes-service-integration.md)



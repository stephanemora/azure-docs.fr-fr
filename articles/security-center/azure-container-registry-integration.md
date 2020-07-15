---
title: Azure Security Center et Azure Container Registry
description: Découvrez comment intégrer Azure Security Center à Azure Container Registry
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
ms.openlocfilehash: f3ef633ff0271d74eea7320faadf17685976d3b6
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970465"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Intégration d’Azure Container Registry avec Security Center

Azure Container Registry (ACR) est un service de registre Docker privé et géré qui stocke et gère vos images conteneurs pour les déploiements Azure dans un registre central. Il est basé sur le registre Docker open source 2.0.

Si vous êtes sur le niveau standard d’Azure Security Center, vous pouvez ajouter le pack de registres de conteneurs. Cette fonctionnalité facultative apporte une meilleure visibilité des vulnérabilités des images de vos registres basés sur ARM. Activez ou désactivez le pack au niveau de l’abonnement pour couvrir tous les registres d’un abonnement. Cette fonctionnalité est facturée par image, comme indiqué dans la [page des tarifs](security-center-pricing.md). L’activation du pack de registres de conteneur garantit que Security Center est prêt à analyser les images qui sont envoyées au registre. 


## <a name="availability"></a>Disponibilité

- État de sortie : **Disponibilité générale**
- Rôles nécessaires : **Lecteur de sécurité** et [rôle de lecteur Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-roles)
- Clouds : 
    - ✔ Clouds commerciaux
    - ✘ Cloud du gouvernement des États-Unis
    - ✘ Cloud du gouvernement chinois, autres clouds gouvernementaux


## <a name="when-are-images-scanned"></a>Quand les images sont-elles analysées ?

Chaque fois qu’une image est envoyée à votre registre, Security Center analyse automatiquement cette image. Pour déclencher l’analyse d’une image, poussez-la vers votre référentiel.

Une fois l’analyse terminée (généralement après environ 10 minutes, mais elle peut prendre jusqu’à 40 minutes), les résultats sont disponibles sous forme de suggestions Security Center comme suit :

[![Exemple de suggestion Azure Security Center sur les vulnérabilités découvertes dans une image hébergée Azure Container Registry (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Avantages de l’intégration

Security Center identifie les registres ACR basés sur ARM dans votre abonnement, et fournit en toute transparence les éléments suivants :

* **Analyse native des vulnérabilités dans Azure** pour toutes les images Linux envoyées. Security Center analyse l’image avec un scanneur fourni par Qualys, leader des solutions de gestion des vulnérabilités. Cette solution native est intégrée de manière transparente par défaut.

* **Recommandations de sécurité** pour les images Linux avec des vulnérabilités connues. Security Center fournit des détails sur chaque vulnérabilité signalée et une classification de gravité. En outre, il fournit des conseils sur la façon de corriger les vulnérabilités spécifiques détectées sur chaque image envoyée au registre.

![Présentation globale d’Azure Security Center et Azure Container Registry (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="acr-with-security-center-faq"></a>FAQ sur ACR avec Security Center

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

Pour en savoir plus sur les fonctionnalités de sécurité des conteneurs de Security Center, consultez :

* [Azure Security Center et sécurité des conteneurs](container-security.md)

* [Intégration à Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Protection des machines virtuelles](security-center-virtual-machine-protection.md) : décrit les recommandations de Security Center

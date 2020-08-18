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
ms.date: 08/02/2020
ms.author: memildin
ms.openlocfilehash: 718f9a29b70dab34269c959ccd62452e56a32d72
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056599"
---
# <a name="azure-container-registry-image-scanning-by-security-center"></a>Analyse d’images Azure Container Registry par Security Center

Azure Container Registry (ACR) est un service de registre Docker privé et géré qui stocke et gère vos images conteneurs pour les déploiements Azure dans un registre central. Il est basé sur le registre Docker open source 2.0.

Si vous êtes sur le niveau standard d’Azure Security Center, vous pouvez ajouter le pack de registres de conteneurs. Cette fonctionnalité facultative apporte une meilleure visibilité des vulnérabilités des images de vos registres basés sur Azure Resource Manager. Activez ou désactivez le pack au niveau de l’abonnement pour couvrir tous les registres d’un abonnement. Cette fonctionnalité est facturée par image, comme indiqué dans la [page des tarifs](security-center-pricing.md). L’activation du pack de registres de conteneur garantit que Security Center est prêt à analyser les images qui sont envoyées au registre. 

## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale|
|Prix :|Niveau standard|
|Registres et images pris en charge :|![Oui](./media/icons/yes-icon.png) Registres Azure Container Registry hébergés sur Linux qui sont accessibles à partir de l’Internet public et qui fournissent un accès à l’interpréteur de commandes.<br>![Non](./media/icons/no-icon.png) Registres Azure Container Registry hébergés sur Windows.<br>![No](./media/icons/no-icon.png) Registres « privés » : Security Center exige que vos registres soient accessibles à partir de l’Internet public. Security Center ne peut actuellement pas se connecter à des registres dont l’accès est limité par un pare-feu, à un point de terminaison de service ou à des points de terminaison privés tels qu’Azure Private Link, ni les analyser.<br>![Non](./media/icons/no-icon.png) Images super-minimalistes, telles que les images [de base Docker](https://hub.docker.com/_/scratch/) ou « Distroless » qui contiennent uniquement une application et ses dépendances de runtime sans gestionnaire de packages, interpréteur de commandes ni système d’exploitation.|
|Rôles et autorisations obligatoires :|**Lecteur de sécurité** et [rôle de lecteur Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-roles)|
|Clouds :|![Oui](./media/icons/yes-icon.png) Clouds commerciaux<br>![Non](./media/icons/no-icon.png) National/souverain (US Gov, Chine Gov, autres Gov)|
|||




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
La [section Disponibilité](#availability) répertorie les types de registres de conteneurs pris en charge par le pack de registres de conteneurs. 

Si des registres qui ne sont pas pris en charge sont connectés à votre abonnement Azure, ils ne seront pas analysés et ne vous seront pas facturés.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les fonctionnalités de sécurité des conteneurs de Security Center, consultez :

* [Azure Security Center et sécurité des conteneurs](container-security.md)

* [Intégration à Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Protection des machines virtuelles](security-center-virtual-machine-protection.md) : décrit les recommandations de Security Center

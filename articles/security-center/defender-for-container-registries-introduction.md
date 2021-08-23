---
title: 'Azure Defender pour les registres de conteneurs : avantages et fonctionnalités'
description: Découvrez les avantages et les fonctionnalités d’Azure Defender pour les registres de conteneurs.
author: memildin
ms.author: memildin
ms.date: 07/05/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: d448d0b0a67987ced1caec04557773c2f8ab33d7
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113651817"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>Introduction à Azure Defender pour des registres de conteneurs

Azure Container Registry (ACR) est un service de registre Docker privé et géré qui stocke et gère vos images conteneurs pour les déploiements Azure dans un registre central. Il est basé sur le registre Docker open source 2.0.

Pour protéger les registres basés sur Azure Resource Manager de votre abonnement, activez **Azure Defender pour les registres de conteneurs** au niveau de l’abonnement. Security Defender analysera ensuite toutes les images envoyées au registre, importées dans le registre, ou les images extraites au cours des 30 derniers jours. Vous serez facturé pour chaque image analysée, une fois par image.

[!INCLUDE [Defender for container registries availability info](../../includes/security-center-availability-defender-for-container-registries.md)]

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>Quels sont les avantages d’Azure Defender pour les registres de conteneurs ?

Security Center identifie les registres ACR basés sur Azure Resource Manager dans votre abonnement et fournit en toute transparence une évaluation et une gestion des vulnérabilités natives d’Azure pour les images de votre registre.

**Azure Defender pour les registres de conteneurs** inclut un scanner de vulnérabilité pour analyser les images dans vos registres Azure Container Registry basés sur Azure Resource Manager et fournir une visibilité plus poussée des vulnérabilités de vos images. L'analyseur intégré est proposé par Qualys, un leader dans le secteur des solutions d’identification des vulnérabilités.

Si des problèmes sont détectés par Qualys ou Security Center, vous recevez une notification dans le tableau de bord de Security Center. Pour chaque vulnérabilité, Security Center fournit des recommandations actionnables, accompagnées d’une classification de gravité et de conseils sur la façon de corriger le problème. Pour plus d’informations concernant les recommandations de Security Center pour les conteneurs, consultez la [liste de référence des recommandations](recommendations-reference.md#recs-container).

Security Center filtre et classifie les résultats à partir de l’analyseur. Quand une image est saine, Security Center la marque comme telle. Security Center génère des recommandations de sécurité uniquement pour les images qui ont des problèmes à résoudre. Security Center fournit des détails sur chaque vulnérabilité signalée et une classification de gravité. En outre, il fournit des conseils sur la façon de corriger les vulnérabilités spécifiques détectées sur chaque image.

En n’avertissant qu’en cas de problème, Security Center réduit le risque d’alertes d’information indésirables.


> [!TIP]
> Pour en savoir plus sur les fonctionnalités de sécurité des conteneurs de Security Center, consultez :
>
> - [Azure Security Center et sécurité des conteneurs](container-security.md)
> - [Présentation d’Azure Defender pour Kubernetes](defender-for-kubernetes-introduction.md)

## <a name="when-are-images-scanned"></a>Quand les images sont-elles analysées ?

Il existe trois déclencheurs pour une analyse d’image :

- **On push** (en mode « push ») : chaque fois qu’une image est envoyée à votre registre, Security Center analyse automatiquement cette image. Pour déclencher l’analyse d’une image, poussez-la vers votre référentiel.

- **Recently pulled** (extraites récemment) : étant donné que de nouvelles vulnérabilités sont découvertes chaque jour, **Azure Defender pour les registres de conteneurs** analyse également toutes les semaines les images extraites au cours des 30 derniers jours. Aucuns frais supplémentaires ne sont facturés pour ces nouvelles analyses. Comme indiqué ci-dessus, vous êtes facturé une fois par image.

- **Lors de l’importation** : Azure Container Registry inclut des outils d’importation pour importer des images dans votre registre à partir de Dockr Hub, d’un registre de conteneurs Microsoft ou d’un autre registre de conteneurs Azure. **Azure Defender pour les registres de conteneurs** analyse toutes les images prises en charge que vous importez. Pour plus d’informations, consultez [Importation d’images conteneur dans un registre de conteneurs](../container-registry/container-registry-import-images.md).
 
L’analyse se termine généralement dans un délai de 2 minutes, mais elle peut prendre jusqu’à 15 minutes. Les résultats sont mis à disposition en tant que recommandations du Security Center, par exemple :

[![Exemple de suggestion Azure Security Center sur les vulnérabilités découvertes dans une image hébergée Azure Container Registry (ACR).](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>Interaction entre Azure Security Center et Azure Container Registry

Vous trouverez ci-dessous un diagramme de haut niveau des composants et des avantages qu’offre la protection de vos registres avec Security Center.

![Présentation globale d’Azure Security Center et Azure Container Registry (ACR).](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq---azure-container-registry-image-scanning"></a>FAQ - Analyse d’images Azure Container Registry

### <a name="how-does-security-center-scan-an-image"></a>Comment Security Center analyse-t-il une image ?
Security Center extrait l’image du registre et l’exécute dans un bac à sable isolé avec l’analyseur Qualys. L’analyseur extrait une liste des vulnérabilités connues.

Security Center filtre et classifie les résultats à partir de l’analyseur. Quand une image est saine, Security Center la marque comme telle. Security Center génère des recommandations de sécurité uniquement pour les images qui ont des problèmes à résoudre. En ne vous avertissant qu’en cas de problème, Security Center réduit le risque d’alertes d’information indésirables.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Puis-je obtenir les résultats de l’analyse via l’API REST ?
Oui. Les résultats se trouvent sous l’[API REST Sub-Assessments](/rest/api/securitycenter/subassessments/list/). De plus, vous pouvez utiliser Azure Resource Graph (ARG), l’API de type Kusto pour toutes vos ressources : une requête peut extraire une analyse spécifique.

### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Quels sont les types de registres analysés ? Quels types sont facturés ?
Pour une liste des types de registres de conteneurs pris en charge par Azure Defender pour les registres de conteneurs, consultez [Disponibilité](#availability).

Si vous connectez des registres non pris en charge à votre abonnement Azure, Azure Defender ne les analysera pas et ne vous les facturera pas.

### <a name="can-i-customize-the-findings-from-the-vulnerability-scanner"></a>Puis-je personnaliser les résultats de l’analyseur de vulnérabilités ?
Oui. Si votre organisation préfère ignorer un résultat, plutôt que de le corriger, vous pouvez éventuellement désactiver cette fonction. Les résultats désactivés n’ont pas d’impact sur votre Niveau de sécurité ni ne génèrent de bruit indésirable.

[Découvrir la création de règles pour désactiver les résultats à partir de l’outil d’évaluation des vulnérabilités intégré](defender-for-container-registries-usage.md#disable-specific-findings-preview).

### <a name="why-is-security-center-alerting-me-to-vulnerabilities-about-an-image-that-isnt-in-my-registry"></a>Pourquoi Security Center m’alerte au sujet de vulnérabilités concernant une image qui ne se trouve pas dans mon registre ?
Security Center fournit des évaluations de vulnérabilité pour chaque image envoyée ou extraite dans un registre. Certaines images peuvent réutiliser des étiquettes à partir d’une image déjà analysée. Par exemple, vous pouvez réassigner l’étiquette « latest » chaque fois que vous ajoutez une image à une synthèse. Dans ce cas, l’« ancienne » image existe toujours dans le registre et peut encore être extraite par sa synthèse. Si l’image présente des résultats de sécurité et qu’elle est extraite, elle expose des vulnérabilités de sécurité.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyser vos images à la recherche de vulnérabilités](defender-for-container-registries-usage.md)

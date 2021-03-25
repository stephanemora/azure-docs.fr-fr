---
title: Diagnostiquer et résoudre les problèmes avec Gestion des API Azure
description: Découvrez comment résoudre les problèmes liés à votre API dans Gestion des API Azure avec l’outil de diagnostic et de résolution des problèmes dans le portail Azure.
author: rzhang628
ms.service: api-management
ms.topic: article
ms.date: 02/05/2021
ms.author: rongzhang
ms.openlocfilehash: d8ec04227316088983977f5b487abfa81fb5c525
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652400"
---
# <a name="azure-api-management-diagnostics-overview"></a>Vue d’ensemble des diagnostics dans Gestion des API Azure

Quand vous générez et gérez une API dans Gestion des API Azure, vous devez vous préparer à toutes sortes de problème pouvant se produire, des erreurs 404 (introuvable) aux erreurs 502 (passerelle incorrecte). Avec les diagnostics de Gestion des API (APIM, API Management), vous pouvez résoudre les problèmes de votre API publiée dans APIM de manière intelligente et interactive sans effectuer de configuration particulière. Si vous rencontrez des problèmes avec vos API publiées, les diagnostics de Gestion des API vous en indiquent la nature et vous orientent vers les informations appropriées pour les résoudre rapidement.

Cette fonctionnalité est particulièrement utile quand vous rencontrez des problèmes avec votre API au cours des dernières 24 heures, mais vous pouvez aussi analyser tous les graphes de diagnostic à tout moment.

## <a name="open-api-management-diagnostics"></a>Ouvrir les diagnostics de Gestion des API

Pour accéder aux diagnostics de Gestion des API, accédez à votre instance de service Gestion des API dans le [portail Azure](https://portal.azure.com). Dans le volet de navigation de gauche, sélectionnez **Diagnostiquer et résoudre les problèmes**.

:::image type="content" source="media/diagnose-solve-problems/apim-diagnostic-home.png" alt-text="Capture d’écran montrant comment accéder aux diagnostics.":::



## <a name="intelligent-search"></a>Recherche intelligente

Vous pouvez rechercher vos problèmes à l’aide de la barre de recherche en haut de la page. La recherche vous permet également à trouver les outils qui vous aideront à résoudre vos problèmes. 

:::image type="content" source="media/diagnose-solve-problems/intelligent-search.png" alt-text="capture d’écran de la recherche intelligente.":::


## <a name="troubleshooting-categories"></a>Catégories de résolution des problèmes

Vous pouvez résoudre les problèmes en fonction de leur catégorie. Les problèmes courants liés aux performances, à la passerelle et aux stratégies de vos API ainsi qu’au niveau de service peuvent tous être analysés dans la catégorie correspondante. Chaque catégorie fournit également des vérifications de diagnostic plus spécifiques. 

:::image type="content" source="media/diagnose-solve-problems/troubleshooting-category.png" alt-text="capture d’écran de la vue d’ensemble des catégories.":::


### <a name="availability-and-performance"></a>Disponibilité et performances

Examinez les problèmes de disponibilité et de performances de vos API dans cette catégorie. Quand vous sélectionnez cette vignette de catégorie, une interface interactive recommande quelques vérifications courantes. Cliquez sur une vérification pour examiner un problème de façon plus approfondie et plus détaillée. La vérification fournit également un graphe montrant les performances de votre API et un résumé des problèmes de performances. Par exemple, votre service d’API a peut-être rencontré une erreur 5xx et expiré au cours de la dernière heure sur le back-end. 

:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-1.png" alt-text="Capture d’écran 1 de la vérification de l’interface interactive.":::



:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-2.png" alt-text="Capture d’écran 2 de la vérification de l’interface interactive.":::

### <a name="api-policies"></a>Stratégies d’API

Cette catégorie détecte et signale les problèmes de stratégie. 

Une interface interactive similaire vous oriente vers les métriques de données pour vous aider à résoudre les problèmes de configuration de vos stratégies d’API.

:::image type="content" source="media/diagnose-solve-problems/proxy-policies.png" alt-text="capture d’écran de la vignette de catégorie des stratégies d’API.":::

### <a name="gateway-performance"></a>Performances de la passerelle 

Pour les requêtes ou réponses de passerelle ou toute erreur 4xx ou 5xx sur votre passerelle, utilisez cette catégorie afin de surveiller et résoudre les problèmes. Vous pouvez encore tirer parti de l’interface interactive pour examiner de façon plus approfondie et plus détaillée un aspect des performances de votre passerelle API. 

:::image type="content" source="media/diagnose-solve-problems/gateway-performance-tile.png" alt-text="capture d’écran de la vignette de catégorie des performances de passerelle.":::

### <a name="service-upgrade"></a>Mise à niveau du service

Cette catégorie vérifie le niveau de service (SKU) que vous utilisez actuellement et, le cas échéant, vous rappelle de procéder à une mise à niveau pour éviter tout problème lié à ce niveau. La même interface interactive vous permet d’approfondir votre examen grâce à des graphes supplémentaires et à un résumé des résultats de vérification. 

:::image type="content" source="media/diagnose-solve-problems/service-sku.png" alt-text="capture d’écran de la vignette de catégorie de mise à niveau du service.":::

## <a name="search-documentation"></a>Rechercher dans la documentation

Au-delà des outils de diagnostic et de résolution des problèmes à votre disposition, vous pouvez également rechercher de la documentation vous aidant spécifiquement à résoudre votre problème. Après avoir exécuté les diagnostics sur votre service, sélectionnez **Rechercher de la documentation** dans l’interface interactive. 

 :::image type="content" source="media/diagnose-solve-problems/search-documentation.png" alt-text="capture d’écran 1 illustrant l’utilisation de la fonction de recherche de documentation.":::


 :::image type="content" source="media/diagnose-solve-problems/search-documentation-2.png" alt-text="capture d’écran 2 illustrant l’utilisation de la fonction de recherche de documentation.":::


## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez également utiliser l’[analytique des API](howto-use-analytics.md) pour analyser l’utilisation et les performances des API. 
* Vous souhaitez résoudre les problèmes d’application web avec les diagnostics ? Consultez [cet article](../app-service/overview-diagnostics.md).
* Tirez parti des diagnostics pour vérifier les problèmes liés à Azure Kubernetes Service. Consultez [Diagnostics d’Azure Kubernetes Service](../aks/concepts-diagnostics.md).
* Publiez vos questions ou vos commentaires sur [UserVoice](https://feedback.azure.com/forums/248703-api-management) en ajoutant « [Diag] » dans le titre.

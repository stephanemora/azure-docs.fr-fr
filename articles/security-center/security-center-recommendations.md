---
title: Recommandations de sécurité dans le Centre de sécurité Azure
description: Ce document vous montre comment les recommandations du Centre de sécurité Azure peuvent vous aider à protéger vos ressources Azure et à rester en conformité avec les stratégies de sécurité.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: dbda0aecdadd81da0f7681a5fc9b140157d5e8f3
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756822"
---
# <a name="security-recommendations-in-azure-security-center"></a>Recommandations de sécurité dans le Centre de sécurité Azure 

Cette rubrique explique comment visualiser et comprendre les recommandations d’Azure Security Center pour vous aider à protéger vos ressources Azure.


## <a name="what-are-security-recommendations"></a>Quelles sont les recommandations de sécurité ?

Security Center analyse périodiquement l’état de sécurité de vos ressources Azure pour identifier les vulnérabilités de sécurité potentielles. Il fournit ensuite des recommandations sur la façon de corriger ces vulnérabilités.

Les recommandations sont des actions à effectuer pour sécuriser et renforcer vos ressources. 

Chaque recommandation vous fournit les informations suivantes :

- Courte description du problème
- Étapes de correction à effectuer pour implémenter la recommandation
- Ressources concernées

## <a name="how-does-microsoft-decide-what-needs-securing-and-hardening"></a>Comment Microsoft décide-t-il de ce qui doit être sécurisé et renforcé ?

Les recommandations de Security Center sont basées sur le Benchmark de sécurité Azure. 

Le Benchmark de sécurité Azure est l’ensemble des directives propres à Azure et créées par Microsoft contenant les bonnes pratiques de sécurité et de conformité basées sur les infrastructures de conformité courantes. Ce point de référence, largement respecté et centré sur le cloud, est basé sur les contrôles du [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) et du [National Institute of Standards and Technology (NIST)](https://www.nist.gov/).

Découvrez le [Benchmark de sécurité Azure](../security/benchmarks/introduction.md).

## <a name="monitor-recommendations"></a>Surveiller les recommandations <a name="monitor-recommendations"></a>

Azure Security Center analyse l’état de sécurité de vos ressources pour identifier les vulnérabilités potentielles. 

1. Dans le menu de Security Center, ouvrez la page **Recommendations** pour voir les recommandations applicables à votre environnement. Les recommandations sont regroupées en contrôles de sécurité.

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="Recommandations regroupées par contrôle de sécurité" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. Pour rechercher des recommandations propres au type de ressource, à la gravité, à l’environnement ou à d’autres critères qui sont importants pour vous, utilisez les filtres facultatifs au-dessus de la liste des recommandations.

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="Filtres permettant d’affiner la liste des recommandations d’Azure Security Center":::

1. Développez un contrôle et sélectionnez une recommandation spécifique pour afficher la page de recommandation détaillée.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Page Détails de la recommandation." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    La page comprend les éléments suivants :

    1. Les boutons **Appliquer** et **Refuser** sur les recommandations prises en charge (voir [Empêcher des configurations incorrectes à l’aide des recommandations Appliquer/Refuser](prevent-misconfigurations.md))
    1. **Indicateur de gravité**
    1. **Intervalle d’actualisation** (le cas échéant) 
    1. **Description** : courte description du problème.
    1. **Étapes de correction** : description des étapes manuelles nécessaires pour corriger le problème de sécurité sur les ressources concernées. Pour obtenir des recommandations avec un « correctif rapide », vous pouvez sélectionner **Afficher la logique de correction** avant d’appliquer la correction suggérée à vos ressources. 
    1. **Ressources concernées** : vos ressources sont regroupées dans des onglets :
        - **Ressources saines** : ressources pertinentes qui ne sont pas touchées ou sur lesquelles vous avez déjà corrigé le problème.
        - **Ressources non saines** : ressources qui sont encore concernées par le problème identifié.
        - **Ressources non applicables** : ressources pour lesquelles la recommandation ne peut pas donner de réponse définitive. L’onglet Non applicables comprend également les raisons pour chaque ressource. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Ressources non applicables avec leurs raisons.":::
    1. Boutons d’action pour corriger la recommandation ou déclencher une application logique.

## <a name="preview-recommendations"></a>Recommandations sur la préversion

Les recommandations marquées **Preview (Préversion)** ne sont pas incluses dans les calculs de votre degré de sécurisation.

Elles doivent tout de même être corrigées dans la mesure du possible, ainsi lorsque la période de préversion se termine, elles seront prises en compte dans le calcul.

Exemple de recommandation de préversion :

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Recommandation portant l’indicateur Preview (Préversion)":::
 
## <a name="next-steps"></a>Étapes suivantes

Ce document vous a présenté les recommandations de sécurité du Centre de sécurité. Pour obtenir des informations connexes :

- [Implémenter les recommandations](security-center-remediate-recommendations.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
- [Empêcher des configurations incorrectes à l’aide des recommandations Appliquer/Refuser](prevent-misconfigurations.md).
- [Automatiser les réponses aux déclencheurs Security Center](workflow-automation.md) : automatiser les réponses aux recommandations.
- [Exempter une ressource d’une recommandation](exempt-resource.md)
- [Recommandations de sécurité - Guide de référence](recommendations-reference.md)
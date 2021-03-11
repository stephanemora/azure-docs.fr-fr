---
title: Recommandations de sécurité dans le Centre de sécurité Azure
description: Ce document vous montre comment les recommandations du Centre de sécurité Azure peuvent vous aider à protéger vos ressources Azure et à rester en conformité avec les stratégies de sécurité.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 57760443746e111750e74ef55fc18729f6ba32c4
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100336"
---
# <a name="review-your-security-recommendations"></a>Consulter vos recommandations de sécurité

Cette rubrique explique comment visualiser et comprendre les recommandations d’Azure Security Center pour vous aider à protéger vos ressources Azure.

## <a name="monitor-recommendations"></a>Surveiller les recommandations <a name="monitor-recommendations"></a>

Azure Security Center analyse l’état de sécurité de vos ressources pour identifier les vulnérabilités potentielles. 

1. Dans le menu de Security Center, ouvrez la page **Recommendations** pour voir les recommandations applicables à votre environnement. Les recommandations sont regroupées en contrôles de sécurité.

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="Recommandations regroupées par contrôle de sécurité" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. Pour rechercher des recommandations propres au type de ressource, à la gravité, à l’environnement ou à d’autres critères qui sont importants pour vous, utilisez les filtres facultatifs au-dessus de la liste des recommandations.

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="Filtres permettant d’affiner la liste des recommandations d’Azure Security Center":::

1. Développez un contrôle et sélectionnez une recommandation spécifique pour afficher la page de recommandation détaillée.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Page Détails de la recommandation." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    La page comprend les éléments suivants :

    1. Pour les recommandations prises en charge, la barre d’outils supérieure affiche tout ou partie des boutons suivants :
        - **Appliquer** et **Refuser** (voir [Empêcher des configurations incorrectes à l’aide des recommandations Appliquer/Refuser](prevent-misconfigurations.md))
        - **Afficher la définition de stratégie** pour accéder directement à l’entrée d’Azure Policy pour la stratégie sous-jacente
    1. **Indicateur de gravité**
    1. **Intervalle d’actualisation** (le cas échéant)
    1. **Nombre de ressources exemptées** ; si des exemptions existent pour cette recommandation, il indique le nombre de ressources qui ont été exemptées
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
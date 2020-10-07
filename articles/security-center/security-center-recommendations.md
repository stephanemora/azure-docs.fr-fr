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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 7f6c0f2a311590219fb59bfe1ec63831c03e8af2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314434"
---
# <a name="security-recommendations-in-azure-security-center"></a>Recommandations de sécurité dans le Centre de sécurité Azure 
Cette rubrique explique comment visualiser et comprendre les recommandations d’Azure Security Center pour vous aider à protéger vos ressources Azure.

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement.  Ce document n'est pas un guide pas à pas.
>

## <a name="what-are-security-recommendations"></a>Quelles sont les recommandations de sécurité ?

Les recommandations sont des actions à effectuer pour sécuriser vos ressources.

Security Center analyse périodiquement l’état de sécurité de vos ressources Azure pour identifier les vulnérabilités de sécurité potentielles. Il fournit ensuite des recommandations sur la façon de corriger ces vulnérabilités.

Chaque recommandation vous fournit les informations suivantes :

- Courte description du problème.
- Étapes de correction à effectuer pour implémenter la recommandations.
- Ressources concernées.

## <a name="monitor-recommendations"></a>Surveiller les recommandations <a name="monitor-recommendations"></a>

Azure Security Center analyse l’état de sécurité de vos ressources pour identifier les vulnérabilités potentielles. La vignette **Recommandations** sous **Vue d’ensemble** montre le nombre total de recommandations identifiées par Security Center.

![Vue d’ensemble de Security Center](./media/security-center-recommendations/asc-overview.png)

1. Sélectionnez la **vignette Recommandations** sous **Vue d’ensemble**. La liste **Recommandations** s’ouvre.

1. Les recommandations sont regroupées en contrôles de sécurité.

      ![Recommandations regroupées par contrôle de sécurité](./media/security-center-recommendations/view-recommendations.png)

1. Développez un contrôle et sélectionnez une recommandation spécifique pour afficher la page de recommandation.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Page Détails de la recommandation." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    La page comprend les éléments suivants :

    - Les boutons **Appliquer** et **Refuser** sur les recommandations prises en charge (voir [Empêcher des configurations incorrectes à l’aide des recommandations Appliquer/Refuser](prevent-misconfigurations.md))
    - **Indicateur de gravité**
    - **Intervalle d’actualisation** (le cas échéant) 
    - **Description** : courte description du problème.
    - **Étapes de correction** : description des étapes manuelles nécessaires pour corriger le problème de sécurité sur les ressources concernées. Pour obtenir des recommandations avec un « correctif rapide », vous pouvez sélectionner **Afficher la logique de correction** avant d’appliquer la correction suggérée à vos ressources. 
    - **Ressources concernées** : vos ressources sont regroupées dans des onglets :
        - **Ressources saines** : ressources pertinentes qui ne sont pas touchées ou sur lesquelles vous avez déjà corrigé le problème.
        - **Ressources non saines** : ressources qui sont encore concernées par le problème identifié.
        - **Ressources non applicables** : ressources pour lesquelles la recommandation ne peut pas donner de réponse définitive. L’onglet Non applicables comprend également les raisons pour chaque ressource. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Page Détails de la recommandation.":::

## <a name="preview-recommendations"></a>Recommandations sur la préversion

Les recommandations marquées **Preview (Préversion)** ne sont pas incluses dans les calculs de votre degré de sécurisation.

Elles doivent tout de même être corrigées dans la mesure du possible, ainsi lorsque la période de préversion se termine, elles seront prises en compte dans le calcul.

Exemple de recommandation de préversion :

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Page Détails de la recommandation.":::
 
## <a name="next-steps"></a>Étapes suivantes

Ce document vous a présenté les recommandations de sécurité du Centre de sécurité. Pour savoir comment implémenter les recommandations :

- [Implémenter les recommandations](security-center-remediate-recommendations.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
- [Empêcher des configurations incorrectes à l’aide des recommandations Appliquer/Refuser](prevent-misconfigurations.md).

---
title: Appliquer des recommandations dans Azure Security Center | Microsoft Docs
description: Ce article explique comment appliquer les recommandations d’Azure Security Center pour protéger vos ressources et respecter les stratégies de sécurité.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: f382646c889d004738064cae2d09fd66d897b110
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438265"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Appliquer les recommandations d’Azure Security Center

Les recommandations fournissent des suggestions visant à mieux sécuriser vos ressources. Pour appliquer une recommandation, vous devez suivre les étapes de correction qu’elle fournit.

## <a name="remediation-steps"></a>Étapes de correction <a name="remediation-steps"></a>

Après avoir lu toutes les recommandations, vous devez décider laquelle appliquer en premier. Nous vous recommandons de traiter en priorité les contrôles de sécurité qui sont le plus susceptibles d’augmenter votre degré de sécurisation.

1. Sélectionnez une recommandation dans la liste.

1. Suivez les instructions dans la section **Étapes de correction**. Chaque recommandation comprend un ensemble d’instructions. La capture d’écran suivante montre les étapes de correction permettant de configurer les applications de manière à autoriser uniquement le trafic via HTTPS.

    :::image type="content" source="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png" alt-text="Étapes de correction manuelle pour une recommandation" lightbox="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png":::

1. Une fois l’opération terminée, une notification vous indique si le problème est résolu.

## <a name="quick-fix-remediation"></a>Application d’un correctif rapide

Pour simplifier la correction et améliorer la sécurité de votre environnement (et ainsi votre degré de sécurisation), de nombreuses recommandations comportent une option de correctif rapide.

Le correctif rapide vous permet d’appliquer rapidement une recommandation à plusieurs ressources.

> [!TIP]
> Les solutions de correctif rapide sont disponibles uniquement pour des recommandations spécifiques. Pour connaître les recommandations concernées, utilisez le filtre **Actions de réponse** dans la liste des recommandations :
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="Utilisez les filtres au-dessus de la liste des recommandations pour rechercher des recommandations incluant l’option correctif rapide":::

Pour mettre en œuvre une solution de correctif rapide :

1. Dans la liste des recommandations qui ont l’étiquette **Correctif rapide !** , sélectionnez une recommandation.

    [![Sélectionnez Correctif rapide !](media/security-center-remediate-recommendations/security-center-quick-fix-select.png)](media/security-center-remediate-recommendations/security-center-quick-fix-select.png#lightbox)

1. Sous l’onglet **Ressources défectueuses**, sélectionnez les ressources auxquelles vous souhaitez implémenter la recommandation, puis sélectionnez **Corriger**.

    > [!NOTE]
    > Certaines des ressources listées peuvent être désactivées, ce qui est le cas si vous n’avez pas les autorisations appropriées pour les modifier.

1. Dans la zone de confirmation, lisez les détails et les implications de la correction.

    ![Correctif rapide](./media/security-center-remediate-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > Les implications sont listées dans la zone grise de la fenêtre **Corriger les ressources** qui s’ouvre après que vous avez cliqué sur **Corriger**. Elles indiquent les changements apportés si vous implémentez le correctif rapide.

1. Ajoutez les paramètres appropriés, si nécessaire, et approuvez la correction.

    > [!NOTE]
    > Une fois la correction terminée, il peut s’écouler plusieurs minutes avant que les ressources apparaissent sous l’onglet **Ressources saines**. Pour voir les actions de correction, consultez le [journal d’activité](#activity-log).

1. Une fois l’opération terminée, une notification s’affiche pour vous informer si la mise à jour a réussi.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Journalisation de l’application du correctif rapide dans le journal d’activité <a name="activity-log"></a>

L’opération de correction applique la configuration à la ressource à l’aide d’un déploiement de modèle ou d’un appel à l’API REST PATCH. Ces opérations sont consignées dans le [journal d’activité Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="next-steps"></a>Étapes suivantes

Ce document vous a montré comment appliquer les recommandations fournies par Security Center. Pour plus d’informations sur Security Center, consultez les pages suivantes :

* [Définition des stratégies de sécurité dans Azure Security Center](tutorial-security-policy.md) : apprenez à configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Présentation des stratégies de sécurité, des initiatives et des recommandations](security-policy-concept.md).
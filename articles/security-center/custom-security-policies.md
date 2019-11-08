---
title: Créer des stratégies de sécurité personnalisées dans Azure Security Center | Microsoft Docs
description: Définitions de stratégie Azure personnalisées surveillées dans Azure Security Center.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: d5432c794c26e350b23fe47aa1574422143ca4b2
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521479"
---
# <a name="using-custom-security-policies-preview"></a>Utilisation de stratégies de sécurité personnalisées (préversion)

Pour vous aider à sécuriser vos systèmes et votre environnement, Azure Security Center génère des recommandations de sécurité. Ces recommandations sont basées sur les meilleures pratiques du secteur, qui sont incorporées à la stratégie de sécurité par défaut générique fournie à tous les clients. Elles peuvent également provenir des connaissances que Security Center a des normes et réglementations du secteur.

Avec cette fonctionnalité en préversion, vous pouvez ajouter vos propres initiatives *personnalisées*. Vous recevez ensuite des recommandations si vos ordinateurs ne suivent pas les stratégies que vous créez.

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>Pour ajouter une initiative personnalisée à votre abonnement 

1. Dans la barre latérale de Security Center, ouvrez la page **Stratégie de sécurité**.

1. Sélectionnez un abonnement ou un groupe d’administration auquel vous voulez ajouter une initiative personnalisée.

    [![Sélection d’un abonnement pour lequel vous allez créer votre stratégie personnalisée](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Lorsque vous ajoutez une norme personnalisée, elle attribue une *initiative* à cette étendue. Nous vous recommandons donc de sélectionner l’étendue la plus vaste requise pour cette attribution. 

1. Dans la page Stratégie de sécurité, sous vos initiatives personnalisées (préversion), cliquez sur **Ajouter une initiative personnalisée**.

    [![Cliquez sur *Ajouter une initiative personnalisée*](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    La page suivante apparaît :

    ![Créer ou ajouter une stratégie](media/custom-security-policies/create-or-add-custom-policy.png)

1. Dans la page Ajouter des initiatives personnalisées, passez en revue la liste de stratégies personnalisées déjà créées dans votre organisation. Si vous en voyez une que vous souhaitez attribuer à votre abonnement, cliquez sur **Ajouter**. Si aucune initiative de la liste ne répond à vos besoins, ignorez cette étape.

1. Pour créer une nouvelle initiative personnalisée :

    1. Cliquez sur **Créer une nouvelle**.
    1. Entrez l’emplacement et le nom de la définition.
    1. Sélectionnez les stratégies à inclure, puis cliquez sur **Ajouter**.
    1. Entrez les paramètres souhaités.
    1. Cliquez sur **Enregistrer**.
    1. Dans la page Ajouter des initiatives personnalisées, cliquez sur Actualiser ; votre nouvelle initiative s’affichera comme étant disponible.
    1. Cliquez sur **Ajouter** et affectez-la à votre abonnement.

    > [!NOTE]
    > La création d’initiatives nécessite les informations d’identification du propriétaire de l’abonnement. Pour plus d’informations sur les rôles Azure, consultez [Autorisations dans Azure Security Center](security-center-permissions.md).

1. Pour afficher les recommandations qui en résultent pour votre stratégie, cliquez sur **Recommandations** dans la barre latérale pour ouvrir la page de recommandations. Les recommandations s’affichent avec une étiquette « Personnalisée » et sont disponibles pendant 30 minutes.

    [![Recommandations personnalisées](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer des stratégies de sécurité personnalisées. 

Pour d’autres informations connexes, consultez les articles suivants : 

- [La vue d’ensemble des stratégies de sécurité](tutorial-security-policy.md)
- [Liste des stratégies de sécurité intégrées](security-center-policy-definitions.md)
---
title: Automatisation des workflows dans Azure Security Center | Microsoft Docs
description: Découvrez comment créer et automatiser des workflows dans Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 015b3fb116c4eb16e4280e2f71873e88dccff278
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92344030"
---
# <a name="create-automatic-responses-to-alerts-and-recommendations-with-workflow-automation"></a>Création de réponses automatiques aux alertes et aux recommandations avec l’automatisation de workflow

Chaque programme de sécurité comprend plusieurs workflows pour la réponse aux incidents. Ces processus peuvent inclure l’envoi de notifications aux parties prenantes concernées, le lancement d’un processus de gestion des changements et l’application d’étapes de correction spécifiques. Les experts en sécurité vous conseillent d’automatiser le plus possible les étapes de ces processus. D’une part, l’automatisation réduit votre charge de travail. D’autre part, elle contribue à renforcer votre sécurité en garantissant que les étapes des processus sont effectuées rapidement, de manière cohérente et selon vos exigences prédéfinies.

Cet article décrit la fonctionnalité Automatisation des workflows d’Azure Security Center. Cette fonctionnalité peut déclencher Logic Apps sur les alertes et recommandations de sécurité. Par exemple, vous pouvez définir que Security Center envoie un e-mail à un utilisateur donné quand une alerte se produit. Vous allez également apprendre à créer des applications logiques à l’aide du service [Azure Logic Apps](../logic-apps/logic-apps-overview.md).

> [!NOTE]
> Si vous avez précédemment accédé à la vue Playbooks (préversion) dans la barre latérale, vous retrouverez les mêmes fonctionnalités regroupées avec les fonctionnalités étendues dans la nouvelle page Automatisation des workflows.



## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale (GA)|
|Prix :|Gratuit|
|Rôles et autorisations obligatoires :|**Rôle d’administrateur de sécurité** ou **Propriétaire** sur le groupe de ressources<br>Doit également disposer d’autorisations en écriture sur la ressource cible<br><br>Pour utiliser des workflows Azure Logic Apps, vous devez également disposer des autorisations/rôles Logic Apps suivants :<br> - Les autorisations [Opérateur d’application logique](../role-based-access-control/built-in-roles.md#logic-app-operator) sont nécessaires pour lire/déclencher des applications logiques (ce rôle ne permet pas la création ni la modification d’applications logiques ; il sert uniquement à *exécuter* des applications existantes)<br> - Les autorisations [Contributeur d’application logique](../role-based-access-control/built-in-roles.md#logic-app-contributor) sont requises pour la création et la modification d’applications logiques<br>Si vous souhaitez utiliser des connecteurs d’applications logiques, vous pouvez avoir besoin d’informations d’identification supplémentaires pour vous connecter à leurs services respectifs (par exemple, vos instances Outlook/Teams/Slack).|
|Clouds :|![Oui](./media/icons/yes-icon.png) Clouds commerciaux<br>![Oui](./media/icons/yes-icon.png) National/souverain (US Gov, Chine Gov, autres Gov)|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Créer une application logique et définir le moment de son exécution automatique 

1. Dans la barre latérale de Security Center, sélectionnez **Automatisation des workflows**.

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="Liste d’automatisations de workflows":::

    À partir de cette page, vous pouvez créer des règles d’automatisation, et activer, désactiver ou supprimer des règles existantes.

1. Pour définir un nouveau workflow, cliquez sur **Add workflow automation** (Ajouter une automatisation de workflow). 

    Un volet s’affiche avec les options de votre nouvelle automatisation. Vous pouvez y entrer :
    1. Le nom et la description de l’automatisation.
    1. Les déclencheurs qui lanceront l’exécution de ce workflow automatique. Par exemple, vous pouvez définir que votre application logique s’exécute quand une alerte de sécurité contenant « SQL » est générée.
    1. L’application logique à exécuter lorsque les conditions du déclencheur définies seront remplies. 

        :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="Liste d’automatisations de workflows":::

1. Dans la section Actions, cliquez sur **Create a new one** (Créer) pour commencer le processus de création de l’application logique.

    Vous êtes redirigé vers le service Azure Logic Apps.

    [![Création d’une application logique](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Entrez un nom, un groupe de ressources et un emplacement, puis cliquez sur **Créer**.

1. Dans votre nouvelle application logique, vous pouvez choisir des modèles prédéfinis intégrés dans la catégorie Sécurité. Vous pouvez aussi définir un workflow personnalisé des événements attendus au déclenchement de ce processus.

    Dans le concepteur d’application logique, les déclencheurs suivants des connecteurs Security Center sont pris en charge :

    * **Quand une recommandation Azure Security Center est créée ou déclenchée** : si votre application logique repose sur une recommandation qui est dépréciée ou remplacée, votre automatisation cesse de fonctionner et vous devez mettre à jour le déclencheur. Pour suivre les changements apportés aux recommandations, consultez les [notes de publication Azure Security Center](release-notes.md).

    * **Quand une alerte Azure Security Center est créée ou déclenchée** : vous pouvez personnaliser le déclencheur pour qu’il ne concerne que les alertes dont les niveaux de gravité vous intéressent.
    
    > [!NOTE]
    > Si vous utilisez le déclencheur hérité « Quand une réponse à une alerte Azure Security Center est déclenchée », votre application logique n’est pas lancée par la fonctionnalité Automatisation des workflows. À la place, utilisez l’un des déclencheurs mentionnés ci-dessus. 

    [![Exemple d’application logique](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Après avoir défini votre application logique, revenez au volet de définition de l’automatisation des workflows (« Ajouter une automatisation de workflow »). Cliquez sur **Actualiser** afin de rendre votre nouvelle application logique disponible pour la sélection.

    ![Actualiser](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Sélectionnez votre application logique et enregistrez l’automatisation. Notez que la liste déroulante Application logique affiche uniquement les applications logiques disposant des connecteurs Security Center pris en charge mentionnés ci-dessus.


## <a name="manually-trigger-a-logic-app"></a>Déclencher manuellement une application logique

Vous pouvez également exécuter des applications logiques manuellement quand une alerte ou recommandation de sécurité quelconques s’affichent.

Pour exécuter manuellement une application logique, ouvrez une alerte ou une recommandation, puis cliquez sur **Déclencher l’application logique** :

[![Déclencher manuellement une application logique](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>Schémas des types de données

Pour consulter les schémas d’événements bruts des alertes de sécurité ou les recommandations que les événements ont transmises à l’instance Logic App, consultez [Schémas des types de données pour l’automatisation du workflow](https://aka.ms/ASCAutomationSchemas). Cela peut se révéler utile si vous n’utilisez pas les connecteurs Logic App intégrés du Centre de sécurité mentionnés plus haut, mais le connecteur HTTP générique de Logic App. Vous pouvez utiliser le schéma JSON d’événement pour l’analyser manuellement si vous le souhaitez.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer des applications logiques, à automatiser leur exécution dans Security Center et à les exécuter manuellement. 

Consultez les documents connexes suivants : 

- [Module Microsoft Learn sur la façon d’utiliser l’automatisation des workflows pour automatiser une réponse de sécurité](/learn/modules/resolve-threats-with-azure-security-center/)
- [Recommandations de sécurité dans Azure Security Center](security-center-recommendations.md)
- [Alertes de sécurité dans le Centre de sécurité Azure](security-center-alerts-overview.md)
- [À propos d’Azure Logic Apps](../logic-apps/logic-apps-overview.md)
- [Connecteurs d’applications logiques](/connectors/)
- [Schémas des types de données pour l’automatisation du workflow](https://aka.ms/ASCAutomationSchemas)
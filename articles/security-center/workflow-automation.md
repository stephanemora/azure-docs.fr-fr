---
title: Automatisation des workflows (préversion) dans Azure Security Center | Microsoft Docs
description: Découvrez comment créer et automatiser des workflows dans Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 57351ccf0c6155a1a3532ec9e6481a724e3219aa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75462439"
---
# <a name="workflow-automation-preview"></a>Automatisation des workflows (préversion)

Chaque programme de sécurité comprend plusieurs workflows pour la réponse aux incidents. Ces processus peuvent inclure l’envoi de notifications aux parties prenantes concernées, le lancement d’un processus de gestion des changements et l’application d’étapes de correction spécifiques. Les experts en sécurité vous conseillent d’automatiser le plus possible les étapes de ces processus. D’une part, l’automatisation réduit votre charge de travail. D’autre part, elle contribue à renforcer votre sécurité en garantissant que les étapes des processus sont effectuées rapidement, de manière cohérente et selon vos exigences prédéfinies.

Cet article décrit la fonctionnalité Automatisation des workflows (préversion) d’Azure Security Center. Cette fonctionnalité en préversion peut déclencher des applications logiques sur les alertes et recommandations de sécurité. Par exemple, vous pouvez définir que Security Center envoie un e-mail à un utilisateur donné quand une alerte se produit. Vous allez également apprendre à créer des applications logiques à l’aide du service [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

> [!NOTE]
> Si vous avez précédemment accédé à la vue Playbooks (préversion) dans la barre latérale, vous retrouverez les mêmes fonctionnalités regroupées avec les fonctionnalités étendues dans la nouvelle page Automatisation des workflows (préversion).


## <a name="requirements"></a>Spécifications

* Pour utiliser des workflows Azure Logic Apps, vous devez avoir les autorisations/rôles Logic Apps suivants :

    * Les autorisations [Opérateur d’application logique](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-operator) sont nécessaires pour lire/déclencher des applications logiques (ce rôle ne permet pas la création ou la modification d’applications logiques ; il sert uniquement à *exécuter* des applications existantes)

    * Les autorisations [Contributeur d’application logique](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#logic-app-contributor) sont requises pour la création et la modification d’applications logiques

* Si vous souhaitez utiliser des connecteurs d’applications logiques, vous pouvez avoir besoin d’informations d’identification supplémentaires pour vous connecter à leurs services respectifs (par exemple, vos instances Outlook/Teams/Slack).


## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>Créer une application logique et définir le moment de son exécution automatique 

1. Dans la barre latérale de Security Center, sélectionnez **Automatisation des workflows (préversion)** .

    [![Liste d’automatisations de workflows](media/workflow-automation/list-of-workflow-automations.png)](media/workflow-automation/list-of-workflow-automations.png#lightbox)

    À partir de cette page, vous pouvez créer des règles d’automatisation, et activer, désactiver ou supprimer des règles existantes.  
1. Pour définir un nouveau workflow, cliquez sur **Add workflow automation** (Ajouter une automatisation de workflow). 

    Un volet s’affiche avec les options de votre nouvelle automatisation. Vous pouvez y entrer :
    1. Le nom et la description de l’automatisation.
    1. Les déclencheurs qui lanceront l’exécution de ce workflow automatique. Par exemple, vous pouvez définir que votre application logique s’exécute quand une alerte de sécurité contenant « SQL » est générée.
    1. L’application logique à exécuter lorsque les conditions du déclencheur définies seront remplies. 

        [![Liste d’automatisations de workflows](media/workflow-automation/add-workflow.png)](media/workflow-automation/add-workflow.png#lightbox)

1. Dans la section Actions, cliquez sur **Create a new one** (Créer) pour commencer le processus de création de l’application logique.

    Vous êtes redirigé vers le service Azure Logic Apps.

    [![Création d’une application logique](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. Entrez un nom, un groupe de ressources et un emplacement, puis cliquez sur **Créer**.

1. Dans votre nouvelle application logique, vous pouvez choisir des modèles prédéfinis intégrés dans la catégorie Sécurité. Vous pouvez aussi définir un workflow personnalisé des événements attendus au déclenchement de ce processus.

    Dans le concepteur d’application logique, les déclencheurs suivants dans les connecteurs Security Center sont pris en charge :

    * **When an Azure Security Center Recommendation is created or triggered (Preview)** [Quand une recommandation Azure Security Center est créée ou déclenchée (préversion)]
    * **When an Azure Security Center Alert is created or triggered (Preview)** [Quand une alerte Azure Security Center est créée ou déclenchée (préversion)]
    
    > [!NOTE]
    > Si vous utilisez le déclencheur hérité « Quand une réponse à une alerte Azure Security Center est déclenchée », votre application logique ne sera pas lancée par la fonctionnalité Automatisation des workflows. À la place, utilisez l’un des déclencheurs mentionnés ci-dessus. 

    [![Exemple d’application logique](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. Après avoir défini votre application logique, revenez au volet de définition de l’automatisation de workflow (« Ajouter une automatisation de workflow »). Cliquez sur **Actualiser** afin de rendre votre nouvelle application logique disponible pour la sélection.

    ![Actualiser](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. Sélectionnez votre application logique et enregistrez l’automatisation. Notez que la liste déroulante Application logique affiche uniquement les applications logiques disposant des connecteurs Security Center pris en charge mentionnés ci-dessus.


## <a name="manually-trigger-a-logic-app"></a>Déclencher manuellement une application logique

Vous pouvez également exécuter les applications logiques manuellement à partir d’une recommandation de sécurité affichée.

Pour exécuter manuellement une application logique, ouvrez une recommandation, puis cliquez sur Trigger Logic App (Preview) [Déclencher l’application logique (préversion)] :

[![Déclencher manuellement une application logique](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)

## <a name="data-types-schemas"></a>Schémas des types de données

Pour consulter les schémas d’événements bruts des alertes de sécurité ou les recommandations que les événements ont transmises à l’instance Logic App, consultez [Schémas des types de données pour l’automatisation du workflow](https://aka.ms/ASCAutomationSchemas). Cela peut se révéler utile si vous n’utilisez pas les connecteurs Logic App intégrés du Centre de sécurité mentionnés plus haut, mais le connecteur HTTP générique de Logic App. Vous pouvez utiliser le schéma JSON d’événement pour l’analyser manuellement si vous le souhaitez.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer des applications logiques, à les exécuter manuellement dans Security Center et à automatiser leur exécution. 

Pour d’autres informations connexes, consultez les articles suivants : 

- [Recommandations de sécurité dans Azure Security Center](security-center-recommendations.md)
- [Alertes de sécurité dans le Centre de sécurité Azure](security-center-alerts-overview.md)
- [À propos d’Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)
- [Connecteurs d’applications logiques](https://docs.microsoft.com/connectors/)
- [Schémas des types de données pour l’automatisation du workflow](https://aka.ms/ASCAutomationSchemas)

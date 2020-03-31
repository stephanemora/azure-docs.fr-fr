---
title: Se connecter à Office 365 Outlook
description: Automatiser les tâches et les workflows qui gèrent la messagerie, les contacts et les calendriers dans Office 365 avec Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/08/2020
tags: connectors
ms.openlocfilehash: b0f2b8b9c369fdb42c7e0e7f77fc090424ae3729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732677"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Gérer la messagerie, les contacts et les calendriers dans Office 365 Outlook avec Azure Logic Apps

Avec [Azure Logic Apps](../logic-apps/logic-apps-overview.md) et le [connecteur Office 365 Outlook](/connectors/office365connector/), vous pouvez créer des tâches et des workflows automatisés qui gèrent votre compte Office 365 en générant des applications logiques. Par exemple, vous automatisez les tâches suivantes :

* Recevoir, envoyer et répondre à des e-mails 
* Planifier des réunions sur votre calendrier
* Ajouter et modifier des contacts 

Vous pouvez utiliser n’importe quel déclencheur pour démarrer votre workflow, par exemple, à l’arrivée d’un nouvel e-mail, lorsqu’un élément de calendrier est mis à jour ou quand un événement se produit dans un autre service, comme Salesforce. Vous pouvez utiliser des actions qui répondent à l’événement déclencheur, par exemple, envoyer un e-mail ou créer un événement de calendrier. 

> [!NOTE]
> Pour automatiser les tâches d’un compte @outlook.com ou @hotmail.com, utilisez le [connecteur Outlook.com](../connectors/connectors-create-api-outlook.md).

## <a name="prerequisites"></a>Prérequis

* Un [compte Office 365](https://www.office.com/).

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/). 

* L’application logique à partir de laquelle vous souhaitez accéder à votre compte Office 365 Outlook. Pour démarrer votre workflow avec un déclencheur Office 365 Outlook, vous devez disposer d’une [application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pour ajouter une action Office 365 Outlook à votre workflow, votre application logique doit déjà disposer d’un déclencheur.

## <a name="add-a-trigger"></a>Ajouter un déclencheur

Un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts) désigne un événement qui démarre le workflow dans votre application logique. Cet exemple d’application logique utilise un déclencheur « d’interrogation » qui recherche tous les événements de calendrier mis à jour dans votre compte e-mail, en fonction de l’intervalle et de la fréquence spécifiés.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre application logique vide dans le Concepteur d’application logique.

1. Dans la zone de recherche, entrez `office 365 outlook` en guise de filtre. Cet exemple sélectionne **Lorsqu’un événement à venir démarre bientôt**.
   
   ![Sélectionner un déclencheur pour démarrer votre application logique](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Si vous êtes invité à vous connecter, indiquez vos informations d’identification Office 365, afin que votre application logique puisse se connecter à votre compte. Ou bien, si votre connexion existe déjà, fournissez les informations pour les propriétés du déclencheur.

   Cet exemple sélectionne le calendrier que le déclencheur vérifie, par exemple :

   ![Configurer les propriétés du déclencheur](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. Dans le déclencheur, définissez les valeurs de **Fréquence** et **Intervalle**. Pour ajouter d’autres propriétés de déclencheur disponibles, telles que **Fuseau horaire**, sélectionnez ces propriétés dans la liste **Ajouter un nouveau paramètre**.

   Par exemple, si vous souhaitez que le déclencheur vérifie le calendrier toutes les 15 minutes, définissez **Fréquence** sur **Minute**, et **Intervalle** sur `15`. 

   ![Définir la fréquence et l’intervalle pour le déclencheur](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

À présent, ajoutez une action qui s’exécute après l’activation du déclencheur. Par exemple, vous pouvez ajouter l’action Twilio **Envoyer un message**, qui envoie un texte lorsqu’un événement de calendrier démarre dans 15 minutes.

## <a name="add-an-action"></a>Ajouter une action

Une [action](../logic-apps/logic-apps-overview.md#logic-app-concepts) est une opération qui est définie par le workflow dans votre application logique. Cet exemple d’application logique crée un contact dans Office 365 Outlook. Vous pouvez utiliser la sortie d’un autre déclencheur ou d’une autre action pour créer le contact. Par exemple, supposez que votre application logique utilise le déclencheur Dynamics 365, **Lorsqu’un enregistrement est créé**. Vous pouvez ajouter l’action Office 365 Outlook **Créer un contact** et utiliser les sorties du déclencheur SalesForce pour créer le contact.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre application logique dans le Concepteur d’applications logiques.

1. Pour ajouter une action comme dernière étape à votre workflow, sélectionnez **Nouvelle étape**. 

   Pour ajouter une action entre des étapes, placez votre pointeur au-dessus de la flèche qui les sépare. Cliquez sur le signe ( **+** ) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Dans la zone de recherche, entrez `office 365 outlook` en guise de filtre. Cet exemple sélectionne **Créer un contact**.

   ![Sélectionner l’action à exécuter dans votre application logique](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Si vous êtes invité à vous connecter, indiquez vos informations d’identification Office 365, afin que votre application logique puisse se connecter à votre compte. Ou bien, si votre connexion existe déjà, fournissez les informations pour les propriétés de l’action.

   Cet exemple sélectionne le dossier de contacts dans lequel l’action crée le contact, par exemple :

   ![Configurer les propriétés de l’action](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Pour ajouter d’autres propriétés d’action disponibles, sélectionnez ces propriétés dans la liste **Ajouter un nouveau paramètre**.

1. Dans la barre d’outils du Concepteur, sélectionnez **Enregistrer**.

## <a name="connector-specific-details"></a>Détails spécifiques du connecteur

Pour plus d’informations techniques concernant les déclencheurs, les actions et les limites, telles qu’elles sont décrites dans le fichier Swagger du connecteur, consultez la [page de référence du connecteur](/connectors/office365connector/). 

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)

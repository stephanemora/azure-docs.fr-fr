---
title: Se connecter à Azure Event Hubs - Azure Logic Apps | Microsoft Docs
description: Gérer et surveiller les événements à l’aide d’Azure Event Hubs et d’Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/21/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 86fc1c3542bea1be840041bb73df15631c066c7e
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294970"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Surveillez, recevez et envoyez des événements avec Azure Event Hubs et Azure Logic Apps 

Cet article explique comment vous pouvez surveiller et gérer les événements envoyés à [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) depuis une application logique à l’aide du connecteur Azure Event Hubs. De cette façon, vous pouvez créer des applications logiques qui automatisent les tâches et les flux de travail pour vérifier, envoyer et recevoir des événements à partir de votre hub d’événements. 

Si vous n’avez pas d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. Si vous ne connaissez pas les applications logiques, consultez les sections [Présentation d’Azure Logic Apps](../logic-apps/logic-apps-overview.md) et [Démarrage rapide : créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Pour obtenir des informations techniques spécifiques aux connecteurs, consultez la <a href="https://docs.microsoft.com/connectors/eventhubs/" target="blank">documentation de référence du connecteur Azure Event Hubs</a>.

## <a name="prerequisites"></a>Prérequis

* Un [espace de noms Azure Event Hubs et un hub d’événements](../event-hubs/event-hubs-create.md)

* L’application logique à partir de laquelle vous souhaitez accéder à votre hub d’événements. Pour démarrer votre application logique avec un déclencheur Azure Event Hubs, vous avez besoin d’une [application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Vérifier les autorisations et obtenir la chaîne de connexion

Pour que votre application logique accède à votre hub d’événements, vérifiez vos autorisations et obtenez la chaîne de connexion pour votre espace de noms Event Hubs.

1. Connectez-vous au <a href="https://portal.azure.com" target="_blank">Portail Azure</a>. 

2. Accédez à votre *espace de noms* Event Hubs, et non à un hub d’événements spécifique. Dans la page de l’espace de noms, sous **Paramètres**, choisissez **Stratégies d’accès partagé**. Sous **Revendications**, vérifiez que vous disposez des autorisations **Gérer** pour cet espace de noms.

   ![Gérer les autorisations pour votre espace de noms Event Hubs](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. Si vous souhaitez pouvoir entrer manuellement vos informations de connexion par la suite, obtenez la chaîne de connexion pour votre espace de noms Event Hubs. 

   1. Sous **Stratégie**, choisissez **RootManageSharedAccessKey**. 

   2. Trouvez la chaîne de connexion de la clé primaire. Choisissez le bouton Copier et enregistrez la chaîne de connexion pour l’utiliser plus tard.

      ![Copier la chaîne de connexion d’espace de noms Event Hubs](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Pour vérifier que votre chaîne de connexion est bien associée à votre espace de noms Event Hubs ou à un hub d’événements spécifique, assurez-vous que la chaîne de connexion ne contient pas le paramètre `EntityPath`. Si vous trouvez ce paramètre, la chaîne de connexion est dédiée à une « entité » Event Hub spécifique et ne peut pas être utilisée avec votre application logique.

4. Passez maintenant à [Ajouter un déclencheur Event Hubs](#add-trigger) ou [Ajouter une action Event Hubs](#add-action).

<a name="add-trigger"></a>

## <a name="add-an-event-hubs-trigger"></a>Ajouter un déclencheur Event Hubs

Dans Azure Logic Apps, chaque application logique doit démarrer avec un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts), qui s’active lorsqu’un événement spécifique se produit ou lorsqu’une condition particulière est remplie. Chaque fois que le déclencheur s’active, le moteur Logic Apps crée une instance d’application logique et lance l’exécution du flux de travail de votre application.

Cet exemple montre comment vous pouvez démarrer un flux de travail d’application logique lorsque de nouveaux événements sont envoyés à votre hub d’événements. 

1. Dans le portail Azure ou Visual Studio, créez une application logique vide, qui ouvre le Concepteur d'applications logiques. Cet exemple utilise le portail Azure.

2. Dans la zone de recherche, entrez « hubs d’événements » en tant que filtre. Dans la liste des déclencheurs, sélectionnez le déclencheur souhaité. 

   Cet exemple utilise ce déclencheur : **Event Hubs - Lorsque des événements sont disponibles dans Event Hub**.

   ![Sélectionner le déclencheur](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

3. Si vous êtes invité à entrer les informations de connexion, [créez votre connexion Event Hubs maintenant](#create-connection). Ou bien, si votre connexion existe déjà, fournissez les informations nécessaires pour le déclencheur.

   1. À partir de la liste des **noms de hub d’événements**, sélectionnez le hub d’événements à surveiller.

      ![Spécifier un hub Event Hubs ou un groupe de consommateurs](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

   2. Sélectionnez l’intervalle et la fréquence à laquelle vous souhaitez que le déclencheur recherche le hub d’événements.
 
   3. Pour sélectionner certaines des options avancées du déclencheur, choisissez **Afficher les options avancées**.
   
      ![Options avancées du déclencheur](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-advanced.png)

      | Propriété | Détails | 
      |----------|---------| 
      | Type de contenu  | Sélectionnez le type de contenu de l’événement. La valeur par défaut est « application/octet-stream ». |
      | Schéma du contenu | Entrez le schéma du contenu au format JSON pour les événements qui sont lus à partir de l’Event Hub. |
      | Nom du groupe de consommateurs | Entrez le [nom du groupe de consommateurs](../event-hubs/event-hubs-features.md#consumer-groups) Event Hub pour la lecture d’événements. Si ce champ n’est pas spécifié, le groupe de consommateurs par défaut est utilisé. |
      | Clé de partition minimum | Entrez l’ID de [partition](../event-hubs/event-hubs-features.md#partitions) minimum à lire. Par défaut, toutes les partitions sont lues. |
      | Clé de partition maximum | Entrez l’ID de [partition](../event-hubs/event-hubs-features.md#partitions) maximum à lire. Par défaut, toutes les partitions sont lues. |
      | Nombre d’événements maximum | Entrez une valeur pour le nombre maximum d’événements. Le déclencheur retourne entre 1 et le nombre d’événements spécifié par cette propriété. |
      |||

4. Une fois terminé, dans la barre d’outils du concepteur, choisissez **Enregistrer**.

5. Continuez maintenant à ajouter une ou plusieurs actions à votre application logique pour les tâches à effectuer avec les résultats du déclencheur.

> [!NOTE]
> Tous les déclencheurs Event Hub sont des déclencheurs *d’interrogation longue*, ce qui signifie que lorsqu’un déclencheur est activé, il traite tous les événements, puis attend 30 secondes le temps qu’un plus grand nombre d’événements s’affichent dans votre hub d’événements.
> Si aucun événement n’est reçu dans les 30 secondes, l’exécution du déclencheur est ignorée. Dans le cas contraire, le déclencheur poursuit la lecture des événements jusqu’à ce que votre hub d’événements soit vide.
> La prochaine interrogation de déclencheur est basée sur l’intervalle de récurrence que vous spécifiez dans les propriétés du déclencheur.

<a name="add-action"></a>

## <a name="add-an-event-hubs-action"></a>Ajouter une action Event Hubs

Dans Azure Logic Apps, une [action](../logic-apps/logic-apps-overview.md#logic-app-concepts) est une étape de votre flux de travail qui suit un déclencheur ou une autre action. Pour cet exemple, l’application logique démarre avec un déclencheur Event Hubs qui recherche de nouveaux événements dans votre hub d'événements. 

1. Dans le portail Azure ou Visual Studio, ouvrez votre application logique dans le Concepteur d’applications logiques. Cet exemple utilise le portail Azure.

2. Sous le déclencheur ou l’action, choisissez **Nouvelle étape** > **Ajouter une action**.

   Pour ajouter une action entre des étapes, déplacez votre souris sur la flèche de connexion. 
   Cliquez sur le signe plus (**+**) qui s’affiche, puis choisissez **Ajouter une action**.

3. Dans la zone de recherche, entrez « hubs d’événements » en tant que filtre.
Dans la liste des actions, sélectionnez l’action souhaitée. 

   Pour cet exemple, sélectionnez l’action **Event Hubs - Envoyer un événement**

   ![Sélectionner « Event Hubs - Envoyer un événement »](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

4. Si vous êtes invité à entrer les informations de connexion, [créez votre connexion Event Hubs maintenant](#create-connection). Ou bien, si votre connexion existe déjà, fournissez les informations nécessaires pour l’action.

   | Propriété | Obligatoire | Description | 
   |----------|----------|-------------|
   | Nom de l’Event Hub | OUI | Sélectionnez le hub d’événements où vous voulez envoyer l’événement. | 
   | Contenu de l’événement | Non  | Le contenu pour l’événement que vous souhaitez envoyer | 
   | properties | Non  | Les valeurs et les propriétés de l’application à envoyer | 
   |||| 

   Par exemple :  

   ![Sélectionnez le nom du hub d’événements et fournissez le contenu de l’événement](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

5. Une fois terminé, dans la barre d’outils du concepteur, choisissez **Enregistrer**.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Se connecter à votre hub d'événements

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Lorsque vous êtes invité à entrer des informations de connexion, fournissez ces précisions :

   | Propriété | Obligatoire | Valeur | Description | 
   |----------|----------|-------|-------------|
   | Nom de connexion | OUI | <*connection-name*> | Le nom à créer pour votre connexion |
   | Espace de noms Event Hubs | OUI | <*event-hubs-namespace*> | Sélectionnez l’espace de noms Event Hubs que vous souhaitez utiliser. | 
   |||||  

   Par exemple : 

   ![Créer une connexion de hub d’événements](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Pour entrer manuellement la chaîne de connexion, choisissez **Entrer manuellement les informations de connexion**. 
   Découvrez [comment trouver votre chaîne de connexion](#permissions-connection-string).

2. Sélectionnez la stratégie Event Hubs à utiliser, si elle n’est pas déjà sélectionnée. Cliquez sur **Créer**.

   ![Créer une connexion de hub d’événements, partie 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Après avoir créé votre connexion, poursuivez avec [Ajouter un déclencheur Event Hubs](#add-trigger) ou [Ajouter une action Event Hubs](#add-action).

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques, telles que les déclencheurs, actions et limites, comme décrit dans le fichier Swagger du connecteur, consultez la [page de référence du connecteur](/connectors/eventhubs/). 

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
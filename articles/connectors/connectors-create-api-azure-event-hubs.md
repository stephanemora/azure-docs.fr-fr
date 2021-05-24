---
title: Se connecter à Azure Event Hubs
description: Connectez-vous à votre Event Hub et ajoutez un déclencheur ou une action à votre flux de travail dans Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 05/03/2021
tags: connectors
ms.openlocfilehash: 7f82debf0cc09d032b00de8197cf873c01801353
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108755582"
---
# <a name="connect-to-an-event-hub-from-workflows-in-azure-logic-apps"></a>Se connecter à un Event Hub à partir des flux de travail dans Azure Logic Apps

Le connecteur Azure Event Hubs vous permet de connecter vos flux de travail d’application logique à des Event Hubs dans Azure. Vous pouvez ensuite faire en sorte que vos flux de travail surveillent et gèrent les événements envoyés à un Event Hub. Par exemple, votre flux de travail peut vérifier, envoyer et recevoir des événements de votre Event Hub. Cet article fournit un guide de prise en main de l’utilisation du connecteur Azure Event Hubs en expliquant comment se connecter à un Event Hub et ajouter une action ou un déclencheur Event Hubs à votre flux de travail.

Pour plus d’informations sur les Event Hubs Azure ou sur Azure Logic Apps, consultez [Présentation d’Azure Event hubs](../event-hubs/event-hubs-about.md) ou [Présentation d’Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="available-operations"></a>Opérations disponibles

Pour toutes les opérations et autres informations techniques, telles que les propriétés, les limites, etc., consultez la [page de référence du connecteur Event Hubs](/connectors/eventhubs/).

> [!NOTE]
> Pour les applications logiques hébergées dans un [environnement de service d’intégration (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), la version ISE de ce connecteur applique les [limites de messages de l’ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) à la place.

## <a name="prerequisites"></a>Prérequis

* Un compte et un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/). 

* Un [Espace de noms Event Hubs et un Event Hub](../event-hubs/event-hubs-create.md)

* Le flux de travail d’application logique dans lequel vous souhaitez accéder à votre Event Hub

  Pour démarrer un flux de travail avec un déclencheur Event Hubs, vous avez besoin d’un flux de travail vide. Si vous débutez avec [Azure Logic Apps](../logic-apps/logic-apps-overview.md), essayez ce [guide de démarrage rapide pour créer un exemple de flux de travail d’application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Vérifier les autorisations et obtenir la chaîne de connexion

Pour vous assurer que votre flux de travail peut accéder à votre Event Hub, vérifiez vos autorisations et récupérez la chaîne de connexion pour l’espace de noms de votre Event Hub.

1. Dans le [portail Azure](https://portal.azure.com), accédez à l’*espace de noms* de votre Event Hubs, et non à un Event Hub spécifique.

1. Dans le menu de l’espace de noms, sous **Paramètres**, sélectionnez **Stratégies d’accès partagé**. Dans la colonne **Revendications**, vérifiez que vous disposez au moins des autorisations de **gestion** pour cet espace de noms.

   ![Capture d’écran montrant le Portail Azure, votre espace de noms Event Hubs et les autorisations de gestion dans la colonne « Revendications ».](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Si vous souhaitez pouvoir entrer manuellement vos informations de connexion par la suite, obtenez la chaîne de connexion pour votre espace de noms Event Hub.

   1. Dans la colonne **Stratégie**, sélectionnez **RootManageSharedAccessKey**.

   1. Trouvez la chaîne de connexion de la clé primaire. Copiez et enregistrez la chaîne de connexion en vue d’une utilisation ultérieure.

      ![Capture d’écran montrant la chaîne de connexion de la clé primaire avec le bouton de copie sélectionné.](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Pour vérifier que votre chaîne de connexion est bien associée à votre espace de noms Event Hubs ou à un hub d’événements spécifique, assurez-vous que la chaîne de connexion ne contient pas le paramètre `EntityPath`. Si vous trouvez ce paramètre, la chaîne de connexion concerne une « entité » Event Hubs spécifique et elle ne peut pas être utilisée avec votre flux de travail.

<a name="create-connection"></a>

## <a name="create-an-event-hub-connection"></a>Créer une connexion Event Hub

Lorsque vous ajoutez un déclencheur ou une action Event Hubs pour la première fois, vous êtes invité à créer une connexion à votre Event Hub.

1. Lorsque vous y êtes invité, choisissez une des options suivantes :

   * Fournissez les informations de connexion suivantes :

     | Propriété | Obligatoire | Valeur | Description |
     |----------|----------|-------|-------------|
     | **Nom de connexion** | Oui | <*connection-name*> | Nom à créer pour votre connexion |
     | **Espace de noms Event Hubs** | Oui | <*event-hubs-namespace*> | Sélectionnez l’espace de noms Event Hubs que vous souhaitez utiliser. |
     |||||

   * Pour entrer manuellement la chaîne de connexion que vous avez enregistrée, sélectionnez **Entrer manuellement les informations de connexion**. Découvrez [comment trouver votre chaîne de connexion](#permissions-connection-string).

1. Sélectionnez la stratégie Event Hubs à utiliser, si elle n’est pas déjà sélectionnée., puis sélectionnez **Créer**.

   ![Capture d’écran montrant les informations de connexion fournies avec l’option de création sélectionnée.](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

1. Après avoir créé votre connexion, poursuivez avec [Ajouter un déclencheur Event Hubs](#add-trigger) ou [Ajouter une action Event Hubs](#add-action).

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Ajouter un déclencheur Event Hubs

Dans Azure Logic Apps, chaque flux de travail doit démarrer par un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts), qui s’active lorsqu’une condition particulière est remplie. Chaque fois que le déclencheur est activé, le service Logic Apps crée une instance de flux de travail et commence à exécuter les étapes du flux de travail.

Les étapes suivantes décrivent comment ajouter un déclencheur, par exemple **Quand des événements sont disponibles dans Event Hub**. Cet exemple montre comment ajouter un déclencheur qui vérifie la présence de nouveaux événements dans votre Event Hub et démarre l’exécution du flux de travail quand de nouveaux événements sont détectés.

1. Dans le concepteur Logic Apps, ouvrez votre flux de travail d’application logique vierge s’il n’est pas déjà ouvert.

1. Dans la zone de recherche des opérations, entrez `event hubs`. Dans la liste des déclencheurs, sélectionnez le déclencheur **Quand des événements sont disponibles dans Event Hub**.

   ![Sélectionner le déclencheur](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Si vous êtes invité à créer une connexion à votre Event Hub, [fournissez les informations de connexion demandées](#create-connection).

1. Dans le déclencheur, fournissez des informations sur l’Event Hub que vous souhaitez surveiller, par exemple :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom de l’Event Hub** | Oui | Nom du hub Event Hubs que vous souhaitez surveiller |
   | **Type de contenu** | Non | Type de contenu de l’événement. Par défaut, il s’agit de `application/octet-stream`. |
   | **Nom du groupe de consommateurs** | Non | [Nom du groupe de consommateurs Event Hub](../event-hubs/event-hubs-features.md#consumer-groups) pour la lecture d’événements. Si ce champ n’est pas spécifié, le groupe de consommateurs par défaut est utilisé. |
   | **Nombre d’événements maximum** | Non | Nombre maximal d’événements. Le déclencheur retourne entre 1 et le nombre d’événements spécifié par cette propriété. |
   | **Intervalle** | Oui | Nombre entier positif qui décrit la fréquence à laquelle le flux de travail s’exécute en fonction de la fréquence |
   | **Fréquence** | Oui | Unité de temps à utiliser pour la récurrence |
   ||||

   Pour plus de propriétés, ouvrez la liste **Ajouter un nouveau paramètre**. La sélection d’un paramètre ajoute cette propriété à la carte de déclencheur, par exemple :

   ![Propriétés de déclencheur](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   **Plus de propriétés**

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Schéma du contenu** | Non | Schéma du contenu au format JSON pour les événements qui sont lus à partir de votre Event Hub. Par exemple, si vous spécifiez le schéma de contenu, vous pouvez déclencher le flux de travail uniquement pour les événements qui correspondent au schéma. |
   | **Clé de partition minimum** | Non | Entrez l’ID de [partition](../event-hubs/event-hubs-features.md#partitions) minimum à lire. Par défaut, toutes les partitions sont lues. |
   | **Clé de partition maximum** | Non | Entrez l’ID de [partition](../event-hubs/event-hubs-features.md#partitions) maximum à lire. Par défaut, toutes les partitions sont lues. |
   | **Fuseau horaire** | Non | S’applique uniquement quand vous spécifiez une heure de début, car ce déclencheur n’accepte pas le décalage UTC. Sélectionnez le fuseau horaire à appliquer. <p>Pour plus d’informations, consultez [Créer et exécuter des tâches et des workflows récurrents avec Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   | **Heure de début** | Non | Fournissez une heure de début au format suivant : <p>AAAA-MM-JJThh:mm:ss si vous sélectionnez un fuseau horaire<p>-ou-<p>AAAA-MM-JJThh:mm:ssZ si vous ne sélectionnez pas de fuseau horaire<p>Pour plus d’informations, consultez [Créer et exécuter des tâches et des workflows récurrents avec Azure Logic Apps](../connectors/connectors-native-recurrence.md). |
   ||||

1. Lorsque c’est chose faite, dans la barre d’outils du concepteur, sélectionnez **Enregistrer**.

1. Maintenant, continuez à ajouter une ou plusieurs actions afin de pouvoir effectuer d’autres tâches à l’aide des sorties du déclencheur.

   Par exemple, pour filtrer les événements basés sur une valeur spécifique, telle qu’une catégorie, vous pouvez ajouter une condition pour que l’action **Envoyer un événement** envoie uniquement les événements qui répondent à votre condition. 

## <a name="trigger-polling-behavior"></a>Comportement d’interrogation du déclencheur

Tous les déclencheurs Event Hub sont des déclencheurs *d’interrogation longue*, ce qui signifie que le déclencheur traite tous les événements, puis attend 30 secondes par partition le temps qu’un plus grand nombre d’événements s’affichent dans votre Event Hub. 

Par exemple, si le déclencheur est configuré avec quatre partitions, ce délai peut prendre jusqu’à deux minutes avant que le déclencheur ne termine d’interroger toutes les partitions. Si aucun événement n’est reçu dans ce délai, l’exécution du déclencheur est ignorée. Dans le cas contraire, le déclencheur poursuit la lecture des événements jusqu’à ce que votre Event Hub. La prochaine interrogation de déclencheur est basée sur l’intervalle de récurrence que vous spécifiez dans les propriétés du déclencheur.

## <a name="trigger-checkpoint-behavior"></a>Comportement du point de contrôle du déclencheur

Lorsqu’un déclencheur Event Hubs lit les événements de chaque partition dans un Event Hub, le déclencheur utilise son propre état pour de conserver les informations sur le décalage du flux (la position de l’événement dans une partition) et les partitions sur lesquelles le déclencheur lit les événements.

Chaque fois que votre flux de travail s’exécute, le déclencheur lit les événements dans une partition, en commençant par le décalage de flux conservé par l’état du déclencheur. En mode tourniquet, le déclencheur effectue une itération au sein de chaque partition dans le Event Hub et lit les événements lors des exécutions de déclencheurs suivantes. Une exécution unique obtient les événements d’une partition unique, une seule fois.

Le déclencheur n’utilise pas cette fonctionnalité de point de contrôle dans le stockage, ce qui n’entraîne aucun coût supplémentaire. Toutefois, le point essentiel est que la mise à jour du déclencheur Event Hubs réinitialise l’état du déclencheur, ce qui peut amener le déclencheur à lire des événements en début de flux.

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Ajouter une action Event Hubs

Dans Azure Logic Apps, une [action](../logic-apps/logic-apps-overview.md#logic-app-concepts) suit le déclencheur ou une autre action et effectue une opération dans votre flux de travail. Les étapes suivantes décrivent la manière générale d’ajouter une action, par exemple **Envoyer un événement**. Pour cet exemple, le flux de travail démarre avec un déclencheur Event Hubs qui recherche de nouveaux événements dans votre Event Hub.

1. Dans le concepteur Logic Apps, ouvrez votre flux de travail d’application logique s’il n’est pas déjà ouvert.

1. Dans le déclencheur ou une autre action, ajoutez une nouvelle étape.

   Pour ajouter une étape entre les étapes existantes, déplacez votre souris sur la flèche. Cliquez sur le signe ( **+** ) qui s’affiche, puis sélectionnez **Ajouter une action**.

1. Dans la zone de recherche des opérations, entrez `event hubs`. Dans la liste des actions, sélectionnez l’action appelée **Envoyer l’événement**.

   ![Sélectionner l’action « Envoyer un événement »](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Si vous êtes invité à créer une connexion à votre Event Hub, [fournissez les informations de connexion demandées](#create-connection).

1. Dans l'action, fournissez des informations sur les événements que vous souhaitez envoyer.

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom de l’Event Hub** | Oui | Event Hub où vous voulez envoyer l’événement |
   | **Contenu** | Non | Le contenu pour l’événement que vous souhaitez envoyer |
   | **Propriétés** | Non | Les valeurs et les propriétés de l’application à envoyer |
   | **Clé de partition** | Non | ID [partition](../event-hubs/event-hubs-features.md#partitions) auquel envoyer l’événement |
   ||||

   Pour plus de propriétés, ouvrez la liste **Ajouter un nouveau paramètre**. La sélection d'un paramètre ajoute cette propriété à la carte action, par exemple :

   ![Sélectionnez le nom du hub Event Hub et fournissez le contenu de l’événement](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   Par exemple, vous pouvez envoyer la sortie de votre déclencheur Event Hubs à un autre Event Hub :

   ![Envoyer un exemple d’événement](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. Lorsque c’est chose faite, dans la barre d’outils du concepteur, sélectionnez **Enregistrer**.

## <a name="connector-reference"></a>Référence de connecteur

Pour toutes les opérations et autres informations techniques, telles que les propriétés, les limites, etc., consultez la [page de référence du connecteur Event Hubs](/connectors/eventhubs/).

> [!NOTE]
> Pour les applications logiques hébergées dans un [environnement de service d’intégration (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), la version ISE de ce connecteur applique les [limites de messages de l’ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) à la place.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)
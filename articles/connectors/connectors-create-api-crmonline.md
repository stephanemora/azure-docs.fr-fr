---
title: Se connecter à Dynamics 365
description: Créer et gérer des enregistrements avec les API REST de base de Dynamics 365 (en ligne) et Azure Logic Apps
services: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: 9837b68fbfba783a468712d8ba1883b198af4954
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789894"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Gérer les enregistrements Dynamics 365 avec Azure Logic Apps

Avec Azure Logic Apps et le connecteur Dynamics 365, vous pouvez créer des flux de travail et des tâches automatisés basés sur vos enregistrements dans Dynamics 365. Vos flux de travail peuvent créer des enregistrements, mettre à jour des éléments, retourner des enregistrements et bien plus encore, dans votre compte Dynamics 365. Vous pouvez inclure des actions dans vos applications logiques qui obtiennent des réponses de Dynamics 365 et mettent la sortie à la disposition d’autres actions. Par exemple, quand un élément est mis à jour dans Dynamics 365, vous pouvez envoyer un e-mail à l’aide d’Office 365.

Cet article vous explique comment générer une application logique qui crée une tâche dans Dynamics 365 quand un enregistrement de prospect est créé dans Dynamics 365.
Si vous débutez avec les applications logiques, consultez [Qu’est-ce qu’Azure Logic Apps ?](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* Un [compte Dynamics 365](https://dynamics.microsoft.com)

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L’application logique à partir de laquelle vous souhaitez accéder à votre compte Dynamics 365. Pour démarrer votre application logique avec un déclencheur Dynamics 365, vous avez besoin d’une [application logique vide](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-dynamics-365-trigger"></a>Ajouter un déclencheur Dynamics 365

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Tout d’abord, ajoutez un déclencheur Dynamics 365 qui se déclenche quand un nouveau prospect apparaît dans Dynamics 365.

1. Dans le [portail Azure](https://portal.azure.com), ouvrez votre application logique vide dans le concepteur d’application logique, si elle n’est pas déjà ouverte.

1. Dans la zone de recherche, entrez le filtre « Dynamics 365 ». Pour cet exemple, dans la liste des déclencheurs, sélectionnez ce déclencheur : **Lorsqu’un enregistrement est créé**

   ![Sélectionner le déclencheur](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Si vous êtes invité à vous connecter à Dynamics 365, faites-le maintenant.

1. Fournissez les détails suivants au sujet déclencheur :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom de l’organisation** | OUI | Nom de l’instance de Dynamics 365 de votre organisation à superviser, par exemple, « Contoso » |
   | **Nom de l’entité** | OUI | Nom de l’entité à superviser, par exemple, « Leads » (Prospects) | 
   | **Fréquence** | OUI | Unité de temps, avec intervalles, à utiliser pour vérifier l’existence de mises à jour pour le déclencheur |
   | **Intervalle** | OUI | Nombre de secondes, de minutes, d’heures, de jours, de semaines ou de mois qui doivent s’écouler avant la recherche suivante |
   ||| 

   ![Détails du déclencheur](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Ajouter une action Dynamics 365

Ajoutez maintenant l’action Dynamics 365 qui crée un enregistrement de tâche pour l’enregistrement du nouveau prospect.

1. Sous votre déclencheur, choisissez **Nouvelle étape**.

1. Dans la zone de recherche, entrez le filtre « Dynamics 365 ». Dans la liste des actions, sélectionnez cette action : **Créer un enregistrement**

   ![Action select](./media/connectors-create-api-crmonline/select-action.png)

1. Fournissez les détails suivants au sujet de l’action :

   | Propriété | Obligatoire | Description |
   |----------|----------|-------------|
   | **Nom de l’organisation** | OUI | Instance de Dynamics 365 où vous souhaitez créer l’enregistrement ; bien que cette instance puisse différer de l’instance de votre déclencheur, elle porte le nom « Contoso » dans cet exemple. |
   | **Nom de l’entité** | OUI | Entité où vous souhaitez créer l’enregistrement, par exemple, « Tasks » (Tâches) |
   | | |

   ![Détails de l’action](./media/connectors-create-api-crmonline/action-details.png)

1. Quand la zone **Objet** s’affiche dans votre action, cliquez dans cette zone afin qu’apparaisse la liste des contenus dynamiques. Dans cette liste, sélectionnez les valeurs de champ à inclure dans l’enregistrement de tâche associé à l’enregistrement du nouveau prospect :

   | Champ | Description |
   |-------|-------------|
   | **Nom** | Nom de famille du prospect en tant que contact principal dans l’enregistrement |
   | **Rubrique** | Nom descriptif du prospect dans l’enregistrement |
   | | |

   ![Détails de l’enregistrement de la tâche](./media/connectors-create-api-crmonline/create-record-details.png)

1. Dans la barre d’outils du concepteur, choisissez **Enregistrer** pour votre application logique. 

1. Pour lancer manuellement l’application logique, sélectionnez **Exécuter** dans la barre d’outils du concepteur.

   ![Exécuter une application logique](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Créez maintenant un enregistrement de prospect dans Dynamics 365 afin de pouvoir déclencher le flux de travail de votre application logique.

## <a name="add-filter-or-query"></a>Ajouter un filtre ou une requête

Pour spécifier comment filtrer les données dans une action Dynamics 365, choisissez **Afficher les options avancées** dans cette action. Vous pouvez ensuite ajouter une requête de filtre ou de tri.
Par exemple, vous pouvez utiliser une requête de filtre pour obtenir uniquement les comptes actifs et trier ces enregistrements par nom de compte. Pour accomplir cette tâche, effectuez les étapes suivantes :

1. Sous **Requête de filtre**, entrez cette requête de filtre OData : `statuscode eq 1`

2. Sous **Trier par**, quand la liste de contenus dynamiques s’affiche, sélectionnez **Nom du compte**. 

   ![Spécifier un filtre et un ordre de tri](./media/connectors-create-api-crmonline/advanced-options.png)

Pour plus d’informations, consultez ces options de requête système de l’API web Dynamics 365 Customer Engagement :

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Bonnes pratiques pour les options avancées

Quand vous spécifiez une valeur pour un champ dans une action ou un déclencheur, le type de données de la valeur doit correspondre au type de champ, que vous entriez la valeur manuellement ou la sélectionniez dans la liste de contenus dynamiques.

Ce tableau décrit certains des types de champs et les types de données requis pour leurs valeurs.

| Type de champ | Type de données requis | Description | 
|------------|--------------------|-------------|
| Champs de texte | Ligne de texte unique | Ces champs de texte nécessitent une seule ligne de texte ou du contenu dynamique qui a le type texte. <p><p>*Champs de l’exemple* : **Description** et **Catégorie** | 
| Champs de type entier | Nombre entier | Certains champs nécessitent un entier ou un contenu dynamique qui a le type entier. <p><p>*Exemples de champs* : **Pourcentage d’achèvement** et **Durée** | 
| Champs de date | Date et heure | Certains champs nécessitent une date au format mm/jj/aaaa ou un contenu dynamique qui a le type date. <p><p>*Exemples de champs* : **Créé le**, **Date de début**, **Début réel**, **Fin réelle** et **Date d’échéance** | 
| Champs nécessitant à la fois un ID d’enregistrement et un type de recherche | Clé primaire | Certains champs qui font référence à un autre enregistrement d’entité nécessitent un ID d’enregistrement et un type de recherche. | 
||||

Sur la base de ces types de champs, voici des exemples de champs dans les déclencheurs et actions Dynamics 365 qui nécessitent un ID d’enregistrement et le type de recherche. Cette exigence signifie que toute valeur que vous sélectionnez dans la liste dynamique ne fonctionne pas.

| Champ | Description |
|-------|-------------|
| **Propriétaire** | Doit être un ID d’utilisateur valide ou un ID d’enregistrement d’équipe. |
| **Type de propriétaire** | Doit être `systemusers` ou `teams`. |
| **Concernant** | Doit être un ID d’enregistrement valide, tel qu’un ID d’enregistrement de contact ou de compte. |
| **Type concernant** | Doit être un type de recherche, tel que `accounts` ou `contacts`. |
| **Client** | Doit être un ID d’enregistrement valide, tel qu’un ID d’enregistrement de contact ou de compte. |
| **Type de client** | Doit être le type de recherche, par exemple `accounts` ou `contacts`. |
|||

Dans cet exemple, l’action nommée **Créer un enregistrement** crée un enregistrement de tâche :

![Créer un enregistrement de tâche avec des ID d’enregistrement et des types de recherche](./media/connectors-create-api-crmonline/create-record-advanced.png)

Cette action assigne l’enregistrement de tâche à un ID d’utilisateur ou d’enregistrement d’équipe spécifique, en fonction de l’ID d’enregistrement dans le champ **Propriétaire** et du type de recherche dans le champ **Type de propriétaire** :

![ID d’enregistrement et type de recherche pour Propriétaire](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Cette action ajoute également un enregistrement de compte qui est associé à l’ID d’enregistrement ajouté dans le champ **Concernant** et au type de recherche dans le champ **Type concernant** :

![ID d’enregistrement et type de recherche pour Concernant](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Rechercher l’ID d’enregistrement

Pour rechercher un ID d’enregistrement, effectuez les étapes suivantes :

1. Dans Dynamics 365, ouvrez un enregistrement, comme un enregistrement de compte.

2. Dans la barre d’outils des actions, choisissez une des étapes suivantes :

   * Choisissez **Isoler**. ![Isoler l’enregistrement](./media/connectors-create-api-crmonline/popout-record.png) 
   * Choisissez **Envoyer un lien par e-mail** afin de pouvoir copier l’URL complète dans votre programme d’e-mail par défaut.

   L’ID d’enregistrement apparaît dans l’URL entre les caractères d’encodage `%7b` et `%7d` :

   ![Rechercher l’ID d’enregistrement](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Résoudre les problèmes liés aux échecs d’exécution

Pour rechercher et examiner les étapes ayant échoué dans votre application logique, vous pouvez afficher l’historique des exécutions de votre application logique, l’état, les entrées, les sorties, etc.

1. Dans le portail Azure, dans le menu principal de votre application logique, choisissez **Vue d’ensemble**. Dans la section **Historique des exécutions**, qui montre tous les états d’exécution pour votre application logique, sélectionnez une exécution ayant échoué pour obtenir plus d’informations.

   ![État d’exécution de l’application logique](./media/connectors-create-api-crmonline/run-history.png)

1. Développez une étape ayant échoué afin de voir plus de détails.

   ![Développer l’étape qui a échoué](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Passez en revue les détails de l’étape, tels que les entrées et les sorties, qui peuvent vous aider à déterminer la cause de l’échec.

   ![Étape ayant échoué : entrées et sorties](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

Pour plus d’informations sur la résolution des problèmes relatifs aux applications logiques, consultez [Diagnostic des échecs d’applications logiques](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-reference"></a>Référence de connecteur

Pour plus d’informations techniques, notamment sur les déclencheurs, les actions et les limites, comme décrit dans le fichier OpenAPI (anciennement Swagger) du connecteur, consultez la [page de référence du connecteur](/connectors/dynamicscrmonline/).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les autres [connecteurs d’applications logiques](../connectors/apis-list.md)

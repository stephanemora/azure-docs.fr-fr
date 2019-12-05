---
title: Suivre les messages B2B avec les journaux d’activité Azure Monitor
description: Suivre la communication B2B pour des comptes d’intégration et pour Azure Logic Apps avec Azure Log Analytics
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 3726b0c8c22614d2acc797295543e69f9358d69c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792935"
---
# <a name="track-b2b-messages-with-azure-monitor-logs"></a>Suivre les messages B2B avec les journaux d’activité Azure Monitor

Après avoir configuré la communication B2B entre les partenaires commerciaux dans votre compte d’intégration, les partenaires peuvent échanger des messages avec les protocoles comme AS2, X12 et EDIFACT. Pour vérifier que ces messages sont traités correctement, vous pouvez effectuer leur suivi avec les [journaux Azure Monitor](../log-analytics/log-analytics-overview.md). Par exemple, vous pouvez utiliser ces fonctionnalités de suivi basées sur le web pour suivre des messages :

* Nombre et état des messages
* État des accusés de réception
* Corrélation entre les messages et les accusés de réception
* Descriptions détaillées des erreurs en cas d’échec
* Fonctionnalités de recherche

> [!NOTE]
> Cette page expliquait auparavant comment effectuer ces tâches avec Microsoft Operations Management Suite (OMS) ; dans la mesure où celui-ci sera [mis hors service en janvier 2019](../azure-monitor/platform/oms-portal-transition.md), ces étapes sont remplacées par Azure Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prérequis

* Une application logique configurée avec une journalisation des diagnostics. Découvrez comment [créer une application logique](quickstart-create-first-logic-app-workflow.md) et comment [configurer la journalisation pour cette application logique](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Un compte d’intégration configuré avec une surveillance et une journalisation. Découvrez comment [créer un compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) et comment [configurer une surveillance et une journalisation pour ce compte](../logic-apps/logic-apps-monitor-b2b-message.md).

* Si ce n’est déjà fait, [publiez les données de diagnostic dans les journaux Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

* Une fois les exigences précédentes remplies, vous avez également besoin d’un espace de travail Log Analytics, que vous utilisez pour le suivi des communications B2B via Log Analytics. Si vous n’avez pas d’espace de travail Log Analytics, découvrez [comment créer un espace de travail Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

## <a name="install-logic-apps-b2b-solution"></a>Installation de la solution Logic Apps B2B

Pour suivre les messages B2B liés à votre application logique avec les journaux Azure Monitor, vous devez ajouter la solution **Logic Apps B2B** aux journaux Azure Monitor. Découvrez-en plus sur l’[ajout de solutions aux journaux Azure Monitor](../azure-monitor/learn/quick-create-workspace.md).

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Tous les services**. Dans la zone de recherche, entrez « Log Analytics », puis sélectionnez **Log Analytics**.

   ![Sélectionner Log Analytics](media/logic-apps-track-b2b-messages-omsportal/find-log-analytics.png)

1. Sous **Log Analytics**, recherchez et sélectionnez votre espace de travail Log Analytics. 

   ![Sélectionner un espace de travail Log Analytics](media/logic-apps-track-b2b-messages-omsportal/select-log-analytics-workspace.png)

1. Sous **Bien démarrer avec Log Analytics** > **Configurer des solutions de monitoring**, choisissez **Afficher les solutions**.

   ![Choisir « Afficher les solutions »](media/logic-apps-track-b2b-messages-omsportal/log-analytics-workspace.png)

1. Sur la page Vue d’ensemble, choisissez **Ajouter** pour ouvrir la liste **Solutions de gestion**. Dans cette liste, sélectionnez **Logic Apps B2B**. 

   ![Sélection de la solution Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/add-b2b-solution.png)

   Si vous ne trouvez pas la solution, choisissez **Charger plus** en bas de la liste jusqu’à ce que la solution s’affiche.

1. Choisissez **Créer**, vérifiez l’espace de travail Log Analytics dans lequel vous souhaitez installer la solution, puis sélectionnez à nouveau **Créer**.   

   ![Sélection de l’option « Créer » pour Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/create-b2b-solution.png)

   Si vous ne souhaitez pas utiliser un espace de travail existant, vous pouvez également créer un espace de travail à ce stade.

1. Lorsque vous avez terminé, revenez à la page **Vue d’ensemble** de votre espace de travail. 

   La solution Logic Apps B2B apparaît maintenant sur la page de vue d’ensemble. 
   Lorsque des messages B2B sont traités, le nombre de messages sur cette page est mis à jour.

<a name="message-status-details"></a>

## <a name="view-b2b-message-information"></a>Affichage des informations sur les messages B2B

Une fois les messages B2B traités, vous pouvez afficher l’état et les détails de ces messages dans la vignette **Logic Apps B2B**.

1. Accédez à votre espace de travail Log Analytics, puis ouvrez la page de vue d’ensemble. Choisissez **Logic Apps B2B**.

   ![Nombre de messages mis à jour](media/logic-apps-track-b2b-messages-omsportal/b2b-overview-tile.png)

   > [!NOTE]
   > Par défaut, la vignette **Logic Apps B2B** affiche des données d’une journée. Pour modifier l’étendue des données en définissant un intervalle différent, sélectionnez le contrôle de l’étendue en haut de la page :
   > 
   > ![Modification de l’intervalle](media/logic-apps-track-b2b-messages-omsportal/change-interval.png)

1. Un fois le tableau de bord de l’état du message affiché, vous pouvez afficher davantage de détails sur un type de message spécifique qui affiche des données d’une journée. Choisissez la vignette **AS2**, **X12** ou **EDIFACT**.

   ![Afficher l’état du message](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

   Une liste de messages s’affiche pour la vignette choisie. 
   Pour en savoir plus sur les propriétés de chaque type de message, consultez les descriptions de propriété de message suivantes :

   * [Propriétés de message AS2](#as2-message-properties)
   * [Propriétés de message X12](#x12-message-properties)
   * [Propriétés de message EDIFACT](#EDIFACT-message-properties)

   Par exemple, voici comment peut se présenter une liste de messages AS2 :

   ![Afficher les messages AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)

3. Pour afficher ou exporter les entrées et sorties de messages spécifiques, sélectionnez ceux-ci, puis choisissez **Télécharger**. Lorsque vous y êtes invité, enregistrez le fichier .zip sur votre ordinateur local, puis extrayez son contenu. 

   Le dossier extrait inclut un dossier pour chaque message sélectionné. 
   Si vous définissez des accusés de réception, le dossier de message comprend également des fichiers avec les détails de l’accusé de réception. 
   Chaque dossier de message comprend au moins les fichiers suivants : 
   
   * fichiers contrôlables de visu avec les détails des charges utiles d’entrée et de sortie ;
   * fichiers encodés avec les entrées et sorties.

   Pour chaque type de message, vous pouvez trouver les formats de noms de dossier et de fichier ici :

   * [Formats de nom de dossier et de fichier AS2](#as2-folder-file-names)
   * [Formats de nom de dossier et de fichier X12](#x12-folder-file-names)
   * [ EDIFACT](#edifact-folder-file-names)

   ![Télécharger les fichiers de message](media/logic-apps-track-b2b-messages-omsportal/download-messages.png)

4. Pour afficher toutes les actions qui ont le même ID d'exécution, dans la page **Recherche dans les journaux**, choisissez un message dans la liste des messages.

   Vous pouvez trier ces actions par colonne, ou rechercher des résultats spécifiques.

   ![Actions avec le même ID d'exécution](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

   * Pour rechercher des résultats avec des requêtes prédéfinies, sélectionnez **Favoris**.

   * Découvrez comment [Générer des requêtes en ajoutant des filtres](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md). 
   Ou apprenez-en davantage sur la manière de [rechercher des données dans les journaux Azure Monitor à l’aide de la fonctionnalité Recherche dans les journaux](../log-analytics/log-analytics-log-searches.md).

   * Pour modifier une requête dans la zone de recherche, mettez à jour la requête avec les colonnes et valeurs que vous souhaitez utiliser comme filtres.

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Formats de noms et descriptions de propriétés pour les messages AS2, X 12 et EDIFACT

Pour chaque type de message, voici les formats de noms et descriptions de propriétés pour les fichiers de message téléchargés.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Description de propriétés des messages AS2

Voici les descriptions de propriété pour chaque message AS2.

| Propriété | Description |
| --- | --- |
| Expéditeur | Partenaire invité spécifié dans **Paramètres de réception**, ou partenaire hôte spécifié dans **Paramètres d’envoi** pour un accord AS2 |
| Destinataire | Partenaire hôte spécifié dans **Paramètres de réception**, ou partenaire invité spécifié dans **Paramètres d’envoi** pour un accord AS2 |
| Application logique | Application logique dans laquelle les actions AS2 sont configurées |
| Statut | État du message AS2 <br>Success = a reçu ou envoyé un message AS2 valide. Aucun MDN n’est configuré. <br>Success = a reçu ou envoyé un message AS2 valide. MDN est configuré et reçu ou envoyé. <br>Failed = a reçu un message AS2 non valide. Aucun MDN n’est configuré. <br>Pending = a reçu ou envoyé un message AS2 valide. Un MDN est configuré et attendu. |
| Ack | État du message MDN <br>Accepted = a reçu ou envoyé un MDN positif. <br>Pending = en attente de réception ou d’envoi d’un MDN. <br>Rejected = a reçu ou envoyé un MDN négatif. <br>Not Required = aucun MDN n’est configuré dans l’accord. |
| Direction | Direction du message AS2 |
| ID de corrélation : | ID qui établit la corrélation de l’ensemble des déclencheurs et actions dans une application logique |
| ID de message | ID de message AS2 extrait des en-têtes de message AS2 |
| Timestamp | Heure à laquelle l’action AS2 a traité le message |
|          |             |

<a name="as2-folder-file-names"></a>

### <a name="as2-name-formats-for-downloaded-message-files"></a>Formats de noms AS2 pour les fichiers de message téléchargés

Voici les formats de noms pour chaque dossier et fichier de message AS2 téléchargé.

| Fichier ou dossier | Format du nom |
| :------------- | :---------- |
| Dossier de message | [expéditeur] \_[récepteur]\_AS2\_[ID de corrélation]\_[ID de message]\_[horodatage] |
| Entrée, sortie et, si configurés, fichiers d’accusé de réception | **Charge utile d’entrée** : [expéditeur]\_[récepteur]\_AS2\_[ID de corrélation]\_input_payload.txt </p>**Charge utile de sortie** : [expéditeur]\_[récepteur]\_AS2\_[ID de corrélation]\_sortie\_payload.txt </p></p>**Entrées** : [expéditeur]\_[récepteur]\_AS2\_[ID de corrélation]\_inputs.txt </p></p>**Sorties** : [expéditeur]\_[récepteur]\_AS2\_[ID de corrélation]\_outputs.txt |
|          |             |

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Description de propriétés des messages X12

Voici les descriptions de propriété pour chaque message X12.

| Propriété | Description |
| --- | --- |
| Expéditeur | Partenaire invité spécifié dans **Paramètres de réception**, ou partenaire hôte spécifié dans **Paramètres d’envoi** pour un accord X12 |
| Receiver | Partenaire hôte spécifié dans **Paramètres de réception**, ou partenaire invité spécifié dans **Paramètres d’envoi** pour un accord X12 |
| Application logique | Application logique dans laquelle les actions X12 sont configurées |
| Statut | État du message X12 <br>Success = a reçu ou envoyé un message X12 valide. Aucun accusé de réception fonctionnel configuré. <br>Success = a reçu ou envoyé un message X12 valide. Accusé de réception fonctionnel configuré et reçu ou envoyé. <br>Failed = a reçu ou envoyé un message X12 non valide. <br>Pending = a reçu ou envoyé un message X12 valide. Accusé de réception fonctionnel configuré et attendu. |
| Ack | État de l’accusé de réception fonctionnel (997) <br>Accepted = a reçu ou envoyé un accusé de réception positif. <br>Rejected = a reçu ou envoyé un accusé de réception négatif. <br>Pending = attendait un accusé de réception fonctionnel mais ne l’a pas reçu. <br>Pending = a généré un accusé de réception fonctionnel mais ne peut pas l’envoyer au partenaire. <br>Not Required = accusé de réception fonctionnel non configuré. |
| Direction | Direction du message X12 |
| ID de corrélation : | ID qui établit la corrélation de l’ensemble des déclencheurs et actions dans une application logique |
| Type de message | Type de message X12 EDI |
| ICN | Numéro de contrôle d’échange pour le message X12 |
| TSCN | Numéro de contrôle de document informatisé pour le message X12 |
| Timestamp | Heure à laquelle l’action X12 a traité le message |
|          |             |

<a name="x12-folder-file-names"></a>

### <a name="x12-name-formats-for-downloaded-message-files"></a>Formats de noms X12 pour les fichiers de message téléchargés

Voici les formats de noms pour chaque dossier et fichier de message X12 téléchargé.

| Fichier ou dossier | Format du nom |
| :------------- | :---------- |
| Dossier de message | [expéditeur] \_[récepteur]\_X12\_[numéro de contrôle d’échange]\_[numéro de contrôle global]\_[numéro de contrôle de document informatisé]\_[horodatage] |
| Entrée, sortie et, si configurés, fichiers d’accusé de réception | **Charge utile d’entrée**: [expéditeur]\_[récepteur]\_X12\_[numéro de contrôle d’échange]\_input_payload.txt </p>**Charge utile de sortie**: [expéditeur]\_[récepteur]\_X12\_[numéro de contrôle d’échange]\_sortie\_payload.txt </p></p>**Entrées**: [expéditeur]\_[récepteur]\_X12\_[numéro de contrôle d’échange]\_inputs.txt </p></p>**Sorties**: [expéditeur]\_[récepteur]\_X12\_[numéro de contrôle d’échange]\_outputs.txt |
|          |             |

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Description de propriétés des messages EDIFACT

Voici les descriptions de propriété pour chaque message EDIFACT.

| Propriété | Description |
| --- | --- |
| Expéditeur | Partenaire invité spécifié dans **Paramètres de réception**, ou partenaire hôte spécifié dans **Paramètres d’envoi** pour un accord EDIFACT |
| Destinataire | Partenaire hôte spécifié dans **Paramètres de réception**, ou partenaire invité spécifié dans **Paramètres d’envoi** pour un accord EDIFACT |
| Application logique | Application logique dans laquelle les actions EDIFACT sont configurées |
| Statut | État du message EDIFACT <br>Success = a reçu ou envoyé un message EDIFACT valide. Aucun accusé de réception fonctionnel configuré. <br>Success = a reçu ou envoyé un message EDIFACT valide. Accusé de réception fonctionnel configuré et reçu ou envoyé. <br>Failed = a reçu ou envoyé un message EDIFACT non valide. <br>Pending = a reçu ou envoyé un message EDIFACT valide. Accusé de réception fonctionnel configuré et attendu. |
| Ack | État de l'accusé de réception fonctionnel (CONTRL) <br>Accepted = a reçu ou envoyé un accusé de réception positif. <br>Rejected = a reçu ou envoyé un accusé de réception négatif. <br>Pending = attendait un accusé de réception fonctionnel mais ne l’a pas reçu. <br>Pending = a généré un accusé de réception fonctionnel mais ne peut pas l’envoyer au partenaire. <br>Not Required = accusé de réception fonctionnel non configuré. |
| Direction | Direction du message EDIFACT |
| ID de corrélation : | ID qui établit la corrélation de l’ensemble des déclencheurs et actions dans une application logique |
| Type de message | Type de message EDIFACT |
| ICN | Numéro de contrôle de l’échange pour le message EDIFACT |
| TSCN | Numéro de contrôle de document informatisé pour le message EDIFACT |
| Timestamp | Heure à laquelle l’action EDIFACT a traité le message |
|          |               |

<a name="edifact-folder-file-names"></a>

### <a name="edifact-name-formats-for-downloaded-message-files"></a>Formats de noms EDIFACT pour les fichiers de message téléchargés

Voici les formats de noms pour chaque dossier et fichier de message EDIFACT téléchargé.

| Fichier ou dossier | Format du nom |
| :------------- | :---------- |
| Dossier de message | [expéditeur] \_[récepteur]\_EDIFACT\_[numéro de contrôle d’échange]\_[numéro de contrôle global]\_[numéro de contrôle de document informatisé]\_[horodatage] |
| Entrée, sortie et, si configurés, fichiers d’accusé de réception | **Charge utile d’entrée**: [expéditeur]\_[récepteur]\_EDIFACT\_[numéro de contrôle d’échange]\_input_payload.txt </p>**Charge utile de sortie**: [expéditeur]\_[récepteur]\_EDIFACT\_[numéro de contrôle d’échange]\_sortie\_payload.txt </p></p>**Entrées**: [expéditeur]\_[récepteur]\_EDIFACT\_[numéro de contrôle d’échange]\_inputs.txt </p></p>**Sorties**: [expéditeur]\_[récepteur]\_EDIFACT\_[numéro de contrôle d’échange]\_outputs.txt |
|          |             |

## <a name="next-steps"></a>Étapes suivantes

* [Rechercher des messages B2B dans les journaux Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md)
* [Schémas de suivi AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schémas de suivi X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schémas de suivi personnalisé](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

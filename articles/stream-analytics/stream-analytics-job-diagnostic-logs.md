---
title: Résoudre les problèmes liés à Azure Stream Analytics à l’aide des journaux de diagnostic
description: Cet article décrit comment analyser les journaux de diagnostic dans Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/19/2019
ms.custom: seodec18
ms.openlocfilehash: cc62a6b9f03bdd6dc8671a6cf96113a2234fc092
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247152"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Résoudre les problèmes liés à Azure Stream Analytics à l’aide des journaux de diagnostic

Il arrive parfois qu’un travail Azure Stream Analytics s’arrête de manière inattendue. Il est important d’être en mesure de résoudre ce type de problème. Les échecs peuvent être liés à un résultat de requête inattendu, à une mauvaise connectivité aux périphériques ou à un arrêt inattendu du service. Les journaux de diagnostic dans Stream Analytics peuvent vous aider à identifier la cause des problèmes lorsqu’ils surviennent et à réduire le délai de récupération.

## <a name="log-types"></a>Types de journaux

Stream Analytics fournit deux types de journaux :

* Les [journaux d'activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (activés en permanence) fournissent des détails sur les opérations effectuées sur les travaux.

* Les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (configurables) offrent des informations plus détaillées sur tous les événements liés à un travail. Les journaux de diagnostic enregistrent les événements entre le moment où le travail est créé et celui où le travail est supprimé. Ils consignent les événements qui se produisent lorsque le travail est mis à jour et lors de son exécution.

> [!NOTE]
> Vous pouvez utiliser des services tels que le stockage Azure, Azure Event Hubs, et les journaux d’Azure Monitor pour analyser les données non conformes. Les frais qui vous seront facturés varient selon le modèle de tarification de chaque service.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Débogage à l’aide des journaux d’activité

Les journaux d’activité sont activés par défaut et proposent des informations détaillées sur les opérations effectuées par votre travail Stream Analytics. Les informations présentes dans les journaux d’activité vous aident à déterminer la cause racine des problèmes ayant un impact sur votre travail. Pour utiliser les journaux d'activité dans Stream Analytics, procédez comme suit :

1. Connectez-vous au portail Azure et sélectionnez **Journal d’activité** sous **Vue d’ensemble**.

   ![Journal d’activité Stream Analytics](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Vous pouvez consulter la liste des opérations effectuées. Toute opération ayant entraîné l’échec de votre travail est accompagnée d'une info-bulle rouge.

3. Cliquez sur une opération pour afficher un résumé la concernant. Les informations qui s'y trouvent sont souvent limitées. Pour plus d’informations sur l’opération, cliquez sur **JSON**.

   ![Résumé des opérations du journal d'activité Stream Analytics](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Faites défiler jusqu'à la section **Propriétés** du JSON, qui fournit des détails sur l’erreur à l'origine de l'échec de l'opération. Dans cet exemple, cet échec était dû à une erreur d'exécution liée à des valeurs de latitude hors limites.

   ![Détails d'erreur JSON](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Vous pouvez effectuer des actions correctives en fonction du message d’erreur dans JSON. Dans cet exemple, il convient d'ajouter des vérifications à la requête pour s'assurer que la valeur de latitude est comprise entre -90 degrés et 90 degrés.

6. Si le message d’erreur dans les journaux d’activité n’est pas utile pour identifier la cause racine, activer les journaux de diagnostic et utiliser des journaux Azure Monitor.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Envoyez des diagnostics dans les journaux d’Azure Monitor

Journaux de diagnostic sous tension et en les envoyant aux journaux d’Azure Monitor sont fortement recommandé. Les journaux de diagnostic sont **désactivés** par défaut. Pour activer les journaux de diagnostic, procédez comme suit :

1.  Connectez-vous au portail Azure et accédez à votre travail Stream Analytics. Sous **Surveillance**, sélectionnez **Journaux de diagnostic**. Ensuite, sélectionnez **Activer les diagnostics**.

    ![Navigation dans le panneau jusqu’aux journaux de diagnostic](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Créez un **nom** dans **Paramètres de diagnostic** et cochez la case située en regard de **Envoyer à Log Analytics**. Ajoutez ou créez ensuite un **espace de travail Log Analytics**. Cochez les cases **Exécution** et **Création** sous **JOURNAL**, ainsi que **AllMetrics** sous **MÉTRIQUE** . Cliquez sur **Enregistrer**.

    ![Paramètres des journaux de diagnostic](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Lorsque votre tâche Stream Analytics démarre, les journaux de diagnostic sont routés vers votre espace de travail Log Analytics. Accédez à l’espace de travail Log Analytics et sélectionnez **Journaux d’activité** sous la section **Général**.

   ![Journaux d’analyse Azure sous la section Général](./media/stream-analytics-job-diagnostic-logs/log-analytics-logs.png)

4. Vous pouvez [écrire votre propre requête](../azure-monitor/log-query/get-started-portal.md) pour rechercher des termes, identifier des tendances, analyser des modèles et fournissent d'autres analyses basées sur vos données. Par exemple, vous pouvez écrire une requête pour filtrer uniquement les journaux de diagnostic contenant le message « Échec de la tâche de diffusion en continu ». Les journaux de diagnostic d’Azure Stream Analytics sont stockés dans la table **AzureDiagnostics**.

   ![Requête de diagnostic et résultats](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-query.png)

5. Après avoir écrit une requête pour rechercher les journaux qui conviennent, enregistrez-la en sélectionnant **Enregistrer** et attribuez-lui un nom ainsi qu'une catégorie. Vous pouvez alors créer une alerte en sélectionnant **Nouvelle règle d’alerte**. Spécifiez ensuite la condition de l’alerte. Sélectionnez **Condition** et entrez la valeur de seuil et la fréquence à laquelle cette recherche de journal personnalisée est évaluée.  

   ![Requête de recherche de journal de diagnostic](./media/stream-analytics-job-diagnostic-logs/search-query.png)

6. Choisissez le groupe d’actions et spécifiez les détails de l’alerte, tels que le nom et la description, avant de pouvoir créer la règle d’alerte. Vous pouvez router les journaux de diagnostic de diverses tâches vers le même espace de travail Log Analytics. Cela vous permet de configurer une seule fois des alertes pour toutes les tâches.  

## <a name="diagnostics-log-categories"></a>Catégories de journaux de diagnostic

Deux catégories de journaux de diagnostic sont actuellement disponibles :

* **Création** : capture les événements de journal liés aux opérations de création de travaux (création du travail, ajout et suppression d’entrées et de sorties, ajout et mise à jour de la requête, démarrage et arrêt du travail).

* **Exécution** : capture les événements qui se produisent pendant l’exécution du travail.
    * Erreurs de connectivité
    * Erreurs de traitement des données, notamment :
        * Événements non conformes à la définition de la requête (types et valeurs de champs ne correspondant pas, champs manquants, etc.)
        * Erreurs d’évaluation d’expression
    * Autres erreurs et événements

## <a name="diagnostics-logs-schema"></a>Schéma des journaux de diagnostic

Tous les journaux sont stockés au format JSON. Chaque entrée comprend les champs de chaîne courants suivants :

Nom | Description
------- | -------
time | L’horodatage (heure UTC) du journal.
ResourceId | L’ID de la ressource sur laquelle l’opération a eu lieu, en majuscules. Comprend l’ID d’abonnement, le groupe de ressources et le nom du travail. Par exemple, **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | La catégorie de journal, **Exécution** ou **Création**.
operationName | Le nom de l’opération qui est journalisée. Par exemple, **Événements d’envoi : Échec d’écriture de la sortie SQL sur mysqloutput**.
status | État de l’opération. Par exemple, **Échec** ou **Réussite**.
level | Le niveau du journal. Par exemple, **Erreur**, **Avertissement** ou **Informations**.
properties | Détail spécifique de l’entrée du journal, sérialisé comme chaîne JSON. Pour plus d’informations, consultez les sections suivantes de cet article.

### <a name="execution-log-properties-schema"></a>Schéma de propriétés des journaux d’exécution

Les journaux d’exécution contiennent des informations sur les événements qui se sont produits pendant l’exécution du travail Stream Analytics. Le schéma de propriétés varie selon le type d’événement. Les types de journaux d’exécution suivants sont actuellement disponibles :

### <a name="data-errors"></a>Erreurs de données

Toute erreur qui se produit lorsque le travail traite des données est consignée dans cette catégorie de journaux. La plupart du temps, ces journaux sont créés au cours des opérations de lecture, de sérialisation et d’écriture des données. Ces journaux n’incluent pas les erreurs de connectivité. Les erreurs de connectivité sont traitées comme des événements génériques.

Nom | Description
------- | -------
Source | Nom de l’entrée ou de la sortie du travail où l’erreur s’est produite.
Message | Message associé à l’erreur.
Type | Le type d’erreur. Par exemple, **DataConversionError**, **CsvParserError** ou **ServiceBusPropertyColumnMissingError**.
Données | Contient des données utiles pour localiser avec précision la source de l’erreur. Troncation possible en fonction de la taille.

En fonction de la valeur **operationName**, les erreurs de données ont le schéma suivant :
* **Événements de sérialisation** : les événements de sérialisation se produisent pendant les opérations de lecture d’événements, lorsque les données en entrée ne répondent pas aux conditions du schéma de requête pour l’une des raisons suivantes :
    * *Incompatibilité de type pendant la (dé)sérialisation de l’événement :* identifie le champ à l’origine de l’erreur.
    * *Impossible de lire un événement, sérialisation non valide* : fournit des informations sur l’emplacement où l’erreur s’est produite dans les données d’entrée. (nom d’objet blob pour une entrée d’objet blob, décalage et exemple de données).
* **Événements d’envoi** : les événements d’envoi se produisent pendant les opérations d’écriture. Ils identifient l’événement de streaming à l’origine de l’erreur.

### <a name="generic-events"></a>Événements génériques

Les événements génériques couvrent tout le reste.

Nom | Description
-------- | --------
Error | (facultatif) Informations sur l’erreur, en général des informations sur l’exception si celles-ci sont disponibles.
Message| Message de journal.
Type | Type de message, correspond à la catégorisation interne des erreurs. Par exemple, **JobValidationError** ou **BlobOutputAdapterInitializationFailure**.
ID de corrélation : | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) qui identifie de façon unique l’exécution du travail. Toutes les entrées du journal d’exécution générées depuis le démarrage du travail jusqu’à son arrêt ont le même **ID de corrélation**.

## <a name="next-steps"></a>Étapes suivantes

* [Présentation de Stream Analytics](stream-analytics-introduction.md)
* [Prise en main de Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Stream Analytics](stream-analytics-scale-jobs.md)
* [Informations de référence sur le langage de requête Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion de Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

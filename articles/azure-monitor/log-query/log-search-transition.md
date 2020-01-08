---
title: Affichage et analyse de données dans Azure Log Analytics | Microsoft Docs
description: Assistance pour les utilisateurs qui passent de la recherche dans les journaux Log Analytics aux journaux Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2019
ms.openlocfilehash: a8d0469d57901f53d8e615f800b5d804944163fa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75398157"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Passer de la recherche dans les journaux d’activité Log Analytics aux journaux d’activité Azure Monitor
La recherche dans les journaux d’activité Log Analytics vient d’être remplacée par une nouvelle expérience d’analyse des journaux d’activité Azure Monitor. Actuellement, la page de recherche dans les journaux d’activité est toujours accessible par le biais de l’élément de menu **Journaux d’activité (classiques)** de la page **Espaces de travail Log Analytics** dans le portail Azure, mais elle sera supprimée le 15 février 2019. Cet article décrit les différences entre les deux expériences pour vous aider à remplacer la recherche dans les journaux. 

## <a name="filter-results-of-a-query"></a>Filtrer les résultats d’une requête
Avec la recherche dans les journaux, une liste de filtres apparaît à mesure que les résultats de la recherche sont fournis. Sélectionnez un filtre et cliquez sur **Appliquer** pour exécuter la requête avec le filtre sélectionné.

![Filtre avec la recherche dans les journaux](media/log-search-transition/filter-log-search.png)

Dans les journaux d’activité Azure Monitor, sélectionnez *Filtre (préversion)* pour afficher des filtres. Cliquez sur l’icône de filtre pour afficher les filtres d’ajout. Sélectionnez un filtre et cliquez sur **Appliquer et exécuter** pour exécuter la requête avec le filtre sélectionné.

![Filtre avec les journaux d’activité](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Extraire des champs personnalisés 
Avec la recherche dans les journaux, vous extrayez des [champs personnalisés](../platform/custom-fields.md) à partir de la vue Liste, où le menu d’un champ inclut l’action _Extraire des champs de Table_.

![Extraction de champs avec la recherche dans les journaux](media/log-search-transition/extract-fields-log-search.png)

Avec les journaux d’activité Azure Monitor, vous extrayez des champs personnalisés à partir de la vue Table. Développez un enregistrement en cliquant sur la flèche à sa gauche, puis cliquez sur le bouton de sélection pour accéder à l’action _Extraire des champs_.

![Extraction de champs avec les journaux d’activité](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Fonctions et groupes d’ordinateurs
Pour enregistrer une recherche avec la recherche dans les journaux, sélectionnez **Recherches enregistrées** et **Ajouter** pour indiquer un nom, une catégorie et le texte de la requête. Créez un [groupe d’ordinateurs](../platform/computer-groups.md) en ajoutant un alias de fonction.

![Enregistrer la recherche dans les journaux](media/log-search-transition/save-search-log-search.png)

Pour enregistrer la requête actuelle dans les journaux d’activité Azure Monitor, sélectionnez **Enregistrer**. Remplacez **Enregistrer sous** par _Fonction_ et indiquez un **alias de fonction** pour créer une [fonction](functions.md). Sélectionnez _Enregistrer cette requête comme groupe d’ordinateurs_ pour utiliser l’alias de fonction d’un [groupe d’ordinateurs](../platform/computer-groups.md).

![Enregistrer une requête de journal](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Requêtes enregistrées
Avec la recherche dans les journaux, vos requêtes enregistrées sont disponibles par le biais de l’élément de barre d’action **Recherches enregistrées**. Dans les journaux d’activité Azure Monitor, accédez aux requêtes enregistrées à partir de l’[Explorateur de requêtes](../log-query/get-started-portal.md#save-queries).

![Explorateur de requêtes](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Examen approfondi de lignes résumées
Dans Recherche dans les journaux, vous pouvez cliquer sur une ligne d’une requête résumée pour lancer une autre requête qui dresse la liste des enregistrements détaillés dans cette ligne.

![Examen approfondi dans Recherche dans les journaux](media/log-search-transition/drilldown-search.png)

Dans les journaux d’activité Azure Monitor, vous devez modifier la requête pour retourner ces enregistrements. Développez l’une des lignes des résultats et cliquez sur **+** en regard de la valeur pour l’ajouter à la requête. Ensuite, mettez la commande **summarize** en commentaire, puis relancez la requête.

![Examen approfondi des journaux d’activité Azure Monitor](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Effectuer une action
Avec la recherche dans les journaux, vous pouvez [démarrer un runbook](take-action.md) à partir d’un résultat de recherche en sélectionnant **Effectuer une action**.

![Effectuer une action](media/log-search-transition/take-action-log-search.png)

Dans les journaux d’activité Azure Monitor, [créez une alerte à partir de la requête de journal d’activité](../platform/alerts-log.md). Configurez un groupe d’actions avec une ou plusieurs actions à exécuter en réponse à l’alerte.

![Groupe d’actions](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez-en plus sur la nouvelle [expérience des journaux d’activité Azure Monitor](get-started-portal.md).

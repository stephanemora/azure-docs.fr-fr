---
title: Fonctionnalités de repérage dans Azure Sentinel | Microsoft Docs
description: Utilisez les requêtes de chasse intégrées à Azure Sentinel pour vous aider à poser les bonnes questions afin de détecter les problèmes dans vos données.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/14/2021
ms.author: yelevin
ms.openlocfilehash: 78662bf6dbc6d4be4f0ea0890993530f772d2114
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563053"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>Repérer les menaces avec Azure Sentinel

> [!IMPORTANT]
>
> L’expérience de requête inter-ressource et les mises à niveau du **tableau de bord de chasse** (voir les éléments marqués ci-dessous) sont actuellement en **PRÉVERSION**. Consultez l’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) pour connaître les conditions juridiques supplémentaires s’appliquant aux fonctionnalités Azure sont en version bêta, en préversion ou non encore en disponibilité générale.
>

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

En tant qu’analystes et investigateurs de sécurité, vous souhaitez être proactif dans la recherche des menaces de sécurité, mais vos différents systèmes et appliances de sécurité génèrent de grandes quantités de données qui peuvent être difficiles à analyser et à filtrer pour obtenir des événements explicites. Azure Sentinel dispose d’outils de repérage puissants dédiés à la recherche et la création de requêtes pour repérer les menaces de sécurité dans les sources de données de votre organisation. Afin de faciliter la tâche des analystes de la sécurité dans la recherche proactive des nouvelles anomalies non détectées par vos applications de sécurité ou même par vos règles analytiques planifiées, les requêtes de repérage intégrées d’Azure Sentinel vous aident à poser les bonnes questions pour identifier les problèmes au niveau des données déjà présents dans le réseau. 

Par exemple, l’une des requêtes intégrées fournit des données sur les processus qui s’exécutent le moins souvent sur votre infrastructure. Vous ne souhaitez pas recevoir une alerte chaque fois que ces processus s’exécutent, car ils peuvent se révéler totalement inoffensifs, mais vous pouvez examiner cette requête de temps à autre pour vérifier qu’il ne se produit rien d’inhabituel.

## <a name="use-built-in-queries"></a>Utiliser les requêtes intégrées

Le [tableau de bord de chasse](#use-the-hunting-dashboard-public-preview) fournit des exemples de requêtes prêtes à l’emploi conçus pour vous aider à prendre en main les tables et le langage de requête et à vous familiariser avec eux. Les requêtes s’exécutent sur les données stockées dans les tables de journal, par exemple pour la création de processus, les événements DNS ou d’autres types d’événements.

Les requêtes de chasse intégrées sont élaborées en permanence par les chercheurs en sécurité de Microsoft, qui ajoutent de nouvelles requêtes et affinent les requêtes existantes afin de vous fournir un point d’entrée pour rechercher de nouvelles détections et chasser l’origine des nouvelles attaques.

Utilisez des requêtes avant, pendant et après une compromission pour effectuer les actions suivantes :

- **Avant qu’un incident se produise** : Il ne suffit pas d’attendre les détections. Prenez des mesures proactives en exécutant au moins une fois par semaine toutes les requêtes de chasse aux menaces liées aux données que vous ingérez dans votre espace de travail.

    Les résultats de votre chasse proactive fournissent un aperçu précoce des événements qui peuvent confirmer qu’une compromission est en cours, ou au moins montrer les zones plus vulnérables de votre environnement qui sont à risque et nécessitent une attention particulière.

- **Pendant une compromission** : utilisez [livestream](livestream.md) pour exécuter une requête spécifique en permanence, en présentant les résultats au fur et à mesure qu’ils arrivent. Utilisez livestream lorsque vous devez surveiller activement les événements des utilisateurs, par exemple si vous devez vérifier si une compromission spécifique est toujours en cours, pour aider à déterminer la prochaine action d’un intervenant de la menace et, vers la fin d’une enquête, pour confirmer que la compromission est effectivement terminée.

- **Après une compromission** : Après une compromission ou un incident, veillez à améliorer votre couverture et vos connaissances afin de prévenir des incidents similaires à l’avenir.

    - Modifiez vos requêtes existantes ou créez-en de nouvelles pour faciliter la détection précoce, en vous basant sur les informations que vous avez recueillies lors de la compromission ou de l’incident.

    - Si vous avez découvert ou créé une requête de chasse qui fournit des informations de grande valeur sur les attaques possibles, créez des règles de détection personnalisées basées sur cette requête et communiquer ces informations sous forme d’alertes aux gestionnaires des incidents de sécurité.

        Affichez les résultats de la requête, puis sélectionnez **Nouvelle règle d’alerte** > **Créer une alerte Azure Sentinel**. Utilisez l’**Assistant de règle d’analyse** pour créer une nouvelle règle basée sur votre requête. Pour plus d’informations, consultez [Créer des règles analytiques personnalisées pour détecter des menaces](detect-threats-custom.md).


> [!TIP]
> - Désormais, dans la préversion publique, vous pouvez également créer des requêtes de chasse et de livestream sur les données stockées dans Azure Data Explorer. Pour plus d’informations, consultez les détails de [construction de requêtes inter-ressources](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md) dans la documentation d’Azure Monitor.
>
> - Utilisez les ressources de la communauté, comme le [référentiel GitHub d’Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Hunting%20Queries), pour trouver des requêtes et des sources de données supplémentaires.
>

## <a name="use-the-hunting-dashboard-public-preview"></a>Utiliser le tableau de bord de chasse (préversion publique)

Le tableau de bord de chasse vous permet d’exécuter toutes vos requêtes, ou un sous-ensemble sélectionné, en une seule sélection. Dans le portail Azure Sentinel, sélectionnez **Chasse**.

Le tableau affiché répertorie toutes les requêtes rédigées par l’équipe d’analystes de la sécurité Microsoft et toute requête supplémentaire que vous avez créée ou modifiée. Chaque requête décrit l’objet du repérage, ainsi que le type de données sur lequel ce processus s’exécute. Ces modèles sont regroupés par tactique : les icônes situées sur la droite classent le type de menace (par exemple, accès initial, persistance et exfiltration).

:::image type="content" source="media/hunting/hunting-start.png" alt-text="Début du repérage par Azure Sentinel" lightbox="media/hunting/hunting-start.png":::

Utilisez le tableau de bord de chasse pour identifier où commencer la chasse, en examinant le nombre de résultats, les pics ou l’évolution du nombre de résultats sur une période de 24 heures. Triez et filtrez par favoris, source de données, tactique ou technique MITRE ATT&CK, résultats ou delta de résultat. Affichez les requêtes qui ont encore besoin de sources de données connectées** et recevez des recommandations sur la façon d’activer ces requêtes.

Le tableau suivant décrit les actions détaillées disponibles dans le tableau de bord de chasse :

|Action  |Description  |
|---------|---------|
|**Voir la façon dont les requêtes s’appliquent à votre environnement**     |   Sélectionnez le bouton **Exécuter toutes les requêtes (préversion)** ou sélectionnez un sous-ensemble de requêtes à l’aide des cases à cocher situées à gauche de chaque ligne, puis sélectionnez le bouton **Exécuter les requêtes sélectionnées (préversion)** . <br><br>L’exécution de vos requêtes peut prendre de quelques secondes à plusieurs minutes, en fonction du nombre de requêtes sélectionnées, de l’intervalle de temps et de la quantité de données interrogées.      |
|**Afficher les requêtes qui ont renvoyé des résultats**    |      Une fois l’exécution de vos requêtes terminée, affichez les requêtes qui ont renvoyé des résultats à l’aide du filtre **Résultats** : <br>- Triez pour voir quelles requêtes ont le plus ou le moins de résultats. <br>- Affichez les requêtes qui ne sont pas du tout actives dans votre environnement en sélectionnant *s.o.* dans le filtre **Résultats**. <br>- Passez la souris sur l’icône d’information (**i**) à côté de *s.o.* pour voir quelles sources de données sont nécessaires pour activer cette requête.  |
|**Identifier les pics de vos données**     |   Identifiez les pics dans les données en triant ou en filtrant sur **Delta des résultats**. <br><br>Cela compare les résultats des dernières 24 heures à ceux des 24 à 48 heures précédentes, mettant en évidence les grandes différences de volume.     |
|**Afficher les requêtes associées à la tactique MITRE ATT&CK**     | La **barre tactique MITRE ATT&CK**, en haut du tableau, indique le nombre de requêtes associées à chaque tactique MITRE ATT&CK. La barre tactique est mise à jour dynamiquement en fonction de l’ensemble actuel de filtres appliqués. <br><br>Cela vous permet de voir quelles tactiques MITRE ATT&CK s’affichent lorsque vous filtrez selon un nombre de résultats donné, un delta de résultat élevé, des résultats *s.o.* ou tout autre ensemble de filtres.        |
|**Afficher les requêtes associées aux techniques MITRE ATT&CK**     | Les requêtes peuvent également être associées aux techniques MITRE ATT&CK. Vous pouvez filtrer ou trier par techniques MITRE ATT&CK à l’aide du filtre **Technique**. En ouvrant une requête, vous pourrez sélectionner la technique pour afficher la description MITRE ATT&CK de cette technique.        |
|**Enregistrer une requête dans vos favoris**     |   Les requêtes enregistrées dans vos favoris s’exécutent automatiquement chaque fois que vous accédez à la page **Repérage**. Vous pouvez créer votre propre requête de repérage ou cloner et personnaliser un modèle de requête de repérage existant.      |
|**Exécuter des requêtes**     |   Sélectionnez **Exécuter la requête** dans la page de détails de la requête de chasse pour exécuter la requête directement à partir de la page de chasse. Le nombre de correspondances s’affiche dans la colonne **Résultats** du tableau. Passez en revue la liste des requêtes de repérage et leurs correspondances.     |
|**Passer en revue une requête sous-jacente**     | Effectuez un examen rapide de la requête sous-jacente dans le volet d’informations de la requête. Vous pouvez voir les résultats en cliquant sur le lien **Afficher les résultats de la requête** (sous la fenêtre de requête) ou sur le bouton **Afficher les résultats** (en bas du volet). La requête s’ouvre dans le panneau **Journaux** (Log Analytics) et sous la requête, vous pouvez examiner les correspondances pour la requête.         |
|     |         |


## <a name="create-your-own-bookmarks"></a>Créez vos propres signets

Au cours du processus de chasse et d’enquête, vous pouvez tomber sur des résultats de requête qui vous semblent inhabituels ou suspects. Ajoutez un signet à ces éléments pour vous y référer ultérieurement, par exemple lorsque vous créez ou enrichissez un incident à des fins d’investigation.

- Dans vos résultats, cochez les cases des lignes que vous souhaitez conserver, puis sélectionnez **Ajouter un signet**. Cela crée un enregistrement pour chaque ligne marquée (signet) qui contient les résultats de la ligne, la requête qui a créé les résultats et les mappages d’entités pour extraire les utilisateurs, les hôtes et les adresses IP. Vous pouvez ajouter vos propres étiquettes et des notes à chaque signet.

- Affichez tous les résultats avec un signet en cliquant sur l’onglet **Signets** dans la page **Chasse** principale. Ajoutez des étiquettes aux signets pour les classer à des fins de filtrage. Par exemple, si vous examinez une campagne d’attaque, vous pouvez créer une étiquette pour cette dernière, appliquer l’étiquette aux signets pertinents, puis filtrer tous les signets en fonction de la campagne.

- Examinez un seul résultat avec signet en sélectionnant le signet, puis en cliquant sur **Examiner** dans le volet d’informations pour ouvrir l’expérience d’investigation.

    Vous pouvez également créer un incident à partir d’un ou plusieurs signets ou ajouter un ou plusieurs signets à un incident existant. Cochez la case située à gauche des signets que vous souhaitez utiliser, puis sélectionnez **Actions relatives aux incidents** > **Créer un nouvel incident** ou **Ajouter à un incident existant**. Triez et examinez l’incident comme pour tout autre incident.


> [!TIP]
> Les signets représentent des événements clés qui sont dignes d’intérêt et qui doivent être transformés en incidents s’ils sont suffisamment graves pour justifier une enquête. Les événements tels que les causes racines potentielles, les indicateurs de compromission ou d’autres événements notables doivent être signalés comme des signets.
>

Pour plus d’informations, consultez [Utiliser des signets dans la chasse](bookmarks.md).

## <a name="use-notebooks-to-power-investigations"></a>Utiliser des notebooks pour dynamiser l’investigation

Les notebooks vous offrent un sorte d’environnement de bac à sable virtuel, doté de son propre noyau. Vous pouvez utiliser des notebooks pour améliorer votre repérage et vos investigations avec le Machine Learning, la visualisation et l’analyse des données. Vous pouvez effectuer une investigation complète dans un notebook, en encapsulant les données brutes, le code que vous exécutez sur celles-ci, les résultats et leurs visualisations, et enregistrer tout cela afin de le partager avec d’autres personnes de votre organisation et de le réutiliser.

Pour plus d’informations, consultez [Utiliser Jupyter Notebook pour faire la chasse aux menaces de sécurité](notebooks.md).


## <a name="useful-operators-and-functions"></a>Opérateurs et fonctions utiles

Les requêtes de chasse sont créées dans le [langage de requête Kusto (KQL)](/azure/data-explorer/kusto/query/), un langage de requête puissant avec IntelliSense qui vous offre la puissance et la flexibilité dont vous avez besoin pour faire passer la chasse au niveau supérieur.

Il s’agit du même langage que celui utilisé par les requêtes dans vos règles analytiques et ailleurs dans Azure Sentinel. Pour plus d’informations, consultez [Informations de référence sur le langage de requête](../azure-monitor/logs/get-started-queries.md).

Les opérateurs suivants sont particulièrement utiles dans les requêtes de chasse Azure Sentinel :

- **where** : filtre une table en fonction du sous-ensemble de lignes correspondant à un prédicat.

- **summarize** : génère une table qui agrège le contenu de la table d’entrée.

- **join** : fusionne les lignes de deux tables pour former une nouvelle table en mettant en correspondance les valeurs des colonnes spécifiées de chaque table.

- **count** : renvoie le nombre d’enregistrements indiqué dans le jeu d’enregistrements d’entrée.

- **top** : renvoie les N premiers enregistrements triés d’après les colonnes spécifiées.

- **limit** : renvoie au maximum le nombre de lignes spécifié.

- **project** : sélectionne les colonnes à inclure, renommer ou supprimer, puis insère les nouvelles colonnes calculées.

- **extend** : crée des colonnes calculées et les ajoute au jeu de résultats.

- **makeset** : renvoie un tableau (JSON) dynamique de l’ensemble de valeurs distinctes prises par Expr dans le groupe.

- **find** : recherche les lignes qui correspondent à un prédicat dans un ensemble de tables.

- **adx() (préversion)**  : effectue des requêtes inter-ressources dans les sources de données Azure Data Explorer à partir de l’expérience de chasse Azure Sentinel et de Log Analytics. Pour plus d’informations, consultez [Requête inter-ressources Azure Data Explorer à l’aide d’Azure Monitor](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md).

## <a name="save-a-query"></a>Enregistrement d’une requête

Créez ou modifiez une requête et sauvegardez-la comme votre propre requête ou partagez-la avec des utilisateurs situés dans le même locataire.

:::image type="content" source="./media/hunting/save-query.png" alt-text="Enregistrer la requête" lightbox="./media/hunting/save-query.png":::

**Pour créer une nouvelle requête** :

1. Sélectionnez **Nouvelle requête**.

1. Renseignez tous les champs vides, puis sélectionnez **Créer**.

    :::image type="content" source="./media/hunting/new-query.png" alt-text="Nouvelle requête" lightbox="./media/hunting/new-query.png":::

**Pour cloner et modifier une requête existante** :

1. Dans la table, sélectionnez la requête à modifier.

1. Sélectionnez les points de suspension (...) sur la ligne de la requête que vous souhaitez modifier, puis sélectionnez **Cloner la requête**.

    :::image type="content" source="./media/hunting/clone-query.png" alt-text="Cloner la requête" lightbox="./media/hunting/clone-query.png":::

1. Modifiez la requête, puis sélectionnez **Créer**.

    :::image type="content" source="./media/hunting/custom-query.png" alt-text="Requête personnalisée" lightbox="./media/hunting/custom-query.png":::



## <a name="sample-query"></a>Exemple de requête

Une requête classique commence par un nom de table suivi d’une série d’opérateurs séparés par \|.

Dans l’exemple ci-dessus, démarrez par le nom de table SecurityEvent et ajoutez les éléments enchaînés à l’aide d’une barre verticale selon vos besoins.

1. Définissez un filtre de temps pour consulter uniquement les enregistrements des sept jours qui précèdent.

1. Ajoutez un filtre à la requête pour qu’elle présente uniquement l’ID d’événement 4688.

1. Ajoutez un filtre à la requête sur l’élément CommandLine pour qu’il contienne uniquement les instances de cscript.exe.

1. Projetez uniquement les colonnes que vous souhaitez explorer et limitez les résultats à 1000, puis sélectionnez **Exécuter la requête**.

1. Sélectionnez le triangle vert et exécutez la requête. Vous pouvez tester la requête et l’exécuter pour rechercher un comportement anormal.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer un examen de repérage à l’aide d’Azure Sentinel. 

Pour plus d'informations, consultez les pages suivantes :

- [Use notebooks to run automated hunting campaigns](notebooks.md) (Utiliser des notebooks pour exécuter des campagnes de repérage automatisées)
- [Keep track of data during hunting](bookmarks.md) (Suivre des données pendant la recherche)

Découvrez un exemple d’utilisation de règles d’analyse personnalisées lors de la [supervision de Zoom](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) avec un [connecteur personnalisé](create-custom-connector.md).


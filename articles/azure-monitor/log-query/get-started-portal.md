---
title: Prise en main d'Azure Monitor Log Analytics | Microsoft Docs
description: Cet article fournit un didacticiel dédié à l’écriture de requêtes dans le Portail Azure à l’aide de Log Analytics.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 07/19/2019
ms.openlocfilehash: 1cf1695db50e6aee2a5dae24ed5231fdda7c12de
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670234"
---
# <a name="get-started-with-log-analytics-in-azure-monitor"></a>Prise en main de Log Analytics dans Azure Monitor

> [!NOTE]
> Vous pouvez effectuer cet exercice dans votre propre environnement si vous collectez des données à partir d’au moins une machine virtuelle. Si ce n’est pas le cas, utilisez notre [environnement de démonstration](https://portal.loganalytics.io/demo), qui comporte de nombreux exemples de données.

Dans ce tutoriel, vous allez apprendre à utiliser Log Analytics dans le portail Azure pour écrire des requêtes de journal Azure Monitor. Au terme du tutoriel, vous saurez :

- Utiliser Log Analytics pour écrire une requête simple
- Comprendre le schéma de vos données
- Filtrer, trier et regrouper les résultats
- Appliquer un intervalle de temps
- Créer des graphiques
- Enregistrer et charger des requêtes
- Exporter et partager des requêtes

Pour un tutoriel sur l'écriture de requêtes de journal, consultez [Bien démarrer avec les requêtes de journal dans Azure Monitor](get-started-queries.md).<br>
Pour plus d’informations sur les requêtes de journal, consultez [Vue d’ensemble des requêtes de journal dans Azure Monitor](log-query-overview.md).

## <a name="meet-log-analytics"></a>Présentation de Log Analytics
Log Analytics est un outil web utilisé pour écrire et exécuter des requêtes de journal Azure Monitor. Pour l’ouvrir, sélectionnez **Journaux d’activité** dans le menu Azure Monitor. Une nouvelle requête vide s’affiche.

![page d'accueil](media/get-started-portal/homepage.png)

## <a name="firewall-requirements"></a>Configuration requise du pare-feu
Pour utiliser Log Analytics, votre navigateur doit pouvoir accéder aux adresses suivantes. Si votre navigateur accède au portail Azure par le biais d’un pare-feu, vous devez activer l’accès à ces adresses.

| Uri | IP | Ports |
|:---|:---|:---|
| portal.loganalytics.io | Dynamique | 80,443 |
| api.loganalytics.io | Dynamique | 80,443 |
| docs.loganalytics.io | Dynamique | 80,443 |

## <a name="basic-queries"></a>Requêtes de base
Les requêtes peuvent être utilisées pour rechercher des termes, identifier des tendances, analyser des modèles et fournissent de nombreux autres insights basés sur vos données. Démarrez avec une requête de base :

```Kusto
Event | search "error"
```

Cette requête recherche dans la table _Event_ les enregistrements qui contiennent le terme _error_ dans n’importe quelle propriété.

Les requêtes peuvent commencer par un nom de table ou une commande [search](/azure/kusto/query/searchoperator). L’exemple ci-dessus commence par le nom de table _Event_, qui récupère tous les enregistrements à partir de la table Event. Le caractère barre verticale (|) sépare les commandes. Ainsi, la sortie de la première commande sert d'entrée à la commande suivante. Vous pouvez ajouter n’importe quel nombre de commandes à une seule requête.

Une autre façon d’écrire cette même requête serait :

```Kusto
search in (Event) "error"
```

Dans cet exemple, l’étendue de la commande **search** est la table _Event_, et le terme _error_ est recherché dans tous les enregistrements de cette table.

## <a name="running-a-query"></a>Exécution d’une requête
Exécutez une requête en cliquant sur le bouton **Exécuter** ou en appuyant sur **Maj+Entrée**. Prenez en compte les détails suivants qui déterminent le code exécuté et les données retournées :

- Sauts de ligne : un simple saut facilite la lecture de votre requête. Plusieurs sauts de ligne la scindent en requêtes distinctes.
- Curseur : placez votre curseur à l’intérieur de la requête pour l’exécuter. La première ligne vide trouvée marque la fin de la requête actuelle.
- Intervalle de temps : un intervalle de temps couvrant les _dernières 24 heures_ est défini par défaut. Pour utiliser un autre intervalle, utilisez le sélecteur d’heure ou ajoutez un filtre d’intervalle de temps explicite à votre requête.


## <a name="understand-the-schema"></a>Comprendre le schéma
Le schéma est une collection de tables regroupées visuellement sous une catégorie logique. Plusieurs catégories proviennent de solutions de supervision. La catégorie _LogManagement_ contient des données courantes telles que les événements Windows et Syslog, les données de performances et les pulsations d'agent.

![schéma](media/get-started-portal/schema.png)

Dans chaque table, les données sont organisées en colonnes avec différents types de données, comme l’indiquent les icônes en regard du nom de colonne. Par exemple, la table _Event_ illustrée dans la capture d’écran comporte des colonnes telles que _Computer_, qui contient du texte, _EventCategory_, qui contient des nombres, et _TimeGenerated_, qui contient des dates/heures.

## <a name="filter-the-results"></a>Filtrer les résultats
Commencez par récupérer tout le contenu de la table _Event_.

```Kusto
Event
```

Log Analytics définit automatiquement l'étendue des résultats par :

- Intervalle de temps :  par défaut, les requêtes sont limitées aux dernières 24 heures.
- Nombre de résultats : les résultats sont limités à un maximum de 10 000 enregistrements.

Cette requête étant très générale, elle retourne trop de résultats pour être utile. Vous pouvez filtrer les résultats par le biais des éléments de la table ou en ajoutant explicitement un filtre à la requête. Le filtrage des résultats par le biais des éléments de la table s’applique au jeu de résultats existant, tandis qu’un filtre appliqué à la requête elle-même retourne un nouveau jeu de résultats filtré et peut donc produire des résultats plus précis.

### <a name="add-a-filter-to-the-query"></a>Ajouter un filtre à la requête
Il existe une flèche à gauche de chaque enregistrement. Cliquez sur cette flèche pour ouvrir les détails de l’enregistrement correspondant.

Placez le curseur au-dessus d’un nom de colonne afin qu’apparaissent les icônes « + » et « - ». Pour ajouter un filtre qui ne retourne que les enregistrements ayant la même valeur, cliquez sur le signe « + ». Cliquez sur « - » pour exclure les enregistrements ayant cette valeur, puis cliquez sur **Exécuter** pour réexécuter la requête.

![Ajouter un filtre à une requête](media/get-started-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>Filtrer par le biais des éléments de la table
Maintenant concentrons-nous sur les événements ayant pour gravité _Error_. Cette information est spécifiée dans une colonne nommée _EventLevelName_. Vous aurez besoin de faire défiler l’affichage vers la droite pour voir cette colonne.

Cliquez sur l’icône Filtre à côté du titre de colonne, puis dans la fenêtre indépendante, sélectionnez les valeurs qui _commencent par_ le texte _error_ :

![Filtrer](media/get-started-portal/filter.png)


## <a name="sort-and-group-results"></a>Trier et regrouper les résultats
Les résultats sont désormais limités de manière à inclure uniquement les événements d’erreur issus de SQL Server, créés dans les dernières 24 heures. Toutefois, les résultats ne sont pas triés. Pour trier les résultats en fonction d’une colonne spécifique, telle que _timestamp_, cliquez sur le titre de la colonne. Un premier clic trie par ordre croissant, tandis qu’un deuxième clic trie par ordre décroissant.

![Trier la colonne](media/get-started-portal/sort-column.png)

Une autre façon d’organiser les résultats consiste à utiliser des groupes. Pour regrouper les résultats selon une colonne spécifique, faites simplement glisser l’en-tête de la colonne au-dessus des autres colonnes. Pour créer des sous-groupes, faites glisser d’autres colonnes vers la barre supérieure.

![Groupes](media/get-started-portal/groups.png)

## <a name="select-columns-to-display"></a>Sélectionner les colonnes à afficher
La table de résultats inclut souvent un grand nombre de colonnes. Peut-être constaterez-vous que certaines colonnes retournées ne sont pas affichées par défaut ou souhaiterez-vous supprimer certaines colonnes qui sont affichées. Pour sélectionner les colonnes à afficher, cliquez sur le bouton Colonnes :

![Select columns](media/get-started-portal/select-columns.png)


## <a name="select-a-time-range"></a>Sélectionner un intervalle de temps
Par défaut, Log Analytics applique l’intervalle de temps correspondant aux _dernières 24 heures_. Pour utiliser un autre intervalle, sélectionnez une autre valeur par le biais du sélecteur d’heure, puis cliquez sur **Exécuter**. Outre les valeurs prédéfinies, vous pouvez utiliser l’option _intervalle de temps personnalisé_ pour sélectionner un intervalle absolu pour votre requête.

![Sélecteur d’heure](media/get-started-portal/time-picker.png)

Quand vous sélectionnez un intervalle de temps personnalisé, les valeurs sélectionnées sont exprimées en UTC et peuvent donc être différentes de celles de votre fuseau horaire local.

Si la requête contient explicitement un filtre pour _TimeGenerated_, le titre du sélecteur d’heure indique _Défini dans la requête_. La sélection manuelle est désactivée pour éviter un conflit.


## <a name="charts"></a>Graphiques
Les résultats des requêtes peuvent être présentés sous la forme d’une table, mais également sous forme graphique. Utilisez la requête suivante en guise d’exemple :

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Par défaut, les résultats sont affichés dans une table. Cliquez sur _Graphique_ pour afficher les résultats sous forme graphique :

![Graphique à barres](media/get-started-portal/bar-chart.png)

Les résultats sont affichés dans un graphique à barres empilées. Cliquez sur _Histogramme empilé_ et sélectionnez _Secteurs_ pour obtenir un autre affichage des résultats :

![Graphique à secteurs](media/get-started-portal/pie-chart.png)

Vous pouvez changer manuellement différentes propriétés de l’affichage, telles que les axes x et y, ou les préférences de regroupement et de fractionnement, à partir de la barre de contrôle.

Vous pouvez également définir l’affichage par défaut dans la requête elle-même, à l’aide de l’opérateur render.

### <a name="smart-diagnostics"></a>Smart Diagnostics
Sur un graphique temporel, s’il existe un pic ou une chute brutal dans vos données, un point peut être mis en évidence sur la ligne. Cela indique que _Smart Diagnostics_ a identifié une combinaison de propriétés qui filtrent le changement soudain. Cliquez sur le point pour obtenir plus de détails sur le filtre, et pour afficher la version filtrée. Cela peut vous aider à identifier l’origine de la modification :

![Smart Diagnostics](media/get-started-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>Épingler au tableau de bord
Pour épingler un diagramme ou une table à l’un de vos tableaux de bord Azure partagés, cliquez sur l’icône en forme d’épingle. Notez que cette icône a été déplacée vers le haut de la fenêtre de Log Analytics, différente de la capture d’écran ci-dessous.

![Épingler au tableau de bord](media/get-started-portal/pin-dashboard.png)

Certaines simplifications sont appliquées à un graphique quand vous l’épinglez à un tableau de bord :

- Colonnes et lignes de table : si vous souhaitez épingler une table au tableau de bord, celle-ci doit avoir au plus quatre colonnes. Seules les sept premières lignes sont affichées.
- Restriction de temps : les requêtes sont automatiquement limitées aux 14 derniers jours.
- Restriction de nombre d’emplacements : si vous utilisez un graphique comportant un grand nombre d’emplacements discrets, les emplacements les moins remplis sont automatiquement regroupés en un seul emplacement _autres_.

## <a name="save-queries"></a>Enregistrer des requêtes
Une fois que vous avez créé une requête utile, vous pouvez l’enregistrer ou la partager avec d’autres utilisateurs. L’icône **Enregistrer** se trouve sur la barre supérieure.

Vous pouvez enregistrer la page de l’intégralité de la requête ou une requête unique en tant que fonction. Les fonctions sont des requêtes qui peuvent également être référencées par d’autres requêtes. Pour enregistrer une requête en tant que fonction, vous devez fournir un alias de fonction, qui est le nom utilisé pour appeler cette requête quand elle est référencée par d’autres requêtes.

![Enregistrer la fonction](media/get-started-portal/save-function.png)

>[!NOTE]
>Les caractères suivants sont pris en charge : `a–z, A–Z, 0-9, -, _, ., <space>, (, ), |` dans le champ **Nom** lors de l’enregistrement ou de la modification de la requête enregistrée.

Les requêtes Log Analytics sont toujours enregistrées dans un espace de travail sélectionné et partagées avec les autres utilisateurs de cet espace de travail.

## <a name="load-queries"></a>Charger des requêtes
L’icône Explorateur de requêtes se trouve dans la zone supérieure droite. Il répertorie toutes les requêtes enregistrées par catégorie. Il vous permet également de marquer des requêtes spécifiques en tant que Favoris pour pouvoir les retrouver rapidement. Double-cliquez sur une requête enregistrée pour l’ajouter à la fenêtre active.

![Explorateur de requêtes](media/get-started-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>Exporter et partager en tant que lien
Log Analytics prend en charge plusieurs méthodes d'exportation :

- Excel : enregistrer les résultats dans un fichier CSV.
- Power BI : exporter les résultats dans Power BI. Pour plus d'informations, consultez [Importation de données de journal Azure Monitor dans Power BI](../../azure-monitor/platform/powerbi.md).
- Partager un lien : vous pouvez partager la requête elle-même sous la forme d’un lien, puis envoyer celui-ci à d’autres utilisateurs ayant accès à l’espace de travail afin qu’ils puissent exécuter la requête.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez-en plus sur la [rédaction des requêtes de journal Azure Monitor](get-started-queries.md).

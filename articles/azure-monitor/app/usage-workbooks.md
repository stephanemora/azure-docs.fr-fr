---
title: Créer des rapports interactifs avec les classeurs Azure Monitor | Microsoft Docs
description: Créer des rapports complexes en toute simplicité grâce à des classeurs paramétrables prédéfinis et personnalisés
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 15543f7f761c707e8eff8e0cc0a0e4532475ddf8
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670999"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Créer des rapports interactifs avec les classeurs Azure Monitor

Les classeurs regroupent du texte, [des requêtes Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics), des métriques Azure et des paramètres sous la forme de rapports interactifs riches en contenu. Les classeurs sont modifiables par tous les membres de l’équipe ayant accès aux mêmes ressources Azure.

Les classeurs sont utiles pour :

* Explorer l’utilisation de votre application lorsque vous ne connaissez pas les mesures d’intérêt à l’avance : nombre d’utilisateurs, taux de rétention, taux de conversion, etc. Contrairement à d’autres outils d’analyse de l’utilisation, les classeurs vous permettent de combiner plusieurs types de visualisations et d’analyses, ce qui les rend très utiles pour ce type d’exploration sous forme libre.
* Expliquer à votre équipe le fonctionnement d’une toute nouvelle fonctionnalité, en montrant le nombre d’interactions clés et d’autres mesures de l’utilisateur.
* Partager les résultats d’une expérimentation A/B dans votre application avec d’autres membres de votre équipe. Vous pouvez expliquer les objectifs de l’expérimentation avec le texte, puis montrer chaque mesure d’utilisation et requête Analytics utilisée pour évaluer l’expérimentation, en vous aidant de légendes claires indiquant si chaque mesure se situe au-dessus ou en dessous de la cible.
* Créer des rapports relatifs à l’impact d’une panne sur l’utilisation de votre application, en combinant des données, une explication du texte et une présentation des étapes suivantes pour éviter à l’avenir d’éventuelles interruptions.

## <a name="starting-with-a-template-or-saved-workbook"></a>Commencer avec un classeur enregistré ou un modèle

Un classeur est composé de sections comprenant des graphiques, des tableaux, du texte et des commandes de saisie modifiables de manière indépendante. Pour mieux comprendre comment fonctionne un classeur, nous allons en ouvrir un. 

Sélectionnez **Classeurs** dans le menu de gauche de l’écran Application Insights pour votre application.

![Capture d’écran de navigation jusqu’aux classeurs](./media/usage-workbooks/001-workbooks.png)

Une galerie de classeurs s’affiche avec plusieurs modèles de classeur prédéfinis que vous pouvez utiliser.

![Capture d’écran de la galerie de classeurs](./media/usage-workbooks/002-workbook-gallery.png)

Nous allons commencer par le **modèle par défaut** qui se trouve sous le titre **Démarrage rapide**.

![Capture d’écran de la galerie de classeurs](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Modifier, réorganiser, cloner et supprimer des sections de classeur

Les classeurs présentent deux modes : le **mode d’édition** et le **mode lecture**. Lorsque vous le sélectionnez pour la première fois, le classeur par défaut s’ouvre en **mode d’édition**. Tout le contenu du classeur s’affiche, y compris les étapes et les paramètres qui sont généralement masqués. Le **mode lecture** présente une vue de rapport simplifiée. Vous pouvez ainsi masquer les éléments complexes de création du rapport, tout en accédant en quelques clics aux paramètres et mécanismes sous-jacents si vous avez besoin de les modifier.

![Commandes d’édition de la section Workbooks d’Application Insights](./media/usage-workbooks/editing-controls-new.png)

1. Lorsque vous avez fini de modifier une section, cliquez sur **Modifications terminées** dans l’angle inférieur gauche de la section.

2. Pour créer un doublon de section, cliquez sur l’icône **Cloner cette section**. La création de sections en double est une bonne façon d’itérer sur une requête sans perdre les itérations précédentes.

3. Pour déplacer une section vers un classeur, cliquez sur l’icône **Déplacer vers le haut** ou **Déplacer vers le bas**.

4. Pour supprimer une section de façon permanente, cliquez sur l’icône **Supprimer**.

## <a name="adding-text-and-markdown-sections"></a>Ajout de texte et de sections Markdown

Le fait d’ajouter des en-têtes, des explications et des commentaires à vos classeurs permet de donner un aspect narratif à un ensemble de tables et de graphiques. Les sections de texte des classeurs prennent en charge la [syntaxe Markdown](https://daringfireball.net/projects/markdown/) pour la mise en forme du texte (en-têtes, gras, italique et listes à puces, par exemple).

Pour ajouter une section de textes à votre classeur, utilisez le bouton **Ajouter du texte** situé en bas du classeur, ou de chaque section.

## <a name="adding-query-sections"></a>Ajout de sections de requête

![Section de requête d’un classeur](./media/usage-workbooks/analytics-section-new.png)

Pour ajouter une section de requête à votre classeur, utilisez le bouton **Ajouter une requête** situé en bas du classeur ou en bas de chaque section.

Les sections de requête sont extrêmement flexibles et peuvent être utilisées pour répondre à des questions telles que :

* Combien d’exceptions votre site a levé au cours de la même période en tant que refus d’utilisation ?
* Quelle était la répartition des temps de chargement de page pour les utilisateurs affichant une page ?
* Combien d’utilisateurs ont affiché certaines pages de votre site en particulier ? Il peut être utile de comprendre si des groupes d’utilisateurs utilisent différents sous-ensembles de fonctionnalités de votre site (utilisez l’opérateur `join` avec le modificateur `kind=leftanti` dans le [langage de requête Kusto](/azure/kusto/query/)).

Vous pouvez également formuler des requêtes concernant d’autres applications que celle à partir de laquelle vous avez lancé le classeur. Vous pouvez lancer des requêtes sur plusieurs applications surveillées via Application Insights, ainsi que sur des espaces de travail Log Analytics, à condition de disposer d’une autorisation d’accès à ces ressources.

Pour lancer des requêtes à partir d’autres ressources Application Insights externes, utilisez l’identificateur **app**.

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Cette requête associe des demandes formulées à partir de trois applications différentes. Une application nommée app01, une application nommée app02 et les demandes formulées à partir de la ressource Application Insights locale.

Pour intégrer des données à partir d’un espace de travail Log Analytics externe, utilisez l’identificateur **workspace**.

Pour en savoir plus sur les requêtes couvrant plusieurs ressources, reportez-vous aux [instructions officielles](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

### <a name="advanced-analytic-query-settings"></a>Paramètres de requête analytique avancés

Chaque section a ses propres paramètres avancés, accessibles via l’icône de paramètres ![Commandes d’édition de la section Workbooks d’Application Insights](./media/usage-workbooks/005-settings.png) située à droite du bouton **Ajouter des paramètres**.

![Commandes d’édition de la section Workbooks d’Application Insights](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Largeur personnalisée**    | Définissez cette option pour personnaliser la taille d’un élément et faire tenir un grand nombre d’éléments sur une seule ligne. Vous pouvez ainsi mieux organiser vos tableaux et graphiques pour créer des rapports interactifs pertinents.  |
   | **Visible sous conditions** | Utilisez cette option pour masquer les étapes en fonction d’une valeur de paramètre en mode lecture. |
   | **Exporter un paramètre**| Cela vous permet de sélectionner une ligne dans le tableau ou le graphique pour faire en sorte que des valeurs soient modifiées ou deviennent visibles.  |
   | **Afficher la requête en mode lecture** | Cette option affiche la requête au-dessus du graphique ou du tableau même en mode lecture.
   | **Afficher le bouton Ouvrir dans Analytics en mode lecture** | Cela ajoute l’icône bleue Analytics à droite du graphique pour vous permettre d’y accéder en un seul clic.|

La plupart de ces paramètres sont relativement intuitifs, mais pour comprendre comment fonctionne l’option **Exporter un paramètre**, il est préférable d’examiner un classeur qui utilise cette fonctionnalité.

L’un des classeurs prédéfinis fournit des informations sur les utilisateurs actifs.

La première section du classeur est basée sur les données de requête Analytics :

![Commandes d’édition de la section Workbooks d’Application Insights](./media/usage-workbooks/003-active-users.png)

La deuxième section est également basée sur les données de requête analytique, mais la sélection d’une ligne dans le premier tableau met à jour de façon interactive le contenu du graphique :

![Commandes d’édition de la section Workbooks d’Application Insights](./media/usage-workbooks/004-active-users-trend.png)

 Cela est possible grâce à l’utilisation des paramètres avancés **Quand un élément est sélectionné, exporter un paramètre** qui sont activés dans la requête Analytics du tableau.

![Commandes d’édition de la section Workbooks d’Application Insights](./media/usage-workbooks/007-settings-export.png)

La deuxième requête analytique utilise alors les valeurs exportées lorsqu’une ligne est sélectionnée. Si aucune ligne n’est sélectionnée, la ligne représentant les valeurs globales est utilisée par défaut. 

```
let start = startofday(ago({TimeRange} + {Metric}));
union customEvents, pageViews
| where timestamp >= start
| where name in ({Activities}) or '*' in ({Activities}) or ('%' in ({Activities}) and itemType == 'pageView') or ('#' in ({Activities}) and itemType == 'customEvent')
{OtherFilters}
| where '{Filter}' == '' or '{Filter}' == '🔸 Overall' or {AnalyzeBy} == replace('🔹 ', '', '{Filter}')
| evaluate activity_engagement(user_Id, timestamp, start, now(), 1d, {Metric})
| where timestamp >= startofday(ago({TimeRange}))
| project timestamp, ["Active User"] = dcount_activities_outer
| render timechart 
```

## <a name="adding-metrics-sections"></a>Ajout de sections de métriques

Les sections de métriques vous offrent un accès complet pour intégrer des données de métriques Azure Monitor dans vos rapports interactifs. La plupart des classeurs prédéfinis contiennent à la fois des données de requête analytique et des données de métriques, ce qui vous permet de combiner les avantages des deux fonctionnalités. Vous avez également la possibilité d’extraire des données de métriques à partir des ressources de l’ensemble des abonnements auxquels vous avez accès.

Voici un exemple de données de machine virtuelle insérées dans un classeur en vue d’afficher les performances du processeur sous forme de tableau :

![Commandes d’édition de la section Workbooks d’Application Insights](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Ajout de sections de paramètres

Les paramètres de classeur vous permettent de modifier les valeurs dans le classeur sans avoir à modifier manuellement les sections de requête ou de texte.  Davantage de personnes peuvent ainsi utiliser ce type de rapports basés sur des classeurs, car il n’est pas nécessaire de comprendre le langage de requête analytique sous-jacent.

Pour remplacer la valeur d’un paramètre dans les sections de requête, de texte ou de paramètre, le nom du paramètre doit être saisi entre accolades, par exemple ``{parameterName}``.  Les noms de paramètre doivent respecter des règles similaires à celles des identificateurs JavaScript, à savoir des caractères alphabétiques ou des traits de soulignement, suivis par des caractères alphanumériques ou des traits de soulignement. Par exemple, **a1** est autorisé, mais pas **1a**.

Les paramètres sont linéaires et appliqués aux différentes étapes du classeur, en partant du haut.  Des paramètres déclarés ultérieurement dans un classeur peuvent remplacer ceux déclarés plus haut.  Cela permet également aux paramètres qui utilisent des requêtes d’accéder aux valeurs de paramètres définis plus haut.  Au sein d’une étape de paramètre, les paramètres sont également linéaires et appliqués de gauche à droite, les paramètres de droite pouvant dépendre d’un paramètre déclaré antérieurement au sein de cette même étape.
 
Quatre types de paramètres sont actuellement pris en charge :

  |         |          |
   | ---------------- |:-----|
   | **Text**    | L’utilisateur modifie une zone de texte, et vous pouvez éventuellement fournir une requête pour renseigner la valeur par défaut. |
   | **Liste déroulante** | L’utilisateur sélectionne un élément à partir d’un ensemble de valeurs. |
   | **Sélecteur d’intervalle de temps**| L’utilisateur choisit parmi un ensemble prédéfini de valeurs d’intervalle de temps, ou sélectionne un élément à partir d’un intervalle de temps personnalisé.|
   | **Sélecteur de ressource** | L’utilisateur choisit parmi les ressources sélectionnées pour le classeur.|

### <a name="using-a-text-parameter"></a>Utilisation d’un paramètre de texte

La valeur qu’un utilisateur saisit dans la zone de texte est directement remplacée dans la requête, sans espace ni guillemets. Si la valeur dont vous avez besoin est une chaîne, le paramètre doit être saisi entre guillemets dans la requête (par exemple **‘{paramètre}’** ).

Ainsi, la valeur saisie dans une zone de texte peut être utilisée n’importe où. Cela peut être un nom de tableau, un nom de colonne, un nom de fonction, un opérateur, etc.

Le type de paramètre de texte dispose d’un paramètre **Obtenir la valeur par défaut à partir d’une requête analytique** qui permet au créateur du classeur d’utiliser une requête pour renseigner la valeur par défaut de cette zone de texte.

Lorsque vous utilisez la valeur par défaut obtenue à partir d’une requête analytique, seule la première valeur de la première ligne (ligne 0, colonne 0) est utilisée comme valeur par défaut. Par conséquent, il est recommandé de limiter votre requête afin qu’elle renvoie une seule ligne et une seule colonne. Toutes les autres données retournées par la requête sont ignorées. 

La valeur retournée par la requête est remplacée directement sans espace ni guillemets. Si la requête ne retourne aucune ligne, le résultat du paramètre est une chaîne vide (si le paramètre n’est pas obligatoire) ou une chaîne non définie (si le paramètre est obligatoire).

### <a name="using-a-dropdown"></a>Utilisation d’une liste déroulante

Le type de paramètre liste déroulante vous permet de créer une commande de liste déroulante pour pouvoir sélectionner une ou plusieurs valeurs.

La liste déroulante est remplie par une requête analytique. Si la requête retourne une colonne, les valeurs de cette colonne correspondent à la fois à la **valeur** et au **libellé** dans la commande de liste déroulante. Si la requête retourne deux colonnes, la première colonne correspond à la **valeur**, et la deuxième colonne au **libellé** dans la liste déroulante.  Si la requête retourne trois colonnes, la troisième colonne est utilisée pour indiquer la sélection par défaut dans cette liste déroulante.  Cette colonne peut être de n’importe quel type, mais le plus simple est d’utiliser des valeurs booléennes ou numériques, où 0 correspond à false et 1 à true.

 Si la colonne est de type chaîne, la chaîne null/vide est considérée comme fausse (false), et toute autre valeur est considérée comme vraie (true). Pour les listes déroulantes à sélection unique, la première valeur avec une valeur true est utilisée comme sélection par défaut.  Pour les listes déroulantes à sélection multiple, toutes les valeurs ayant une valeur true forment le jeu de valeurs sélectionné par défaut. Les éléments de la liste déroulante s’affichent dans l’ordre où la requête a retourné les lignes. 

Examinons les paramètres présents dans le rapport sur les utilisateurs actifs. Cliquez sur le symbole de modification en regard de **TimeRange**.

![Commandes d’édition de la section Workbooks d’Application Insights](./media/usage-workbooks/009-time-range.png)

Cette action lance l’élément de menu Modifier le paramètre :

![Commandes d’édition de la section Workbooks d’Application Insights](./media/usage-workbooks/010-time-range-edit.png)

La requête utilise une fonctionnalité du langage de requête analytique, appelée **datatable**, qui vous permet de créer un tableau arbitraire riche en contenu à partir de rien. Par exemple, la requête analytique suivante :

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Génère le résultat :

![Commandes d’édition de la section Workbooks d’Application Insights](./media/usage-workbooks/011-data-table.png)

Vous pouvez également utiliser une liste déroulante pour sélectionner un nom de pays/région dans un ensemble de pays/régions proposés :

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

La requête affiche les résultats comme suit :

![Liste déroulante des pays](./media/usage-workbooks/012-country-dropdown.png)

Les listes déroulantes sont des outils incroyablement efficaces pour créer et personnaliser des rapports interactifs.

### <a name="time-range-parameters"></a>Paramètres d’intervalle de temps

Bien que vous puissiez définir votre propre paramètre d’intervalle de temps personnalisé via le type de paramètre liste déroulante, vous pouvez également utiliser le type de paramètre intervalle de temps prêt à l’emploi si vous n’avez pas besoin du même niveau de flexibilité. 

Les types de paramètre intervalle de temps ont 15 plages par défaut qui vont de cinq minutes aux 90 derniers jours. Il est également possible de sélectionner un intervalle de temps personnalisé, ce qui permet au créateur du rapport de sélectionner des valeurs de début et de fin spécifiques pour l’intervalle de temps.

### <a name="resource-picker"></a>Sélecteur de ressource

Le type de paramètre de sélecteur de ressource vous donne la possibilité de limiter ou d’étendre votre rapport à certains types de ressources. Le classeur **Informations sur les échecs** est un exemple de classeur prédéfini qui utilise le type de sélecteur de ressource.

![Liste déroulante des pays](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Enregistrer et partager des classeurs avec votre équipe

Les classeurs sont enregistrés au sein d’une ressource Application Insights, soit dans la section **Mes rapports** qui est privée ou dans la section **Rapports partagés** accessible à tous les utilisateurs ayant accès à la ressource Application Insights. Pour afficher tous les classeurs de la ressource, cliquez sur le bouton **Ouvrir** dans la barre d’action.

Pour partager un classeur qui se trouve actuellement dans **Mes rapports** :

1. Dans la barre d’action, cliquez sur **Ouvrir**.
2. Cliquez sur le bouton «... » en regard du classeur que vous souhaitez partager.
3. Cliquez sur **Déplacer vers les rapports partagés**.

Pour partager un classeur avec un lien ou par e-mail, cliquez sur **Partager** dans la barre d’action. Gardez à l’esprit que les destinataires du lien ont besoin d’accéder à cette ressource dans le portail Azure pour afficher le classeur. Pour apporter des modifications, les destinataires doivent au moins disposer des autorisations de collaborateur pour la ressource.

Pour épingler un lien à un classeur dans un tableau de bord Azure :

1. Dans la barre d’action, cliquez sur **Ouvrir**.
2. Cliquez sur le bouton «... » en regard du classeur que vous souhaitez épingler.
3. Cliquez sur **Épingler au tableau de bord**.

## <a name="contributing-workbook-templates"></a>Partage de modèles de classeur

Vous avez créé un modèle de classeur intéressant et souhaitez le partager avec la communauté ? Pour en savoir plus, rendez-vous sur notre page dédiée au [dépôt GitHub](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md).

## <a name="next-steps"></a>Étapes suivantes
- Pour activer les expériences d’utilisation, commencez à envoyer des [événements personnalisés](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou des [affichages de page](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Si vous envoyez déjà des événements personnalisés ou des affichages de page, explorez les outils d’utilisation pour savoir comment les utilisateurs emploient votre service.
    - [Utilisateurs, sessions, événements](../../azure-monitor/app/usage-segmentation.md)
    - [Entonnoirs](../../azure-monitor/app/usage-funnels.md)
    - [Rétention](../../azure-monitor/app/usage-retention.md)
    - [Flux d’utilisateurs](../../azure-monitor/app/usage-flows.md)
    - [Ajouter du contexte utilisateur](../../azure-monitor/app/usage-send-user-context.md)

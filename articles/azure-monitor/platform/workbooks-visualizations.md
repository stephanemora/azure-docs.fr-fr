---
title: Visualisations de classeurs Azure Monitor
description: En savoir plus sur les composants de visualisation des classeurs Azure Monitor, notamment le texte, les graphiques, les grilles, les arborescences et les graphes.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d05902f47dff3dd2f8a63ae240c0b8825a5c441f
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658028"
---
# <a name="azure-monitor-workbook-visualizations"></a>Visualisations de classeurs Azure Monitor

Les classeurs Azure Monitor prennent en charge plusieurs styles de visualisation pour répondre à vos besoins en matière de création de rapports. Cet article fournit des exemples de chaque type de visualisation.

## <a name="text"></a>Texte

Les classeurs permettent aux auteurs d’inclure des blocs de texte dans leurs classeurs. Le texte peut être une analyse humaine de télémétrie, des informations pour aider les utilisateurs à interpréter vos données, des en-têtes de section, etc.

![Capture d’écran de la table de texte Apdex](./media/workbooks-visualizations/apdex.png)

Le texte est ajouté à l’aide d’un contrôle Markdown qui fournit un contrôle de mise en forme complet.

![Capture d’écran du Markdown brut qui génère la table rendue](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>Ajouter un contrôle de texte

1. Basculez le classeur en mode d’édition en cliquant sur l’élément **Modifier** de la barre d’outils.
2. Utilisez le lien **Ajouter du texte** pour ajouter un contrôle de texte au classeur.
3. Ajoutez du Markdown au contrôle.
4. Cliquez sur le bouton **Modification terminée** pour afficher le texte mis en forme.

> [!TIP]
> Utilisez cet [aide-mémoire sur Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) pour en savoir plus sur les différentes options de mise en forme.

## <a name="charts"></a>Graphiques

Les classeurs permettent de présenter les données d’analyse sous forme de graphiques. Les types de graphiques pris en charge incluent les courbes, les barres, les catégories, les surfaces, les nuages de points, les secteurs et le temps. Les auteurs peuvent choisir de personnaliser la hauteur, la largeur, la palette de couleurs, la légende, les titres, les messages sans données, etc. du graphique.

Les classeurs prennent en charge les graphiques pour les journaux et les sources de données métriques. 

### <a name="adding-a-log-chart"></a>Ajout d’un graphique de journal

1. Basculez le classeur en mode d’édition en cliquant sur l’élément **Modifier** de la barre d’outils.
2. Utilisez le lien **Ajouter une requête** pour ajouter un contrôle de requête de journal au classeur.
3. Sélectionnez le type de requête **Journal**, le type de ressource (par exemple, Application Insights) et les ressources à cibler.
4. Utiliser l’éditeur de requête pour saisir le [KQL](https://docs.microsoft.com/azure/kusto/query/) pour votre analyse (par exemple, la tendance des requêtes).
5. Définissez la visualisation sur un des éléments suivants : **Zones**, **Barres**, **Bar (catégorie)** , **Courbes**, **Secteurs**, **Nuage de points** ou **Temps**.
6. Définissez d’autres paramètres si nécessaire, comme la plage de temps, la visualisation, la taille, la palette de couleurs et la légende.

![Capture d’écran du graphique de journal en mode édition](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>Paramètres du graphique de journal

| Paramètre | Explication | Exemple |
| ------------- |:-------------|:-------------|
| `Query Type` | Type de requête à utiliser | Journal, Azure Resource Graph, etc. |
| `Resource Type` | Le type de ressource à cibler | Application Insights, Log Analytics ou Azure d’abord |
| `Resources` | Ensemble de ressources à partir duquel obtenir les valeurs des métriques | MyApp1 |
| `Time Range` | Fenêtre de temps pour afficher le graphique du journal | Dernière heure, dernières 24 heures, etc. |
| `Visualization` | Visualisation à utiliser | Zones, Barres, Courbes, Secteurs, Nuages de points, Temps, Catégories |
| `Size` | Taille verticale du contrôle | Petit, moyen, grand ou complet |
| `Color palette` | Palette de couleurs à utiliser dans le graphique. Ignoré en mode multi-métrique ou segmenté. | Bleu, vert, rouge, etc. |
| `Legend` | Fonction d’agrégation à utiliser pour la légende | Somme ou moyenne des valeurs, ou valeur max, min, première, dernière |
| `Query` | Toute requête KQL qui retourne des données au format attendu par la visualisation du graphique | _requests \| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h_ |

### <a name="adding-a-metric-chart"></a>Ajout d’un graphique de métrique

1. Basculez le classeur en mode d’édition en cliquant sur l’élément **Modifier** de la barre d’outils.
2. Utilisez le lien **Ajouter une métrique** pour ajouter un contrôle de métrique au classeur.
3. Sélectionnez un type de ressource (par exemple, compte de stockage), les ressources à cibler, l’espace de noms et le nom de la métrique, et l’agrégation à utiliser.
4. Définissez d’autres paramètres si nécessaire, comme la plage de temps, le fractionnement, la visualisation, la taille et la palette de couleurs.

![Capture d’écran du graphique de métrique en mode édition](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>Paramètres du graphique de métriques

| Paramètre | Explication | Exemple |
| ------------- |:-------------|:-------------|
| `Resource Type` | Le type de ressource à cibler | Stockage ou machine virtuelle. |
| `Resources` | Ensemble de ressources à partir duquel obtenir les valeurs des métriques | MyStorage1 |
| `Namespace` | Espace de noms avec la métrique | Stockage > Blob |
| `Metric` | La métrique à visualiser | Stockage > Blob > Transactions |
| `Aggregation` | Fonction d’agrégation à appliquer à la mesure | Somme, Total, Moyenne, etc. |
| `Time Range` | Fenêtre de temps dans laquelle afficher la métrique | Dernière heure, dernières 24 heures, etc. |
| `Visualization` | Visualisation à utiliser | Zones, Barres, Courbes, Nuages de points et Grille |
| `Split By` | Fractionner éventuellement la métrique sur une dimension | Transactions par type de zone géographique |
| `Size` | Taille verticale du contrôle | Petit, Moyen ou Grand |
| `Color palette` | Palette de couleurs à utiliser dans le graphique. Ignoré si le paramètre `Split by` est utilisé | Bleu, vert, rouge, etc. |

## <a name="grids"></a>Grilles

Les grilles ou les tables sont un moyen courant de présenter des données aux utilisateurs. Les classeurs permettent aux utilisateurs de définir un style individuel pour les colonnes de la grille pour fournir une interface utilisateur riche à leurs rapports.

L’exemple ci-dessous montre une grille qui combine des icônes, des cartes thermiques et des sparklines pour présenter des informations complexes. Le classeur fournit également des opérations de tri, une zone de recherche et un bouton Aller à l’analyse.

![Capture d’écran de la grille basée sur un journal](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>Ajout d’une grille basée sur un journal

1. Basculez le classeur en mode d’édition en cliquant sur l’élément **Modifier** de la barre d’outils.
2. Utilisez le lien **Ajouter une requête** pour ajouter un contrôle de requête de journal au classeur.
3. Sélectionnez le type de requête **Journal**, le type de ressource (par exemple, Application Insights) et les ressources à cibler.
4. Utilisez l’éditeur de requête pour saisir le KQL pour votre analyse (par exemple, les machines virtuelles dont la mémoire est inférieure à un seuil)
5. Définir la visualisation sur **Grille**
6. Définissez d’autres paramètres si nécessaire, comme la plage de temps, la taille, la palette de couleurs et la légende.

![Capture d’écran de la requête de grille basée sur un journal](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>Vignettes

Les vignettes sont un moyen très utile de présenter des données de synthèse dans des classeurs. L’image ci-dessous montre un cas d’usage courant des vignettes : une synthèse au niveau de l’application en plus d’une grille détaillée.

![Capture d’écran de l’affichage en mosaïque du résumé](./media/workbooks-visualizations/tiles-summary.png)

Les vignettes de classeur prennent en charge un titre, un sous-titre, du texte long, des icônes, des dégradés basés sur les métriques, des lignes/barres spark, des pieds de page, etc.

### <a name="adding-a-tile"></a>Ajout d'une vignette

1. Basculez le classeur en mode d’édition en cliquant sur l’élément _Modifier_ de la barre d’outils.
2. Utilisez le lien **Ajouter une requête** pour ajouter un contrôle de requête de journal au classeur. 
3. Sélectionnez le type de requête **Journal**, le type de ressource (par exemple, Application Insights) et les ressources à cibler.
4. Utiliser l’éditeur de requête pour saisir le KQL pour votre analyse
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. Définir la taille sur **Complet**
6. Définir la visualisation sur **Mosaïques**
7. Cliquez sur le bouton **Paramètres de vignette** pour ouvrir le volet Paramètres
8. Dans **Champs de vignette**, définissez :
    * Titre : `name`
    * Gauche : `Requests`, Renderer : `Big Number`, Palette de couleurs : `Green to Red`, Valeur min : `0`
    * Bas : `appName`
9. Cliquez sur le bouton **Enregistrer et fermer** au bas du volet.

![Capture d’écran de l’affichage en mosaïque du résumé](./media/workbooks-visualizations/tile-settings.png)

Voici comment les vignettes s’affichent en mode lecture :

![Capture d’écran de l’affichage en mosaïque du résumé](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Arborescences

Les classeurs prennent en charge les vues hiérarchiques via des grilles d’arborescence. Les arborescences permettent de développer certaines lignes au niveau suivant pour une expérience d’exploration.

L’exemple ci-dessous montre les mesures d’intégrité du conteneur (taille de la plage de travail) visualisées comme une grille arborescente. Les nœuds de niveau supérieur sont ici des nœuds de service Azure Kubernetes (AKS), le niveau suivant étant les pods et le niveau final étant les conteneurs. Notez que vous pouvez toujours mettre en forme vos colonnes comme dans une grille (carte thermique, icônes, lien). Dans ce cas, la source de données sous-jacente est un espace de travail Log Analytics avec les journaux d’AKS.

![Capture d’écran de l’affichage en mosaïque du résumé](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>Ajout d’une grille arborescente
1. Basculez le classeur en mode d’édition en cliquant sur l’élément _Modifier_ de la barre d’outils.
2. Utilisez le lien **Ajouter une requête** pour ajouter un contrôle de requête de journal au classeur. 
3. Sélectionnez le type de requête **Journal**, le type de ressource (par exemple, Application Insights) et les ressources à cibler.
4. Utiliser l’éditeur de requête pour saisir le KQL pour votre analyse
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. Définir la visualisation sur **Grille**
6. Cliquez sur le bouton **Paramètres de colonne** pour ouvrir le volet Paramètres
7. Dans la section **Arborescence/Regrouper par paramètres** en bas, définissez :
    * Type d'arborescence : `Parent/Child`
    * Champ d'ID : `Id`
    * Champ d'ID parent : `ParentId`
    * Afficher le contrôle pour développer sur : `Name`
    * Développer le niveau supérieur de l'arborescence : `checked`
8. Dans la section _Colonnes_ en haut, définissez :
    * _Id_ - Convertisseur de colonne : `Hidden`
    * _ID parent_ - Renderer de colonne : `Hidden`
    * _Requêtes_ - Renderer de colonne : `Bar`, Couleur : `Blue`, Valeur minimale : `0`
9. Cliquez sur le bouton _Enregistrer et fermer_ au bas du volet.    

![Capture d’écran de l’affichage en mosaïque du résumé](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>Paramètres d’arborescence

| Paramètre | Explication |
|:------------- |:-------------|
| `Id Field` | ID unique de chaque ligne dans la grille |
| `Parent Id Field` | ID du parent de la ligne actuelle |
| `Show the expander on` | Colonne sur laquelle afficher l’icône de développement d’arborescence. Il est courant que les grilles d’arborescence masquent leur ID et le champ d’ID parent, car ils ne sont pas très lisibles. Au lieu de cela, l’icône de développement apparaît sur un champ avec une valeur plus lisible, comme le nom de l’entité |
| `Expand the top level of the tree` | Si cette option est activée, la grille de l’arborescence est développée au niveau supérieur. Utile si vous souhaitez afficher plus d’informations par défaut |

## <a name="graphs"></a>Graphes

Les classeurs prennent en charge la visualisation de graphes arbitraires basés sur les données des journaux pour afficher les relations entre les entités d’analyse.

Le graphe ci-dessous montre les données entrantes/sortantes d’un ordinateur via différents ports vers/depuis des ordinateurs externes. Il est coloré par type (ordinateur vs. port vs. adresse IP externe) et les tailles des bords correspondent à la quantité de données circulant entre. Les données sous-jacentes proviennent d’une requête KQL ciblant les connexions de machines virtuelles.

![Capture d’écran de l’affichage en mosaïque du résumé](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>Ajout d'un graphe
1. Basculez le classeur en mode d’édition en cliquant sur l’élément _Modifier_ de la barre d’outils.
2. Utilisez le lien **Ajouter une requête** pour ajouter un contrôle de requête de journal au classeur. 
3. Sélectionnez le type de requête **Journal**, le type de ressource (par exemple, Application Insights) et les ressources à cibler.
4. Utiliser l’éditeur de requête pour saisir le KQL pour votre analyse
    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```
7. Définissez la visualisation sur **Graphe**
8. Cliquez sur le bouton **Paramètres de graphe** pour ouvrir le volet Paramètres
9. Dans _Champs de disposition_ en bas, définissez :
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. Dans _Paramètres de format de nœud_ en haut, définissez :
    * _Contenu en haut_- Utiliser la colonne : `Name`, Renderer de colonne : `Text`
    * _Contenu au centre_- Utiliser la colonne : `Calls`, Renderer de colonne : `Big Number`, Palette de couleurs : `None`
    * _Contenu en bas_- Utiliser la colonne : `Kind`, Renderer de colonne : `Text`
10. Cliquez sur le bouton _Enregistrer et fermer_ au bas du volet.

![Capture d’écran de l’affichage en mosaïque du résumé](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>Étapes suivantes

* [Déployez](workbooks-automate.md) des classeurs avec Azure Resource Manager.
* [Contrôlez](workbooks-access-control.md) et partagez l’accès à vos ressources de classeur.

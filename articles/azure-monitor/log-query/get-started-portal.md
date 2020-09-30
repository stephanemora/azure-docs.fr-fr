---
title: 'Tutoriel : Bien démarrer avec les requêtes Log Analytics'
description: Ce tutoriel vous montre comment écrire et gérer des requêtes de journal Azure Monitor à l’aide de Log Analytics dans le Portail Azure.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: b337065f125d12e309dd1f7fcc56c2af72b1c28c
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90088355"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>Tutoriel : Bien démarrer avec les requêtes Log Analytics

Ce tutoriel vous montre comment utiliser Log Analytics pour écrire, exécuter et gérer des requêtes de journal Azure Monitor dans le Portail Azure. Vous pouvez utiliser des requêtes Log Analytics pour rechercher des termes, identifier des tendances, analyser des modèles et fournir beaucoup d’autres insights issus de vos données. 

Ce tutoriel vous montre comment utiliser Log Analytics pour :

> [!div class="checklist"]
> * Comprendre le schéma des données de journal
> * Écrire et exécuter des requêtes simples, et modifier l’intervalle de temps pour les requêtes
> * Filtrer, trier et regrouper les résultats de requête
> * Afficher, modifier et partager des visuels des résultats de requête
> * Enregistrer, charger, exporter et copier des requêtes et des résultats

Pour plus d’informations sur les requêtes de journal, consultez [Vue d’ensemble des requêtes de journal dans Azure Monitor](log-query-overview.md).<br/>
Pour suivre un tutoriel détaillé sur l’écriture de requêtes de journal, consultez [Bien démarrer avec les requêtes de journal dans Azure Monitor](get-started-queries.md).

## <a name="open-log-analytics"></a>Ouvrir Log Analytics
Pour utiliser Log Analytics, vous devez être connecté à un compte Azure. Si vous ne possédez pas de compte Azure, [créez un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Vous pouvez effectuer la plupart des étapes de ce tutoriel en utilisant [cet environnement de démonstration](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade) qui comprend de nombreux exemples de données. Avec l’environnement de démonstration, vous ne pouvez pas enregistrer les requêtes ni épingler les résultats à un tableau de bord.

Vous avez aussi la possibilité d’utiliser votre propre environnement si Azure Monitor vous sert à collecter des données de journal sur au moins une ressource Azure. Pour ouvrir un espace de travail Log Analytics, dans le volet de navigation gauche d’Azure Monitor, sélectionnez **Journaux**. 

## <a name="understand-the-schema"></a>Comprendre le schéma
 
Un *schéma* est une collection de tables regroupées en catégories logiques. Le schéma de démonstration comporte plusieurs catégories issues des solutions de supervision. Par exemple, la catégorie **LogManagement** contient les événements Windows et Syslog, les données de performances et les pulsations d’agent.

Les tables du schéma sont présentées sous l’onglet **Tables** de l’espace de travail Log Analytics. Les tables contiennent des colonnes ; le type de données de chaque colonne est représenté par une icône affichée à côté du nom de la colonne. Par exemple, la table**Event** contient des colonnes de texte comme **Computer** et des colonnes numériques comme **EventCategory**.

![La capture d’écran montre la page Journaux du portail Azure avec une nouvelle requête, mettant en évidence le volet Tables avec Computer et EventCategory encadrés.](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>Écrire et exécuter des requêtes de base

Log Analytics s’ouvre en affichant une nouvelle requête vide dans l’**Éditeur de requête**.

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>Écrivez votre requête.

Les requêtes de journal Azure Monitor utilisent une version du langage de requête Kusto. Les requêtes peuvent commencer par un nom de table ou une commande [search](/azure/kusto/query/searchoperator). 

La requête suivante récupère tous les enregistrements de la table **Event** :

```Kusto
Event
```

Le caractère barre verticale (|) sépare les commandes. Ainsi, la sortie de la première commande sert d’entrée à la commande suivante. Vous pouvez ajouter n’importe quel nombre de commandes à une seule requête. La requête suivante récupère les enregistrements de la table **Event**, puis recherche le terme **error** dans toutes les propriétés :

```Kusto
Event 
| search "error"
```

L’ajout d’un saut de ligne rend les requêtes plus lisibles. Si plusieurs sauts de ligne sont ajoutés, la requête est divisée en plusieurs requêtes distinctes.

Voici une autre façon d’écrire la même requête :

```Kusto
search in (Event) "error"
```

Dans le deuxième exemple, la commande **search** recherche le terme **error** uniquement dans les enregistrements de la table **Events**.

Par défaut, Log Analytics limite les requêtes à l’intervalle de temps des dernières 24 heures. Pour définir un autre intervalle de temps, ajoutez un filtre **TimeGenerated** explicite à la requête ou utilisez le contrôle **Intervalle de temps**.

### <a name="use-the-time-range-control"></a>Utiliser le contrôle Intervalle de temps
Pour utiliser le contrôle **Intervalle de temps**, sélectionnez-le dans la barre supérieure, puis sélectionnez une valeur dans la liste déroulante, ou l’option **Personnalisé** afin de créer un intervalle de temps personnalisé.

![Sélecteur d’heure](media/get-started-portal/time-picker.png)

- Les valeurs de l’intervalle de temps sont exprimées en temps UTC et peuvent donc être différentes de celles de votre fuseau horaire local.
- Si la requête définit explicitement un filtre **TimeGenerated**, le contrôle sélecteur affiche **Définir dans la requête** et est désactivé pour empêcher tout conflit.

### <a name="run-a-query"></a>Exécution d’une requête
Pour exécuter une requête, placez le curseur dans la requête, puis sélectionnez **Exécuter** dans la barre supérieure ou appuyez sur **Maj**+**Entrée**. La requête s’exécute jusqu’à ce qu’elle arrive à une ligne vide.

## <a name="filter-results"></a>Filtrer les résultats
Log Analytics limite les résultats à 10 000 enregistrements. Une requête générale comme `Event` retourne trop de résultats pour être utile. Vous pouvez filtrer les résultats de la requête en limitant les éléments de table inclus dans la requête ou en ajoutant explicitement un filtre aux résultats. Le filtrage sur les éléments de table retourne un nouveau jeu de résultats, alors qu’un filtre explicite s’applique au jeu de résultats existant.

### <a name="filter-by-restricting-table-elements"></a>Filtrer en limitant les éléments de table
Pour filtrer les résultats de la requête `Event` sur les événements **Error** en limitant les éléments de table inclus dans la requête :

1. Dans les résultats de la requête, sélectionnez la flèche déroulante à côté d’un enregistrement dont la colonne **EventLevelName** contient **Error**. 
   
1. Dans les détails développés, cliquez sur **...** devant **EventLevelName**, puis sélectionnez **Inclure « Error »** . 
   
   ![Ajouter un filtre à une requête](media/get-started-portal/add-filter.png)
   
1. Notez que la requête dans l’**Éditeur de requête** a été remplacée par celle-ci :
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. Sélectionnez **Exécuter** pour exécuter la nouvelle requête.

### <a name="filter-by-explicitly-filtering-results"></a>Filtrer par un filtrage explicite des résultats
Pour filtrer les résultats de la requête `Event` sur les événements **Error** en appliquant un filtre aux résultats :

1. Dans les résultats de la requête, sélectionnez l’icône **Filtrer** à côté de l’en-tête de colonne **EventLevelName**. 
   
1. Dans le premier champ de la fenêtre contextuelle, sélectionnez **Est égal à** et dans le champ en dessous, entrez *error*. 
   
1. Sélectionnez **Filtrer**.
   
   ![La capture d’écran montre une table des résultats avec un menu contextuel permettant de filtrer les résultats sur la valeur EventLevelName.](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>Trier, regrouper et sélectionner des colonnes
Pour trier les résultats d’une requête en fonction d’une colonne spécifique, par exemple **TimeGenerated [UTC]** , sélectionnez l’en-tête de la colonne. Sélectionnez de nouveau cet en-tête pour passer de l’ordre croissant à l’ordre décroissant, ou inversement.

![Trier la colonne](media/get-started-portal/sort-column.png)

Une autre façon d’organiser les résultats consiste à utiliser des groupes. Pour regrouper les résultats d’après une colonne spécifique, faites glisser l’en-tête de la colonne vers la barre au-dessus de la table de résultats qui indique **Faites glisser un en-tête de colonne et placez-le ici pour regrouper en fonction de cette colonne**. Pour créer des sous-groupes, faites glisser d’autres colonnes vers la barre supérieure. Vous pouvez réorganiser la hiérarchie et trier les groupes et sous-groupes dans la barre.

![La capture d’écran montre les résultats de la requête avec des sous-groupes pour EventLevelName et Computer.](media/get-started-portal/groups.png)

Pour masquer ou afficher des colonnes dans les résultats, sélectionnez **Colonnes** au-dessus de la table, puis sélectionnez ou désélectionnez les colonnes de votre choix dans la liste déroulante.

![Select columns](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>Afficher et modifier des graphiques
Vous pouvez également examiner les résultats de la requête sous une forme graphique. Entrez l’exemple de requête suivante :

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Par défaut, les résultats sont affichés dans une table. Sélectionnez **Graphique** au-dessus de la table pour afficher les résultats sous une forme graphique.

![Graphique à barres](media/get-started-portal/bar-chart.png)

Les résultats sont représentés dans un graphique à barres empilées. Sélectionnez d’autres options, comme **Histogramme empilé** ou **Secteurs** pour voir des représentations différentes des résultats.

![Graphique à secteurs](media/get-started-portal/pie-chart.png)

Vous pouvez changer manuellement les propriétés de la vue, telles que les axes x et y ou les préférences de regroupement et de fractionnement, à partir de la barre de contrôle.

Vous pouvez également définir les vues par défaut dans la requête elle-même, à l’aide de l’opérateur [render](/azure/kusto/query/renderoperator).

## <a name="pin-results-to-a-dashboard"></a>Épingler des résultats à un tableau de bord

Pour épingler une table ou un graphique de résultats Log Analytics à un tableau de bord Azure partagé, sélectionnez **Épingler au tableau de bord** dans la barre supérieure. 

![Épingler au tableau de bord](media/get-started-portal/pin-dashboard.png)

Dans le volet **Épingler à un autre tableau de bord**, sélectionnez ou créez un tableau de bord partagé auquel les résultats seront épinglés, puis sélectionnez **Appliquer**. La table ou le graphique s’affiche sur le tableau de bord Azure sélectionné.

![Graphique épinglé au tableau de bord](media/get-started-portal/pin-dashboard2.png)

Une table ou un graphique que vous épinglez à un tableau de bord partagé présente les limitations suivantes : 

- Seules les données des 14 derniers jours sont prises en compte.
- Une table peut afficher un maximum de quatre colonnes et les sept premières lignes.
- Les graphiques qui contiennent beaucoup de catégories différentes regroupent automatiquement les catégories les moins remplies dans une seule et même catégorie **Autres**.

## <a name="save-load-or-export-queries"></a>Enregistrer, charger ou exporter des requêtes

Une fois que vous avez créé une requête, vous pouvez enregistrer ou partager la requête ou des résultats avec d’autres utilisateurs. 

### <a name="save-queries"></a>Enregistrer des requêtes

Pour enregistrer une requête :

1. Sélectionnez **Enregistrer** dans la barre supérieure.
   
1. Dans la boîte de dialogue **Enregistrer**, attribuez à la requête un **Nom**, qui peut contenir les caractères suivants : a–z, A–Z, 0-9, espace, trait d’union, trait de soulignement, point, parenthèse ou barre verticale. 
   
1. Spécifiez si la requête doit être enregistrée en tant que **Requête** ou **Fonction**. Les fonctions sont des requêtes que d’autres requêtes peuvent référencer. 
   
   Pour enregistrer une requête en tant que fonction, entrez un **Alias de fonction**, qui est un nom abrégé utilisé par les autres requêtes pour appeler cette requête.
   
1. Si vous êtes dans un espace de travail Log Analytics, spécifiez une **catégorie** que l’**Explorateur de requêtes** devra utiliser pour la requête. (Les catégories ne sont pas disponibles pour les requêtes Application Insights.)
   
1. Sélectionnez **Enregistrer**.
   
   ![Enregistrer la fonction](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>Charger des requêtes
Pour charger une requête enregistrée, sélectionnez **Explorateur de requêtes** en haut à droite. Le volet de l’**Explorateur de requêtes** s’ouvre et liste toutes les requêtes par catégorie. Développez les catégories ou entrez un nom de requête dans la barre de recherche, puis sélectionnez une requête pour la charger dans l’**Éditeur de requête**. Vous pouvez marquer une requête comme **Favori** en sélectionnant l’étoile à côté de son nom.

![Explorateur de requêtes](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>Exporter et partager des requêtes
Pour exporter une requête, sélectionnez **Exporter** dans la barre supérieure, puis sélectionnez **Exporter au format CSV - Toutes les colonnes**, **Exporter au format CSV - Colonnes affichées** ou **Exporter vers Power BI (requête M)** dans la liste déroulante.

La vidéo suivante montre comment intégrer Log Analytics à Excel.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4Asme]

Pour partager un lien vers une requête, sélectionnez **Copier le lien** dans la barre supérieure, puis sélectionnez **Copier le lien vers la requête**, **Copier le texte de la requête** ou **Copier les résultats de la requête** pour copier dans le Presse-papiers. Vous pouvez envoyer le lien de la requête à d’autres utilisateurs qui ont accès au même espace de travail.

## <a name="next-steps"></a>Étapes suivantes

Passez au tutoriel suivant pour en savoir plus sur l’écriture de requêtes de journal dans Azure Monitor.
> [!div class="nextstepaction"]
> [Écrire des requêtes de journal dans Azure Monitor](get-started-queries.md)

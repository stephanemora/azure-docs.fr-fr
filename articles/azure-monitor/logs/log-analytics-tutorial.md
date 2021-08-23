---
title: Tutoriel Log Analytics
description: Découvrez dans ce tutoriel comment utiliser les fonctionnalités de Log Analytics dans Azure Monitor pour générer et exécuter une requête de journal et analyser ses résultats dans le portail Azure.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 06/28/2021
ms.openlocfilehash: 6a78bcdb6f8f3036da4f273256d681f94f87a1bb
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113356632"
---
# <a name="log-analytics-tutorial"></a>Tutoriel Log Analytics
Log Analytics est un outil du portail Azure permettant de modifier et d’exécuter des requêtes de journal à partir de données collectées par les journaux Azure Monitor et d’analyser leurs résultats de manière interactive. Vous pouvez utiliser des requêtes Log Analytics pour récupérer des enregistrements correspondant à des critères particuliers, identifier des tendances, analyser des modèles et fournir divers insights sur vos données. 

Ce tutoriel vous guide dans l’interface Log Analytics, présente quelques requêtes de base et vous montre comment tirer parti des résultats. Vous allez apprendre les opérations suivantes :

> [!div class="checklist"]
> * Comprendre le schéma des données de journal
> * Écrire et exécuter des requêtes simples, et modifier l’intervalle de temps pour les requêtes
> * Filtrer, trier et regrouper les résultats de requête
> * Afficher, modifier et partager des visuels des résultats de requête
> * Charger, exporter et copier des requêtes et des résultats

> [!IMPORTANT]
> Ce tutoriel utilise des fonctionnalités de Log Analytics pour générer et exécuter une requête au lieu d’utiliser la requête elle-même. Vous tirerez parti des fonctionnalités de Log Analytics pour générer une requête et utiliser un autre exemple de requête. Lorsque vous êtes prêt à apprendre la syntaxe des requêtes et à commencer à modifier directement la requête, parcourez le [tutoriel sur le langage de requête Kusto (KQL)](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor). Ce tutoriel présente plusieurs exemples de requêtes que vous pouvez modifier et exécuter dans Log Analytics, en tirant parti de plusieurs des fonctionnalités que vous allez découvrir dans ce tutoriel.


## <a name="prerequisites"></a>Prérequis
Ce tutoriel utilise l’[environnement de démonstration Log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), qui comprend de nombreux exemples de données prenant en charge les exemples de requêtes. Vous pouvez également utiliser votre propre abonnement Azure, mais vous ne disposerez peut-être pas de données dans les mêmes tables.

## <a name="open-log-analytics"></a>Ouvrir Log Analytics
Ouvrez l’[environnement de démonstration Log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade) ou sélectionnez **Journaux** dans le menu Azure Monitor de votre abonnement. Cela permet de définir un espace de travail Log Analytics comme étendue initiale, ce qui signifie que votre requête sélectionnera parmi toutes les données de cet espace de travail. Si vous sélectionnez **Journaux** dans le menu d’une ressource Azure, l’étendue est définie sur les seuls enregistrements de cette ressource. Pour plus d’informations sur l’étendue, consultez [Étendue de requête de journal](./scope.md).

Vous pouvez voir l’étendue dans l’angle supérieur gauche de l’écran. Si vous utilisez votre propre environnement, vous verrez une option permettant de sélectionner une autre étendue, mais cette option n’est pas disponible dans l’environnement de démonstration.

:::image type="content" source="media/log-analytics-tutorial/scope.png" alt-text="Étendue Log Analytics" lightbox="media/log-analytics-tutorial/scope.png":::

## <a name="table-schema"></a>Schéma de table
Le côté gauche de l’écran comprend l’onglet **Tables**, qui vous permet d’inspecter les tables disponibles dans l’étendue actuelle. Celles-ci sont regroupées par **Solution** par défaut, mais vous pouvez modifier leur regroupement ou les filtrer. 

Développez la solution **Gestion des journaux** et recherchez la table **AppRequests**. Vous pouvez développer la table pour afficher son schéma, ou pointer sur son nom pour afficher des informations supplémentaires à son sujet. 

:::image type="content" source="media/log-analytics-tutorial/table-details.png" alt-text="Affichage de tables" lightbox="media/log-analytics-tutorial/table-details.png":::

Cliquez sur **Liens utiles** pour accéder à la référence des tables qui documente chaque table et ses colonnes. Cliquez sur **Aperçu des données** pour jeter un œil à quelques enregistrements récents dans la table. Cela peut être utile pour s’assurer qu’il s’agit bien des données que vous attendez, avant d’exécuter une requête avec elles.

:::image type="content" source="media/log-analytics-tutorial/sample-data.png" alt-text="Exemples de données" lightbox="media/log-analytics-tutorial/sample-data.png":::

## <a name="write-a-query"></a>Écrivez votre requête.
Commençons par écrire une requête à l’aide de la table **AppRequests**. Double-cliquez sur son nom pour l’ajouter à la fenêtre de requête. Vous pouvez également taper directement dans la fenêtre et même faire en sorte qu’IntelliSense vous aide à compléter les noms des tables dans l’étendue actuelle et les commandes KQL.

Il s’agit de la requête la plus simple que nous pouvons écrire. Elle retourne simplement tous les enregistrements d’une table. Exécutez-la en cliquant sur le bouton **Exécuter** ou en appuyant sur Maj+Entrée avec le curseur positionné n’importe où dans le texte de la requête.

:::image type="content" source="media/log-analytics-tutorial/query-results.png" alt-text="Résultats de la requête" lightbox="media/log-analytics-tutorial/query-results.png":::

Vous pouvez constater que nous avons des résultats. Le nombre d’enregistrements retournés par la requête est affiché dans le coin inférieur droit. 

## <a name="filter"></a>Filtrer

Ajoutons un filtre à la requête pour réduire le nombre d’enregistrements retournés. Sélectionnez l’onglet **Filtrer** dans le volet gauche. Cela présente différentes colonnes dans les résultats de la requête, que vous pouvez utiliser pour filtrer les résultats. Les valeurs les plus élevées de ces colonnes sont affichées avec le nombre d’enregistrements ayant cette valeur. Cliquez sur **200** sous **ResultCode**, puis **Appliquer et exécuter**. 

:::image type="content" source="media/log-analytics-tutorial/query-pane.png" alt-text="Volet Requête" lightbox="media/log-analytics-tutorial/query-pane.png":::

Une instruction **where** est ajoutée à la requête avec la valeur que vous avez sélectionnée. Les résultats incluent désormais uniquement les enregistrements ayant cette valeur, ce qui vous permet de voir que le nombre d’enregistrements est réduit.

:::image type="content" source="media/log-analytics-tutorial/query-results-filter-01.png" alt-text="Résultats de la requête filtrés" lightbox="media/log-analytics-tutorial/query-results-filter-01.png":::


## <a name="time-range"></a>Plage temporelle
Toutes les tables d’un espace de travail Log Analytics ont une colonne nommée **TimeGenerated** qui correspond à l’heure de création de l’enregistrement. Toutes les requêtes ont une plage de temps qui limite les résultats aux enregistrements ayant une valeur **TimeGenerated** comprise dans cette plage. La plage de temps peut être définie dans la requête ou avec le sélecteur en haut de l’écran.

Par défaut, la requête retourne les enregistrements des dernières 24 heures. Vous devriez voir un message ici indiquant que nous ne voyons pas tous les résultats. Cela est dû au fait que Log Analytics peut retourner un maximum de 30 000 enregistrements, et que notre requête en a retourné davantage. Sélectionnez la liste déroulante **Plage de temps** et choisissez **12 heures**. Cliquez à nouveau sur **Exécuter** pour retourner les résultats. 

:::image type="content" source="media/log-analytics-tutorial/query-results-max.png" alt-text="Plage temporelle" lightbox="media/log-analytics-tutorial/query-results-max.png":::


## <a name="multiple-query-conditions"></a>Plusieurs conditions de requête
Nous allons réduire les résultats en ajoutant une autre condition de filtre. Une requête peut inclure un nombre quelconque de filtres pour cibler exactement le jeu d’enregistrements souhaité. Sélectionnez **Accéder à la page d’accueil/d’index** sous **Nom**, puis cliquez sur **Appliquer et exécuter**. 

:::image type="content" source="media/log-analytics-tutorial/query-results-filter-02.png" alt-text="Résultats de la requête avec plusieurs filtres" lightbox="media/log-analytics-tutorial/query-results-filter-02.png":::


## <a name="analyze-results"></a>Analyser les résultats
En plus de vous aider à écrire et à exécuter des requêtes, Log Analytics offre des fonctionnalités permettant d’exploiter les résultats. Commencez par développer un enregistrement pour afficher les valeurs de toutes ses colonnes.

:::image type="content" source="media/log-analytics-tutorial/expand-record.png" alt-text="Développer un enregistrement" lightbox="media/log-analytics-tutorial/expand-record.png":::

Cliquez sur le nom d’une colonne pour trier les résultats en fonction de cette colonne. Cliquez sur l’icône de filtre en regard de celle-ci pour spécifier une condition de filtre. Cela est similaire à l’ajout d’une condition de filtre à la requête elle-même, sauf que ce filtre est effacé si la requête est réexécutée. Appliquez cette méthode si vous souhaitez analyser rapidement un jeu d’enregistrements dans le cadre de l’analyse interactive.

Par exemple, définissez un filtre sur la colonne **DurationMs** pour limiter les enregistrements à ceux qui ont pris plus de **100** millisecondes. 

:::image type="content" source="media/log-analytics-tutorial/query-results-filter.png" alt-text="Filtre des résultats de la requête" lightbox="media/log-analytics-tutorial/query-results-filter.png":::

Au lieu de filtrer les résultats, vous pouvez regrouper les enregistrements par une colonne particulière. Effacez le filtre que vous venez de créer, puis activez le curseur **Regrouper les colonnes**. 

:::image type="content" source="media/log-analytics-tutorial/query-results-group-columns.png" alt-text="Grouper la colonne" lightbox="media/log-analytics-tutorial/query-results-group-columns.png":::

À présent, faites glisser la colonne **Url** sur la ligne de regroupement. Les résultats sont désormais organisés d’après cette colonne, et vous pouvez réduire chaque groupe pour vous aider dans votre analyse.

:::image type="content" source="media/log-analytics-tutorial/query-results-grouped.png" alt-text="Résultats de la requête regroupés" lightbox="media/log-analytics-tutorial/query-results-grouped.png":::

## <a name="work-with-charts"></a>Utiliser des graphiques
Examinons une requête qui utilise des données numériques que nous pouvons afficher dans un graphique. Au lieu de créer une requête, nous allons sélectionner un exemple de requête.

Dans le volet gauche, cliquez sur **Requêtes**. Ce volet comprend des exemples de requêtes que vous pouvez ajouter à la fenêtre de requête. Si vous utilisez votre propre espace de travail, vous devez disposer de diverses requêtes dans plusieurs catégories, mais si vous utilisez l’environnement de démonstration vous ne verrez qu’une seule catégorie **Espaces de travail Log Analytics**. Développez-la pour afficher les requêtes de la catégorie.

Cliquez sur la requête appelée **Taux d’erreur de fonction** dans la catégorie **Applications**. Cette opération ajoute la requête à la fenêtre de requête. Notez que la nouvelle requête est séparée de l’autre par une ligne vide. Une requête en KQL se termine lorsqu’elle rencontre une ligne vide. Elles sont donc considérées comme des requêtes distinctes. 

:::image type="content" source="media/log-analytics-tutorial/example-query.png" alt-text="Nouvelle requête" lightbox="media/log-analytics-tutorial/example-query.png":::

La requête active est celle sur laquelle le curseur est positionné. Vous pouvez voir que la première requête est mise en surbrillance, indiquant qu’il s’agit de la requête active. Cliquez n’importe où dans la nouvelle requête pour la sélectionner, puis cliquez sur le bouton **Exécuter** pour l’exécuter.

:::image type="content" source="media/log-analytics-tutorial/example-query-output-table.png" alt-text="Tableau des résultats de la requête" lightbox="media/log-analytics-tutorial/example-query-output-table.png":::

Pour afficher les résultats dans un graphique, sélectionnez **Graphique** dans le volet des résultats.  Notez qu’il existe différentes options pour manipuler le graphique, par exemple pour le remplacer par un autre type.


:::image type="content" source="media/log-analytics-tutorial/example-query-output-chart.png" alt-text="Graphique des résultats de la requête" lightbox="media/log-analytics-tutorial/example-query-output-chart.png":::


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment utiliser Log Analytics, suivez le tutoriel sur l’utilisation des requêtes de journal.
> [!div class="nextstepaction"]
> [Écrire des requêtes de journal dans Azure Monitor](get-started-queries.md)

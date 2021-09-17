---
title: Tutoriel Log Analytics
description: Découvrez dans ce tutoriel comment utiliser les fonctionnalités de Log Analytics dans Azure Monitor pour générer et exécuter une requête de journal et analyser ses résultats dans le portail Azure.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 06/28/2021
ms.openlocfilehash: bf19056cbde9c719c8d8665d2fc9a954e7a943b6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729339"
---
# <a name="log-analytics-tutorial"></a>Tutoriel Log Analytics
Log Analytics est un outil du portail Azure permettant de modifier et d’exécuter des requêtes de journal à partir de données collectées par les journaux Azure Monitor et d’analyser leurs résultats de manière interactive. Vous pouvez utiliser des requêtes Log Analytics pour récupérer des enregistrements correspondant à des critères particuliers, identifier des tendances, analyser des modèles et fournir divers insights sur vos données. 

Ce tutoriel vous guide dans l’interface Log Analytics, présente quelques requêtes de base et vous montre comment tirer parti des résultats. Vous allez apprendre à effectuer les tâches suivantes :

> [!div class="checklist"]
> * Comprendre le schéma des données de journal
> * Écrire et exécuter des requêtes simples, et modifier l’intervalle de temps pour les requêtes
> * Filtrer, trier et regrouper les résultats de requête
> * Afficher, modifier et partager des visuels des résultats de requête
> * Charger, exporter et copier des requêtes et des résultats

> [!IMPORTANT]
> Dans ce tutoriel, vous tirerez parti des fonctionnalités de Log Analytics pour générer une requête et utiliser un autre exemple de requête. Lorsque vous êtes prêt à apprendre la syntaxe des requêtes et à commencer à modifier directement la requête, lisez le [tutoriel sur le langage de requête Kusto (KQL)](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor). Ce tutoriel vous dirige à travers des exemples de requêtes que vous pouvez modifier et exécuter dans Log Analytics. Il utilise plusieurs des fonctionnalités que vous allez apprendre dans ce tutoriel.


## <a name="prerequisites"></a>Prérequis
Ce tutoriel utilise l’[environnement de démonstration Log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), qui comprend de nombreux exemples de données prenant en charge les exemples de requêtes. Vous pouvez également utiliser votre propre abonnement Azure, mais vous ne disposerez peut-être pas de données dans les mêmes tables.

## <a name="open-log-analytics"></a>Ouvrir Log Analytics
Ouvrez l’[environnement de démonstration Log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade) ou sélectionnez **Journaux** dans le menu Azure Monitor de votre abonnement. Cette étape permet de définir un espace de travail Log Analytics comme étendue initiale, ce qui signifie que votre requête sélectionnera parmi toutes les données de cet espace de travail. Si vous sélectionnez **Journaux** dans le menu d’une ressource Azure, l’étendue est définie sur les seuls enregistrements de cette ressource. Pour plus d’informations sur l’étendue, consultez [Étendue de requête de journal](./scope.md).

Vous pouvez voir l’étendue dans l’angle supérieur gauche de l’écran. Si vous utilisez votre propre environnement, vous verrez une option permettant de sélectionner une autre étendue. Cette option n’est pas disponible dans l’environnement de démonstration.

:::image type="content" source="media/log-analytics-tutorial/scope.png" alt-text="Capture d’écran montrant l’étendue Log Analytics pour la démonstration." lightbox="media/log-analytics-tutorial/scope.png":::

## <a name="view-table-information"></a>Afficher les informations de table
Le côté gauche de l’écran comprend l’onglet **Tables**, où vous pouvez inspecter les tables disponibles dans l’étendue actuelle. Ces tables sont regroupées par **Solution** par défaut, mais vous pouvez modifier leur regroupement ou les filtrer. 

Développez la solution **Gestion des journaux** et recherchez la table **AppRequests**. Vous pouvez développer la table pour afficher son schéma, ou pointer sur son nom pour afficher des informations supplémentaires à son sujet. 

:::image type="content" source="media/log-analytics-tutorial/table-details.png" alt-text="Capture d’écran montrant la vue Tables." lightbox="media/log-analytics-tutorial/table-details.png":::

Sélectionnez **Liens utiles** pour accéder à la référence des tables qui documente chaque table et ses colonnes. Sélectionnez **Aperçu des données** pour jeter un œil à quelques enregistrements récents dans la table. Cette préversion peut être utile pour s’assurer qu’il s’agit bien des données que vous attendez, avant d’exécuter une requête avec elles.

:::image type="content" source="media/log-analytics-tutorial/sample-data.png" alt-text="Capture d’écran montrant un échantillon de données." lightbox="media/log-analytics-tutorial/sample-data.png":::

## <a name="write-a-query"></a>Écrivez votre requête.
Commençons par écrire une requête à l’aide de la table **AppRequests**. Double-cliquez sur son nom pour l’ajouter à la fenêtre de requête. Vous pouvez aussi taper directement dans la fenêtre. Vous pouvez même faire en sorte qu’IntelliSense vous aide à compléter les noms des tables dans l’étendue actuelle et les commandes Kusto Query Language (KQL).

Il s’agit de la requête la plus simple que nous pouvons écrire. Elle retourne simplement tous les enregistrements d’une table. Exécutez-la en sélectionnant le bouton **Exécuter** ou en sélectionnant Maj+Entrée avec le curseur positionné n’importe où dans le texte de la requête.

:::image type="content" source="media/log-analytics-tutorial/query-results.png" alt-text="Capture d’écran montrant les résultats de la requête." lightbox="media/log-analytics-tutorial/query-results.png":::

Vous pouvez constater que nous avons des résultats. Le nombre d’enregistrements retournés par la requête s’affiche dans le coin inférieur droit. 

## <a name="filter-query-results"></a>Filtrer les résultats d’une requête

Ajoutons un filtre à la requête pour réduire le nombre d’enregistrements retournés. Sélectionnez l’onglet **Filtrer** dans le volet gauche. Cet onglet présente des colonnes dans les résultats de la requête, que vous pouvez utiliser pour filtrer les résultats. Les valeurs les plus élevées de ces colonnes sont affichées avec le nombre d’enregistrements ayant cette valeur. Sélectionnez **200** sous **ResultCode**, puis **Appliquer et exécuter**. 

:::image type="content" source="media/log-analytics-tutorial/query-pane.png" alt-text="Capture d’écran montrant le volet Requêtes." lightbox="media/log-analytics-tutorial/query-pane.png":::

Une instruction **where** est ajoutée à la requête avec la valeur que vous avez sélectionnée. Les résultats incluent désormais uniquement les enregistrements ayant cette valeur, ce qui vous permet de voir que le nombre d’enregistrements est réduit.

:::image type="content" source="media/log-analytics-tutorial/query-results-filter-01.png" alt-text="Capture d’écran montrant un filtre de résultats de requête." lightbox="media/log-analytics-tutorial/query-results-filter-01.png":::


### <a name="time-range"></a>Plage temporelle
Toutes les tables d’un espace de travail Log Analytics ont une colonne nommée **TimeGenerated** qui correspond à l’heure de création de l’enregistrement. Toutes les requêtes ont une plage de temps qui limite les résultats aux enregistrements ayant une valeur **TimeGenerated** comprise dans cette plage. La plage de temps peut être définie dans la requête ou avec le sélecteur en haut de l’écran.

Par défaut, la requête retourne les enregistrements des dernières 24 heures. Vous devriez voir un message ici indiquant que nous ne voyons pas tous les résultats. Cela est dû au fait que Log Analytics peut retourner un maximum de 30 000 enregistrements, et que notre requête en a retourné davantage. Sélectionnez la liste déroulante **Plage de temps** et choisissez la valeur **12 heures**. Sélectionnez à nouveau **Exécuter** pour retourner les résultats. 

:::image type="content" source="media/log-analytics-tutorial/query-results-max.png" alt-text="Capture d’écran qui montre l’intervalle de temps." lightbox="media/log-analytics-tutorial/query-results-max.png":::


### <a name="multiple-query-conditions"></a>Plusieurs conditions de requête
Nous allons réduire les résultats en ajoutant une autre condition de filtre. Une requête peut inclure un nombre quelconque de filtres pour cibler exactement le jeu d’enregistrements souhaité. Sélectionnez **Accéder à la page d’accueil/d’index** sous **Nom**, puis sélectionnez **Appliquer et exécuter**. 

:::image type="content" source="media/log-analytics-tutorial/query-results-filter-02.png" alt-text="Capture d’écran montrant les résultats de la requête avec plusieurs filtres." lightbox="media/log-analytics-tutorial/query-results-filter-02.png":::


## <a name="analyze-results"></a>Analyser les résultats
En plus de vous aider à écrire et à exécuter des requêtes, Log Analytics offre des fonctionnalités permettant d’exploiter les résultats. Commencez par développer un enregistrement pour afficher les valeurs de toutes ses colonnes.

:::image type="content" source="media/log-analytics-tutorial/expand-record.png" alt-text="Capture d’écran montrant le développement d’un enregistrement." lightbox="media/log-analytics-tutorial/expand-record.png":::

Sélectionnez le nom d’une colonne pour trier les résultats selon cette colonne. Sélectionnez l’icône de filtre en regard de celle-ci pour fournir une condition de filtre. Cela est similaire à l’ajout d’une condition de filtre à la requête elle-même, sauf que ce filtre est effacé si la requête est réexécutée. Appliquez cette méthode si vous souhaitez analyser rapidement un jeu d’enregistrements dans le cadre de l’analyse interactive.

Par exemple, définissez un filtre sur la colonne **DurationMs** pour limiter les enregistrements à ceux qui ont pris plus de **100** millisecondes. 

:::image type="content" source="media/log-analytics-tutorial/query-results-filter.png" alt-text="Capture d’écran montrant un filtre de résultats de requête." lightbox="media/log-analytics-tutorial/query-results-filter.png":::

Au lieu de filtrer les résultats, vous pouvez regrouper les enregistrements par une colonne particulière. Effacez le filtre que vous venez de créer, puis activez la bascule **Regrouper les colonnes**. 

:::image type="content" source="media/log-analytics-tutorial/query-results-group-columns.png" alt-text="Capture d’écran montrant l’activation du regroupement des colonnes." lightbox="media/log-analytics-tutorial/query-results-group-columns.png":::

Faites glisser la colonne **Url** sur la ligne de regroupement. Les résultats sont désormais organisés d’après cette colonne, et vous pouvez réduire chaque groupe pour vous aider dans votre analyse.

:::image type="content" source="media/log-analytics-tutorial/query-results-grouped.png" alt-text="Capture d’écran montrant les résultats de la requête groupés." lightbox="media/log-analytics-tutorial/query-results-grouped.png":::

## <a name="work-with-charts"></a>Utiliser des graphiques
Examinons une requête qui utilise des données numériques que nous pouvons afficher dans un graphique. Au lieu de créer une requête, nous allons sélectionner un exemple de requête.

Dans le volet gauche, sélectionnez **Requêtes**. Ce volet comprend des exemples de requêtes que vous pouvez ajouter à la fenêtre de requête. Si vous utilisez votre propre espace de travail, vous devriez avoir une variété de requêtes dans plusieurs catégories. Si vous utilisez l’environnement de démonstration, vous ne voyez peut-être que des espaces de travail **Log Analytics**. Développez-la pour afficher les requêtes de la catégorie.

Sélectionnez la requête appelée **Taux d’erreur de fonction** dans la catégorie **Applications**. Cette étape ajoute la requête à la fenêtre de requête. Notez que la nouvelle requête est séparée de l’autre par une ligne vide. Une requête en KQL se termine lorsqu’elle rencontre une ligne vide. Elles sont donc considérées comme des requêtes distinctes. 

:::image type="content" source="media/log-analytics-tutorial/example-query.png" alt-text="Capture d’écran montrant une nouvelle requête." lightbox="media/log-analytics-tutorial/example-query.png":::

La requête active est celle sur laquelle le curseur est positionné. Vous pouvez voir que la première requête est mise en surbrillance, indiquant qu’il s’agit de la requête active. Cliquez n’importe où dans la nouvelle requête pour la sélectionner, puis sélectionnez le bouton **Exécuter** pour l’exécuter.

:::image type="content" source="media/log-analytics-tutorial/example-query-output-table.png" alt-text="Capture d’écran montrant la table des résultats de la requête." lightbox="media/log-analytics-tutorial/example-query-output-table.png":::

Pour afficher les résultats dans un graphique, sélectionnez **Graphique** dans le volet des résultats.  Notez qu’il existe différentes options pour manipuler le graphique, par exemple pour le remplacer par un autre type.

:::image type="content" source="media/log-analytics-tutorial/example-query-output-chart.png" alt-text="Capture d’écran montrant le graphique des résultats de la requête." lightbox="media/log-analytics-tutorial/example-query-output-chart.png":::


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment utiliser Log Analytics, suivez le tutoriel sur l’utilisation des requêtes de journal :
> [!div class="nextstepaction"]
> [Écrire des requêtes de journal dans Azure Monitor](get-started-queries.md)

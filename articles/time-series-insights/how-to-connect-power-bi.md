---
title: Connecter votre environnement à Power BI – Azure Time Series Insights | Microsoft Docs
description: Découvrez comment connecter Azure Time Series Insights à Power BI pour partager, organiser sous la forme de graphique et afficher des données au sein de votre organisation.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 38403eed56dc718afdfce13375dd2662beb13eb6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100374165"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>Visualiser des données à partir d’Azure Time Series Insights dans Power BI

Azure Time Series Insights est une plateforme permettant de stocker, de gérer, d’interroger et de visualiser des données de série chronologique dans le cloud. [Power BI](https://powerbi.microsoft.com) est un outil d’analytique commercial avec des fonctionnalités de visualisation riches qui vous permet de partager des informations et des résultats au sein de votre organisation. Les deux services peuvent maintenant être intégrés, ce qui vous permet d’améliorer la puissante analytique d’Azure Time Series Insights grâce à la visualisation performante des données et aux fonctions de partage faciles de Power BI.

Vous découvrirez comment effectuer les actions suivantes :

* Connecter Azure Time Series Insights à Power BI à l’aide du connecteur Azure Time Series Insights natif
* Créer des visuels avec vos données de séries chronologiques dans Power BI
* Publier le rapport dans Power BI et le partager avec le reste de votre organisation


## <a name="prerequisites"></a>Prérequis

* Souscrivez un [abonnement Azure gratuit](https://azure.microsoft.com/free/) si vous n’en avez pas déjà un.
* Télécharger et installer la dernière version de [Power BI Desktop](https://powerbi.microsoft.com/downloads/)
* Avoir ou créer un environnement [Azure Time Series Insights Gen2](./how-to-provision-manage.md)

Consultez les [stratégies d’accès à l’environnement](./concepts-access-policies.md) et veillez à disposer d’un accès direct ou d’un accès invité à l’environnement Azure Time Series Insights Gen2. 

> [!IMPORTANT]
> * Téléchargez et installez la dernière version de [Power BI Desktop](https://powerbi.microsoft.com/downloads/). Pour suivre les étapes décrites dans cet article, vérifiez que la version de décembre 2020 (2.88.321.0) de Power BI Desktop au minimum est installée. 

## <a name="export-data-from-azure-time-series-insights-into-power-bi-desktop"></a>Exporter des données Azure Time Series Insights vers Power BI Desktop

Pour commencer :

1. Ouvrez l’Explorateur Azure Time Series Insights Gen2 et organisez vos données. Il s’agit des données qui seront exportées dans Power BI.
1. Une fois que vous avez créé un affichage dont vous êtes satisfait, accédez au menu déroulant **Plus d’actions**, puis sélectionnez **Se connecter à Power BI**.

    [![Exportation avec l’Explorateur Azure Time Series Insights Gen2](media/how-to-connect-power-bi/export-from-explorer.jpg)](media/how-to-connect-power-bi/export-from-explorer.jpg#lightbox)

1. Définissez vos paramètres pour l’exportation :

   * **Format de données** : indiquez si vous voulez exporter des **données agrégées** ou des **événements bruts** dans Power BI. 

       > [!NOTE]
       > Si vous exportez des événements bruts, vous pouvez agréger ces données ultérieurement dans Power BI. Toutefois, si vous exportez des données agrégées, vous ne pouvez pas rétablir les données brutes dans Power BI. Il existe une limite de nombre d’événements de 250 000 pour les données de niveau Événement brut.

   * **Intervalle de temps** : Indiquez si vous souhaitez afficher un intervalle de temps **fixe** ou les données **les plus récentes** dans Power BI. Si vous choisissez l’intervalle de temps fixe, les données de l’étendue de recherche que vous avez représentées sous forme de graphique sont exportées vers Power BI. Si vous choisissez l’intervalle de temps le plus récent, Power BI récupère les données les plus récentes pour l’étendue de recherche que vous avez choisie (par exemple, si vous représentez graphiquement une heure de données et que vous choisissez le paramètre « les plus récentes », le connecteur Power BI effectue toujours des requêtes pour l’heure de données la plus récente.)
  
   * **Type de magasin** : indiquez si vous souhaitez exécuter la requête sélectionnée sur un **magasin chaud** ou sur un **magasin froid**. Si vous avez sélectionné une plage qui s’étend à la fois sur un stockage froid et un stockage chaud, votre requête sera routée vers le stockage froid par défaut, car le stockage chaud contient uniquement les données les plus récentes. La modification manuelle du paramètre storeType est autorisée, mais elle n’est pas recommandée pour une expérience optimale. 

    > [!TIP] 
    > L’Explorateur Azure Time Series Insights sélectionne automatiquement les paramètres recommandés en fonction de l’étendue de recherche et des données que vous avez choisi d’exporter. 

1. Une fois que vous avez configuré vos paramètres, sélectionnez **Copier la requête dans le presse-papiers**.

    [![Boîte de dialogue d’exportation de l’Explorateur Azure Time Series Insights](media/how-to-connect-power-bi/choose-explorer-parameters.jpg)](media/how-to-connect-power-bi/choose-explorer-parameters.jpg#lightbox)

1. Démarrez Power BI Desktop.
   
1. Dans Power BI Desktop, sous l’onglet **Accueil**, sélectionnez **Obtenir les données** dans le coin supérieur droit, puis **Plus**.

    [![Obtenir des données dans Power BI](media/how-to-connect-power-bi/get-data-power-bi.jpg)](media/how-to-connect-power-bi/get-data-power-bi.jpg#lightbox)

1. Recherchez **Azure Time Series Insights**, sélectionnez **Azure Time Series Insights (bêta)** , puis **Se connecter**.

    [![Connecter Power BI à Azure Time Series Insights](media/how-to-connect-power-bi/select-tsi-connector.jpg)](media/how-to-connect-power-bi/select-tsi-connector.jpg#lightbox)

    Vous pouvez également accéder à l’onglet **Azure** et sélectionner **Azure Time Series Insights (bêta)** , puis **Connecter**.

1. Collez la requête que vous avez copiée à partir de l’Explorateur Azure Time Series Insights dans le champ **Requête personnalisée**, puis appuyez sur **OK**.

    [![Coller la requête personnalisée et sélectionner OK](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1.  La table de données sera chargée. Appuyez sur **Charger** pour charger dans Power BI. Si vous souhaitez apporter des transformations aux données, vous pouvez le faire maintenant en cliquant sur **Transformer les données**. Vous pouvez également transformer vos données une fois qu’elles ont été chargées.

    [![Passer en revue les données contenues dans la table et sélectionner Charger](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

## <a name="create-a-report-with-visuals"></a>Créer un rapport avec des visuels

Maintenant que vous avez importé les données dans Power BI, il est temps de créer un rapport avec des visuels.

1. Sur le côté gauche de la fenêtre, vérifiez que vous avez sélectionné l’affichage **Rapport**.

    [![Capture d’écran montrant l’icône d la vue Rapport.](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. Dans la colonne **Visualisations**, sélectionnez le visuel de votre choix. Par exemple, sélectionnez **Graphique en courbes**. Cela permet d’ajouter un graphique en courbes vide à votre canevas.

1.  Dans la liste **Champs**, sélectionnez **_Timestamp**, puis faites-le glisser vers le champ **Axe** pour afficher l’heure le long de l’axe X. Veillez à basculer sur **_Timestamp** pour la valeur de l’**axe** (la valeur par défaut est **Hiérarchie de date**).

    [![Sélectionner _Timestamp](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1.  À nouveau, dans la liste **Champs**, sélectionnez la variable que vous souhaitez tracer, puis faites-la glisser jusqu’au champ **Valeurs** pour afficher les valeurs le long de l’axe Y. Sélectionnez la valeur d’ID de série chronologique, puis faites-la glisser jusqu’au champ **Légende** pour créer plusieurs lignes dans le graphique, une par ID de série chronologique. Cette opération affiche une vue similaire à celle fournie par l’Explorateur Azure Time Series Insights. 

    [![Créer un graphique en courbes de base](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. Pour ajouter un autre graphique à votre canevas, sélectionnez n’importe où sur le canevas à l’extérieur du graphique en courbes, puis répétez ce processus.

    [![Créer des graphiques supplémentaires à partager](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Une fois que vous avez créé votre rapport, vous pouvez le publier sur Power BI Reporting Services et le partager avec d’autres utilisateurs de votre organisation.

## <a name="advanced-editing"></a>Modification avancée
Si vous avez déjà chargé un jeu de données dans Power BI, mais que vous souhaitez modifier la requête (par exemple, la date/heure ou les paramètres d’ID d’environnement), vous pouvez le faire via la fonctionnalité Éditeur avancé de Power BI. Reportez-vous à la [documentation Power BI](/power-bi/desktop-query-overview) pour en savoir plus sur la façon d’apporter des changements avec l’**éditeur Power Query**. 

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [Power BI Desktop](/power-bi/desktop-query-overview).

* Découvrez l’[interrogation de données](concepts-query-overview.md) dans Azure Time Series Insights Gen2.
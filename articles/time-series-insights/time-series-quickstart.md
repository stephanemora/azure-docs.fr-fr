---
title: 'Démarrage rapide : Explorateur Azure Time Series Insights | Microsoft Docs'
description: Ce démarrage rapide montre comment commencer à utiliser l’explorateur Azure Time Series Insights dans votre navigateur web pour visualiser de grands volumes de données IoT. Faites le tour des fonctionnalités clés dans un environnement de démonstration.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: be663520c9c1afd11ace57cd9dcb8ffb8219b86b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696952"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Démarrage rapide : Explorer Azure Time Series Insights

Cet explorateur faisant office de démarrage rapide vous montre comment démarrer avec Time Series Insights dans un environnement de démonstration gratuit. Vous y découvrirez comment utiliser votre navigateur web pour visualiser de grands volumes de données IoT avant de faire le tour des fonctionnalités clés actuellement en disponibilité générale.

Azure Time Series Insights est un service entièrement managé d’analyse, de stockage et de visualisation qui simplifie la découverte et l’analyse simultanées de milliards d’événements IoT. Cette solution vous donne une vue globale de vos données, vous permettant de valider rapidement votre solution IoT et d’éviter des temps morts coûteux d’appareils stratégiques. Grâce à Azure Time Series Insights, vous pouvez découvrir des tendances masquées, détecter les anomalies et effectuer des analyses de cause première quasiment en temps réel.

Pour plus de flexibilité, il est possible d’ajouter Azure Time Series Insights à des applications préexistantes via ses [API REST](./time-series-insights-update-tsq.md) et ses [Kits de développement logiciel (SDK) clients](./tutorial-create-tsi-sample-spa.md) puissants. Les API vous permettent de stocker et d’interroger des données de série chronologique et consomment des données de série chronologique dans l’application cliente de votre choix. Vous pouvez également choisir d’utiliser le kit de développement logiciel (SDK) client pour ajouter des composants d’interface utilisateur à votre application existante.

L’Explorateur Time Series Insights constitue une visite guidée des fonctionnalités actuellement en disponibilité générale.

## <a name="prepare-the-demo-environment"></a>Préparer l’environnement de démonstration

1. Créez un[compte Azure gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) si ce n’est pas déjà fait.

1. Dans votre navigateur, accédez à la [démonstration de disponibilité générale](https://insights.timeseries.azure.com/demo).

1. Si vous y êtes invité, connectez-vous à l’Explorateur Time Series Insights à l’aide de vos informations d’identification de compte Azure.

1. La page de présentation Time Series Insights s’affiche. Cliquez sur **Suivant** pour commencer la visite guidée.

   [![Cliquez sur Suivant](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>Explorer l’environnement de démonstration

1. Le **Volet de sélection de temps** s’affiche. Utilisez ce volet pour sélectionner un intervalle de temps à visualiser.

   [![Volet de sélection d’heure](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. Cliquez et faites glisser dans la région, puis cliquez sur le bouton **Rechercher**.

   [![Sélectionner un délai d’exécution](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   Time Series Insights affiche une visualisation de graphique pour l’intervalle de temps spécifié. Vous pouvez effectuer diverses actions dans le graphique en courbes, comme le filtrage, l’épinglage, le tri et l’empilement.

   Pour revenir au **volet de sélection de temps**, cliquez sur la flèche vers le bas, comme illustré :

   [![Graphique](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. Cliquez sur **Ajouter** dans le **Volet de temps** pour ajouter un nouveau terme de recherche.

   [![Ajouter un élément](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. Dans le graphique, vous pouvez sélectionner une région en cliquant avec le bouton droit de la région et en sélectionnant **Explorer les événements**.

   [![Explorer les événements](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   Une grille de vos données brutes s’affiche à partir de la région que vous explorez :

   [![Affichage de grille](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>Sélectionner et filtrer les données

1. Modifiez vos termes pour modifier les valeurs dans le graphique et ajoutez un autre terme pour effectuer une corrélation croisée des différents types de valeurs :

   [![Ajouter un terme](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. Entrez une condition de filtre dans la zone **Filtrer les séries...**  pour le filtrage improvisé de séries. Pour le démarrage rapide, saisissez **Station5** pour avoir une corrélation croisée de la température et de la pression pour cette station.

   [![Filtrer les séries](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

Après avoir terminé le démarrage rapide, vous pouvez tester l’exemple de jeu de données pour créer différentes visualisations.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes prêt à créer votre propre environnement Time Series Insights :
> [!div class="nextstepaction"]
> [Planifier votre environnement Time Series Insights](time-series-insights-environment-planning.md)

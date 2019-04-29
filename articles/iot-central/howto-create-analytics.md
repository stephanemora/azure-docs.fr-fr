---
title: Analyser les données de votre appareil dans votre application Azure IoT Central | Microsoft Docs
description: Analysez les données de votre appareil dans votre application Azure IoT Central.
author: lmasieri
ms.author: lmasieri
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 05915e2059e45c002a9dfa71a538ed6391caa157
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886477"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Guide pratique pour utiliser une analytique permettant d’analyser les données de vos appareils

*Cet article s’applique aux opérateurs, aux créateurs et aux administrateurs.*

Azure IoT Central offre des fonctionnalités d’analytique avancées permettant d’obtenir des insights à partir de grandes quantités de données provenant de vos appareils. Pour commencer, accédez à**Analytique** dans le menu de navigation de gauche.

## <a name="querying-your-data"></a>Interrogation de vos données

Vous devez choisir un **ensemble d’appareils**, ajouter un **filtre** (facultatif) et sélectionner une **période** pour commencer. Une fois que vous avez terminé, sélectionnez **afficher les résultats** pour commencer à visualiser vos données.

* **Ensembles d’appareils :** Un [ensemble d’appareils](howto-use-device-sets.md) est un groupe de vos appareils défini par l’utilisateur. Par exemple, tous les réfrigérateurs de Paris ou la totalité des éoliennes rev 2.0.

* **Filtres :** Vous pouvez ajouter des filtres à votre recherche pour affiner l’exploration de vos données. Vous pouvez ajouter jusqu’à 10 filtres à la fois. Par exemple, parmi tous les réfrigérateurs de Paris, trouvez ceux dont la température a dépassé 60 degrés.
* **Période :** Par défaut, nous allons récupérer les données des 10 dernières minutes. Vous pouvez remplacer cette valeur par un des intervalles de temps prédéfinis ou sélectionner une période personnalisée.

  ![Requête Analytics](media/howto-create-analytics/analytics-query.png)

## <a name="visualizing-your-data"></a>Visualisation des données

Une fois que vous avez interrogé vos données, vous pouvez commencer à les visualiser. Vous pouvez afficher/masquer les mesures, changer la façon dont les données sont agrégées et diviser les données en fonction des différentes propriétés de l’appareil.  

* **Fractionner par** : le fractionnement des données en fonction des propriétés de l’appareil vous permet d’explorer vos données de façon plus détaillée. Par exemple, vous pouvez diviser vos résultats par ID d’appareil ou emplacement.

* **Mesures :** vous pouvez choisir d’afficher/masquer à la fois jusqu’à 10 éléments de télémétrie différents signalés par vos appareils. Parmi les mesures figurent par exemple la température et l’humidité.

* **Agrégation :** par défaut, nous agrégeons les données selon leur valeur moyenne, mais vous pouvez choisir un autre critère d’agrégation.

   ![Visualisation de l’analytique avec division](media/howto-create-analytics/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interaction avec vos données

Vous disposez de différentes manières de modifier vos résultats de requête pour répondre à vos besoins de visualisation. Vous pouvez alternent entre une vue du graphique et un affichage de grille, effectuer un zoom avant et arrière, actualiser votre jeu de données et modifier la façon dont les lignes sont affichées.

* **Afficher la grille :** Vos résultats sont disponibles dans un format de table, ce qui vous permet d’afficher la valeur spécifique pour chaque point de données. Cette vue répond également aux standards d’accessibilité.
* **Afficher le graphique :** Vos résultats sont affichés dans un format de ligne pour vous aider à identifier vers le haut ou vers le bas tendances et anomalies.

  ![Affichage du mode grille pour votre analytique](media/howto-create-analytics/analytics-showgrid.png)

Zoom vous permet à l’accueil dans sur vos données. Si vous souhaitez vous concentrer sur une période de votre jeu de résultats, utilisez votre curseur pour délimiter la zone sur laquelle vous souhaitez effectuer un zoom, puis utilisez les commandes disponibles pour exécuter l’une des actions suivantes :

* **Zoom avant :** Une fois que vous avez sélectionné une période de temps, le zoom avant est activé et vous permet de zoomer pour vos données.
* **Zoom arrière :** ce contrôle vous permet d’effectuer un zoom arrière d’un niveau à partir de votre dernier zoom. Par exemple, si vous avez zoom dans à vos données trois fois, le zoom arrière prend vous sauvegardez une seule étape à la fois.
* **Réinitialisation du zoom :** une fois que vous avez effectué différents niveaux de zoom, vous pouvez utiliser la commande de réinitialisation du zoom pour revenir à votre jeu de résultats d’origine.

  ![Effectuer un zoom sur vos données](media/howto-create-analytics/analytics-zoom.png)

Vous pouvez changer le style de ligne en fonction de vos besoins. Vous avez quatre options :

* **Ligne :** Une ligne plate entre chacun des points de données.
* **Lisse :** Une ligne courbe entre chaque point.
* **Étape :** Ligne entre chaque point du graphique est une étape.
* **Nuage de points :** Tous les points sont tracées sur le graphique sans les lignes qui connectent les.

  ![Différents types de lignes disponibles dans Analytics](media/howto-create-analytics/analytics-linetypes.png)

Enfin, vous pouvez organiser vos données sur l’axe des ordonnées en choisissant un des trois modes :

* **Empilé :** un graphique pour chaque mesure est empilé et chacun des graphiques a son propre axe Y. Les graphiques empilés sont pratiques quand plusieurs mesures sont sélectionnées et que vous voulez une vue distincte de chacune de ces mesures.
* **Non empilé :** un graphique pour chaque mesure est tracé sur un même axe Y, mais les valeurs de cet axe varient en fonction de la mesure sélectionnée. Les graphiques non empilés sont pratiques quand vous voulez superposer plusieurs mesures et voir les modèles de comportement de ces mesures pour le même intervalle de temps.
* **Axe Y partagé :** tous les graphiques partagent le même axe Y et les valeurs de l’axe ne changent pas. Les graphiques avec axe Y partagé sont pratiques quand vous voulez examiner une seule mesure tout en segmentant les données avec Division par.

  ![Organiser les données sur l’axe Y avec différents modes de visualisation](media/howto-create-analytics/analytics-yaxis.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à créer des analytiques personnalisées pour votre application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Préparer et connecter une application Node.js](howto-connect-nodejs.md)
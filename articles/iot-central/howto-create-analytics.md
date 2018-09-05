---
title: Créer des analytiques personnalisées pour votre application Azure IoT Central | Microsoft Docs
description: En tant qu’opérateur, découvrez comment créer des analytiques personnalisées pour votre application Azure IoT Central.
author: lmasieri
ms.author: lmasieri
ms.date: 08/26/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0a78c534605b6eab08d5b12674689f0459e80b26
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247094"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Guide pratique pour utiliser une analytique permettant d’analyser les données de vos appareils

Microsoft Azure IoT Central offre des fonctionnalités d’analytique avancées pour rendre signifiantes les grandes quantités de données provenant de vos appareils. Pour commencer, accédez à**Analytique** dans le menu de navigation de gauche. 

  ![Accès à l’analytique dans IoT Central](media\howto-create-analytics\analytics-navigation.png)

## <a name="querying-your-data"></a>Interrogation de vos données

Vous devez choisir un **ensemble d’appareils**, ajouter un **filtre** (facultatif) et sélectionner une **période** pour commencer. Une fois que vous avez terminé, cliquez sur *Afficher les résultats* pour commencer à visualiser vos données.


* **Ensemble d’appareils** : un [ensemble d’appareils](howto-use-device-sets.md) est un groupe défini par l’utilisateur de vos appareils. Par exemple, tous les réfrigérateurs de Paris ou la totalité des éoliennes rev 2.0.

<!---
to-do: confirm if 10 is the max number of filters
to-do: do we need to explain how fiters work?
--->

* **Filtres** : vous pouvez ajouter des filtres à votre recherche pour affiner l’exploration de vos données. Vous pouvez ajouter jusqu’à 10 filtres à la fois. Par exemple, parmi tous les réfrigérateurs de Paris, trouvez ceux dont la température a dépassé 60 degrés. 
* **Période** : par défaut, nous allons récupérer les données des 10 dernières minutes. Vous pouvez remplacer cette valeur par un des intervalles de temps prédéfinis ou sélectionner une période personnalisée. 

 ![Requête Analytics](media\howto-create-analytics\analytics-query.png)

## <a name="visualizing-your-data"></a>Visualisation des données

Une fois que vous avez interrogé vos données, vous pouvez commencer à les visualiser. Vous pouvez afficher/masquer les mesures, changer la façon dont les données sont agrégées et diviser les données en fonction des différentes propriétés de l’appareil.  

* **Diviser par** : la division des données en fonction des propriétés de l’appareil vous permet d’explorer vos données de façon plus détaillée. Par exemple, vous pouvez diviser vos résultats par ID d’appareil ou emplacement.
<!---
to-do: confirm if 10 is the max number of measurements
--->
* **Mesures** : vous pouvez choisir d’afficher/de masquer jusqu’à 10 éléments de télémétrie différents signalés par vos appareils à la fois. Parmi les mesures figurent par exemple la température et l’humidité. 
* **Agrégation :** par défaut, nous agrégeons les données par leur moyenne, mais vous pouvez choisir un autre critère d’agrégation. 

   ![Visualisation de l’analytique](media\howto-create-analytics\analytics-visualize.png) <br/><br/>
   ![Visualisation de l’analytique avec division](media\howto-create-analytics\analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interaction avec vos données

Vous pouvez affiner l’affichage des résultats des requêtes de différentes façons en fonction de vos besoins. Vous pouvez basculer entre un mode graphique et un mode grille, effectuer un zoom avant/arrière, actualiser votre jeu de données et modifier la façon dont les lignes sont affichées.

* **Afficher la grille** : vos résultats sont disponibles sous forme de tableau afin que vous puissiez voir la valeur spécifique de chaque point de données. Cette vue répond également aux standards d’accessibilité. 
* **Afficher le graphique** : vos résultats s’affichent sous forme de lignes afin de souligner les tendances à la hausse ou à la baisse et les anomalies. 

 ![Affichage du mode grille pour votre analytique](media\howto-create-analytics\analytics-showgrid.png)

Le zoom vous permet d’explorer vos données. Si vous souhaitez vous concentrer sur une période au sein de votre jeu de résultats, utilisez votre curseur pour délimiter la zone sur laquelle vous souhaitez effectuer un zoom, puis utilisez les commandes disponibles pour exécuter l’une des actions suivantes :
* **Zoom avant** : une fois que vous avez sélectionné une période, le zoom avant est activé, et vous pouvez donc l’utiliser sur vos données.
* **Zoom arrière** : cette commande vous permet d’effectuer un zoom arrière d’un niveau à partir de votre dernier zoom. Par exemple, si vous avez effectué un triple zoom avant sur vos données, vous devrez effectuer trois zooms arrière pour retrouver l’affichage initial.
* **Réinitialisation du zoom** : une fois que vous avez effectué différents niveaux de zoom, vous pouvez utiliser la commande de réinitialisation du zoom pour revenir à votre jeu de résultats d’origine. 

 ![Effectuer un zoom sur vos données](media\howto-create-analytics\analytics-zoom.png)


Vous pouvez changer le style de ligne en fonction de vos besoins. Vous disposez de quatre options :
* **Ligne** : une ligne plate est formée entre chaque point de données. 
* **Lissée** : une ligne courbe est formée entre chaque point.
* **Étape** : une ligne entre chaque point sur le graphique crée un graphique d’étapes.
* **Nuage de points** : tous les points sont tracés sur le graphique sans être reliés par des lignes. 

 ![Différents types de lignes disponibles dans Analytics](media\howto-create-analytics\analytics-linetypes.png)

Enfin, vous pouvez organiser vos données sur l’axe Y en choisissant parmi les trois modes suivants :

* **Empilé :** un graphique pour chaque mesure est empilé et chacun des graphiques a son propre axe Y. Les graphiques empilés sont pratiques quand plusieurs mesures sont sélectionnées et que vous voulez une vue distincte de chacune de ces mesures.
* **Non empilé :** un graphique pour chaque mesure est tracé sur un même axe Y, mais les valeurs de cet axe varient en fonction de la mesure sélectionnée. Les graphiques non empilés sont pratiques quand vous voulez superposer plusieurs mesures et voir les modèles de comportement de ces mesures pour le même intervalle de temps.
* **Axe Y partagé :** tous les graphiques partagent le même axe Y et les valeurs de l’axe ne changent pas. Les graphiques avec axe Y partagé sont pratiques quand vous voulez examiner une seule mesure tout en segmentant les données avec Division par.

 ![Organiser les données sur l’axe Y avec différents modes de visualisation](media\howto-create-analytics\analytics-yaxis.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à créer des analytiques personnalisées pour votre application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Préparer et connecter une application Node.js](howto-connect-nodejs.md)
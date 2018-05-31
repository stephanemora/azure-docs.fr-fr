---
title: Créer des analytiques personnalisées pour votre application Azure IoT Central | Microsoft Docs
description: En tant qu’opérateur, découvrez comment créer des analytiques personnalisées pour votre application Azure IoT Central.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 2f19998429daab9000e35b520673c417b4bc828a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199933"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Guide pratique pour utiliser une analytique permettant d’analyser les données de vos appareils

Microsoft Azure IoT Central offre des fonctionnalités d’analytique avancées pour rendre signifiantes les grandes quantités de données provenant de vos appareils. Vous pouvez utiliser une analytique pour voir et analyser les données d’un [ensemble d’appareils](howto-use-device-sets.md) dans votre application. Un ensemble d’appareils est un groupe défini par l’utilisateur de vos appareils. Vous pouvez limiter votre analyse à un petit ensemble d’appareils ou à un seul appareil.

En tant qu’opérateur, choisissez **Analytique** dans le menu de navigation gauche, choisissez un ensemble d’appareils, puis choisissez les mesures à afficher sur le graphique. Voici quelques outils que vous pouvez utiliser pour segmenter davantage les données :

* **Mesures :** choisissez les mesures à afficher, comme la température et l’humidité.
* **Agrégation :** choisissez la fonction d’agrégation pour les mesures. Par exemple, **Minimum** ou **Moyenne**.
* **Division par :** descendez dans la hiérarchie en divisant les données par propriété ou nom d’appareil. Par exemple, divisez-les par emplacement d’appareil :

     ![Page principale de l’analytique](media\howto-create-analytics\analytics-main.png)

* **Intervalle de temps :** vous pouvez choisir un intervalle de temps parmi ceux qui sont prédéfinis ou créer un intervalle de temps personnalisé : ![Intervalle de temps d’analytique](media\howto-create-analytics\analytics-time-range.png)

## <a name="change-the-visualizations"></a>Changer les visualisations

Vous pouvez adapter les graphiques selon vos besoins de visualisation en choisissant un des trois modes disponibles :

* **Empilé :** un graphique pour chaque mesure est empilé et chacun des graphiques a son propre axe Y. Les graphiques empilés sont pratiques quand plusieurs mesures sont sélectionnées et que vous voulez une vue distincte de chacune de ces mesures.
* **Non empilé :** un graphique pour chaque mesure est tracé sur un même axe Y, mais les valeurs de cet axe varient en fonction de la mesure sélectionnée. Les graphiques non empilés sont pratiques quand vous voulez superposer plusieurs mesures et voir les modèles de comportement de ces mesures pour le même intervalle de temps.
* **Axe Y partagé :** tous les graphiques partagent le même axe Y et les valeurs de l’axe ne changent pas. Les graphiques avec axe Y partagé sont pratiques quand vous voulez examiner une seule mesure tout en segmentant les données avec Division par.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à créer des analytiques personnalisées pour votre application Azure IoT Central, voici l’étape suivante suggérée :

> [!div class="nextstepaction"]
> [Préparer et connecter une application Node.js](howto-connect-nodejs.md)